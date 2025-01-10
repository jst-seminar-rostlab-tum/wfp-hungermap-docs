---
sidebar_position: 5
---

# Accordions

The accordions in our application, sourced from NextUI, are designed to expand and reveal additional information or collapse to hide it. These accordions have been customized to align with our specific requirements. They are utilized in the following sections of the website: 

- Comparison Portal
- Data Sources
- Country view of three types of map `FCS`, `Nutrition` and `IPC`. 
- About 
- Download Portal


#### Desktop Version
In Fcs country view, accordions are displayed as in the image. 3 Accordions are placed in the left and 4 accordions are placed to the right.

![Accordion Fcs](./assets/AccordionsFcs.png)


#### Mobile Version
Depend upon the dimension of the mobile either there will be button with an upward arrow or button with an upward arrow labeled as "Insights" displayed.

![Accordion Mobile](./assets/AccordionFcsMobile.png)

![Accordion Mobile](./assets/AccordionFcsMobileXS.png)

![Accordion Open](./assets/AccordionFcsOpen.png)

Regardless of the platform (desktop or mobile), each accordion includes an informational popover at the end for additional information.

---

#### Implementation
In `AccordionModal.tsx`, AccordionModal() function is designed to render a model with dynamic content on different sizes of mobile screens. Depends upon the screen size of the mobile, if we have small screens **450px or less**, a circular button with an arrow up icon is displayed, while for slightly larger screens **700px or less** a button with an arrow up icon and label named ***Insights*** is shown. Clicking the button opens a modal containing content provided by the `useAccordionModal()` context. The modal includes a title and body fetched from this context and it allows scrolling inside if the content overflows and provides blurred background, enhancing the focus on the modal content. The modal's visibility is managed by the `useDisclosure()` hook.

`AccordionListItems.tsx` defines AccordionListItems() function, which is used to render a list of accordion items, primarily designed for the mobile screens. It enables multiple selection modes, disable selection, expand all items or highlight specific words in the titles. Each accordion item can include tooltips, popovers, descriptions etc. When only one item is provided, it expands by default, otherwise items remain collapsed unless interacted with. The component integrates spinners for loading states and uses a highlighter library to emphasize specific words in item titles. It relies on utility functions, such as `AccordionOperations.getSelectionModeType`, to determine the selection behavior. The parameter of `AccordionListItems()` is defined as following :
```js
export default function AccordionListItems({
  items,
  loading = false,
  multipleSelectionMode = false,
  noSelectionMode = false,
  expandAll = false,
  highlightedTitleWords = [],
}: AccordionContainerProps)
```

whereas items are of type AccordionItemProps[] which represents the whole content of the accordions and it is defined as following:
```js
export interface AccordionItemProps {
  title: string;
  infoIcon?: ReactNode;
  tooltipInfo?: string;
  popoverInfo?: ReactNode;
  description?: ReactNode;
  content?: string | ReactElement;
  hideIndicator?: boolean;
  containedWords?: string;
}

export interface SearchableAccordionItemProps extends AccordionItemProps {
  containedWords: string;
}
```

The AccordionBoxItems() function in `AccordionBoxItems.tsx`renders a set of collapsible accordion items stacked vertically for desktop version, with customization options for appearance, behavior and content. It allows single or multiple items to be expanded at once, supports word highlighting in titles and includes optional tooltips, popovers and loading spinners. The expandAll prop can automatically expand all items and a ReadMore Component manages lengthy popover content. The parameters of `AccordionBoxItems()` are defined as following: 

```js
export default function AccordionBoxItems({
  items,
  title,
  loading = false,
  multipleSelectionMode = false,
  noSelectionMode = false,
  color = 'bg-content1',
  maxWidth,
  expandAll = false,
  highlightedTitleWords = [],
}: AccordionContainerProps)
```


`AccordionContainer.tsx` responsible for displaying accordion items adaptively for both mobile and desktop screens. For mobile, it uses a modal (triggered by a custom bottom button) to show the items, leveraging the `useAccordionModal` hook to manage modal content and title. For desktop version, it directly renders the accordions by packing inside `<AccordionBoxItems>` and for mobile version accordions are packed inside `<AccordionListItems>`. Basically, using AccordionContainer to call accordions operations.

```js
export default function AccordionContainer({
  items,
  title,
  loading = false,
  multipleSelectionMode = false,
  noSelectionMode = false,
  color = 'bg-content1',
  accordionModalActive,
  maxWidth,
  expandAll = false,
  highlightedTitleWords = [],
}: AccordionContainerProps)
```
---

**Accordion Content**

The content of accordions could be string or ReactElement. For instance in our use case, map layers like `FCS` and `IPC` we are giving Card (which is nextui component) or charts (for example line chart) as content. 

In `Card.tsx`, CustomCard react component is structured to accept a title and content as props, with content consisting of an array of `CardContent[]`. We have two scenarios for how the cards are structured. In the first scenario, the SVG image is displayed on the left side of the card, followed by the card's title on the right, with numerical values appearing below the title. The first scenario is depicted with the help of below image and the card is named as ***Population*** In the second scenario, used for representing changes in food consumption trends, the design includes two more SVG image to the left. The additional two SVGs are placed in adjacent to each other and below these SVG images, the numerical value is displayed and underneath that, a small text indicates the age of the data such as "1 month ago" or "3 months ago". This particular card is shown in below image and it is named as ***Population with Insufficient Food Consumption*** The parameters of Card are as following: 

```js
export interface CardContent {
  imageSrc?: string;
  svgIcon?: React.ReactNode;
  text?: React.ReactNode;
  value?: string | number;
  content?: React.ReactNode;
  timeText?: string;
  altText?: string;
  changeValues?: {
    imageSrc: string;
    text: string;
    timeText: string;
    altText: string;
  }[];
  textClass?: string;
}

```

![Accordion Card](./assets/AccordionCard.png)


---

#### Example

In this particular we have card as the content of the accordion. Accordions will get wrapped inside AccordionContainer, after that in accordionContainer, depends on if it's mobile version then accordions get wrapped inside AccordionListItems or if it's desktop version then accordions get wrapped inside AccordionBoxItems.

```js

<AccordionContainer
    loading={loading}
    title={countryName}
    accordionModalActive
    maxWidth={600}
    items={[...foodSecurityAccordionItems, ...macroEconomicAccordionItems]}
/>
```

here in our case if the content of accordion is card then the items parameter of the AccordionContainer are set like that 

```js
        title: 'Nutrition',
        infoIcon: <CustomInfoCircle />,
        popoverInfo: FcsAccordionOperations.getFoodSecutriyPopoverInfo(),
        content:
          nutritionData && (nutritionData.Acute != null || nutritionData.Chronic != null) ? (
            {nutritionData.Acute != null && (
              <CustomCard
                title="Acute Nutrition"
                content={[
                 {
                  svgIcon: <Nutrition className="w-[50px] h-[50px] object-contain" />,
                    text: (
                      <NutritionAccordionText
                        nutritionValue={`${nutritionData.Acute} %`}
                        text="of children (under 5)"
                      />
                      ),
                    },
                  ]}
                />
              )}
          )
```
Same thing happen if the content is a chart then we just have to call same like Card but instead of CustomCard we need to write the name of the chart for example like this `<LineChart>`.
















