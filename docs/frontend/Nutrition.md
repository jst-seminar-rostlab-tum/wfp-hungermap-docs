### Nutrition Map

Nutrition map gives the information of Nutrition in the world as well as country level. It helps to visualize and retrieve the information risk of inadequate intake of micronutrients at country level.

---

#### World View 

In world view, nutrition map renders nutrition layer along with point gradient and hover effect. 

***Technical Overview***

The nutrition value of world view is fetched using the hook ```useNutritionQuery``` which is implemented in ```globalHooks.ts```. The hook is a custom wrapper around React Query's useQuery for fetching and caching nutritional data. It accepts an enabled boolean parameter to control whether the query is active. The endpoint ```globalRepo.getNutritionData``` is used for fetching the global level nutrition data.

```js 
export const useNutritionQuery = (enabled: boolean) =>
  useQuery<CountryNutrition>(
    {
      queryKey: ['fetchNutritionData'],
      queryFn: globalRepo.getNutritionData,
      enabled,
    },
    cachedQueryClient
  );
``` 
The code snippet which triggers Nutrition Map:

```js
 {selectedMapType === GlobalInsight.NUTRITION &&
    countries.features.map((country) => (
      <NutritionChoropleth
        countryId={country.properties.adm0_id}
        data={{ type: 'FeatureCollection', features: [country as Feature<Geometry, CountryProps>] }}
        setRegionLabelTooltips={setRegionLabelTooltips}
        onDataUnavailable={onDataUnavailable}
        />
    ))}
```

```NutritionChoropleth.tsx``` creates a global view by rendering the NutritionChoropleth for every country. The NutritionChoropleth function renders a nutrition-focused interactive choropleth map using react-leaflet and GeoJSON data. It dynamically displays country or pass region-level features to ```NutritionStateChoropleth``` if the user selects the country, applying custom styles based on selected micronutrient data. Main props need by NutritionChoropleth are : 

- ```data```:	GeoJSON data for rendering the map layers.
- ```countryId```: ID of the country represented by this instance of the component.
-  ```setRegionLabelTooltips```: Function to set the region label tooltips.
-  ```onDataUnavailable```: A callback to signal to the parent component that there's no regional Nutrition data for this    country. 

 Along with nutrition information, the point legend will also appear. For the world view, the legends help to see whether the data is actual, predicted or not analyzed. Apart from point legend, there is also hover effect activated.

![Nutrition World](/img/Nutrition/NutritionWorldView.png)

---

#### Country View

The Country view of nutrition map visualizes the Micronutrients information, along with customized tooltip, accordion and gradient legend.

***Technical Overview***

The NutritionStateChoropleth component in ```NutritionStateChoropleth.tsx``` provides an interactive map visualization for region-level nutrition data within a selected country.  Country level nutrition data get extracted with the help of this endpoint ```countryRepository.getRegionNutritionData(selectedCountryData.properties.adm0_id)```. It uses react-leaflet's GeoJSON component to render map layers dynamically styled and updated based on the user's selected nutrient type.
NutritionStateChoropleth component get rendered with the following props: 

- ```setRegionLabelTooltips```: Function to set the region label tooltips.
- ```countryMapData```: The GeoJSON data of the region.
- ```onDataUnavailable```: A callback to signal to the parent component that there's no regional Nutrition data for this country
- ```selectedNutrient```: The selected micronutrient


``` js
<NutritionStateChoropleth
    onDataUnavailable={onDataUnavailable}
    setRegionLabelTooltips={setRegionLabelTooltips}
    countryMapData={data}
    selectedNutrient={selectedNutrient}
/>
```

To further improve the user experience, a loading state has been enabled in this view. Once the region data becomes available, the loading state will be dismissed. 
``` js
 <CountryLoadingLayer countryMapData={countryMapData} color="hsl(var(--nextui-nutritionAnimation))" />
 ```

The NutritionAccordion in ```NutritionAccordion.tsx``` enables users to select different micronutrients, driving visual and informational updates across the map. Per default it will show the region map of ***Mean Adequacy Ratio***. User can select from 7 different micronutrients. These are Folate, Iron, Zinc, Vitamin A and Vitamin B12. The Nutrition Accordion is triggered as follows: :

```js
    <NutritionAccordion
      setSelectedNutrient={setSelectedNutrient}
      selectedNutrient={selectedNutrient}
      countryName={countryData.adm0_name}
    >
```
Gradient legend depicts the information regarding the ```Risk of Inadequate Micronutrient Intake```. 

![Nutrition Region](/img/Nutrition/NutritionRegion.png)

In the country view of the nutrition, user experience hover effect when move mouse over the region. When hover over the region, the tooltip appear with content name of the region, current selected micronutrient and the value of the micronutrient.

![Nutrition Tooltip](/img/Nutrition/NutritionTooltip.png)


