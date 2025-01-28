import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Info pages

**Author:** `Jakob Schödl`

> If you want to add a new info page, follow [this guide](/docs/how_to/how_to_add_info_page).

:::note
All pages that are reachable from the sidebar (except for the home page) are _info pages_. They differ from the map page
as follows:

* Instead of a sidebar, a **topbar** (desktop) or a **hamburger menu** (mobile) without the map layers and filters is
  displayed
* The content has a **maximum width** (and is centered)
* Textual content is searchable (optional)
  :::

## Related files

The content of a page `/yourInfoPage` should be stored at the following places:

* `/src/app/yourInfoPage`
    * `layout.tsx`: Metadata and layout of the page (includes the navigation bar and the chat bot button)
    * `page.tsx`: The actual page content
* `/src/domain/constant/yourInfoPage`:
    * `yourTextElements.tsx`: Searchable text paragraphs of the page
    * `yourAccordionItems.tsx`: Searchable accordion items of the page

## Search handling

> If you want to add search or additional searchable content to a page, see [this guide](/docs/how_to/how_to_add_info_page#adding-search).

```tsx title="At the top of the file"
'use client'
```

Searchable pages need the `useState` hook to keep track of the current search. That's only supported for client
components.

```tsx
import { Suspense, useState } from 'react';

import DocsSearchBar from '@/components/Search/DocsSearchBar';
import DocsSearchBarSkeleton from '@/components/Search/DocsSearchBarSkeleton';
import SearchableSection from '@/components/Search/SearchableSection';

function Page() {
  const [searchWords, setSearchWords] = useState<string[]>([]);
  // ...

  return (
    <>
      {/* ... */}
      <Suspense fallback={<DocsSearchBarSkeleton/>}>
        <DocsSearchBar setSearchWords={setSearchWords}/>
      </Suspense>
      {!searchWords.length && <h1 className="!mb-0">Demo page title</h1>}
      <SearchableSection
        searchWords={searchWords}
        {/* ... */}
      />
      {/* ... */}
    </>
  );
}
```

`searchWords` is an array of lower-case search words that can be used to highlight matching words, e.g. using
`SearchableSection`. Its length is 0 only if there is no ongoing search.

The `DocsSearchBar` already takes care of synchronizing those words with the query parameter `?search=...`. That
requires
a [Suspense boundary](https://nextjs.org/docs/app/building-your-application/routing/loading-ui-and-streaming#example)
around the component to avoid build errors (and improve user experience).

## Usage of `onVisibilityChange`

It is desirable to show a hint like "No results" instead of just a blank screen if a search matches no content. Supposed
all of the searchable content is stored within `SearchableSection` components, this requires to
check if all of these are currently hidden. We get this information by passing a handler function to the
`onVisibilityChange` prop of each `SearchableSection`, which gets called with respective boolean values.

<Tabs>
<TabItem value="single" label="single SearchableSection">
If there is only one `SearchableSection`, a single `useState` hook is sufficient.

```tsx
function Page() {
  const [searchWords, setSearchWords] = useState<string[]>([]);
  const [sectionIsVisible, setSectionIsVisible] = useState(true);
  // ...

  return (
    <>
      <SearchableSection
        // highlight-next-line
        onVisibilityChange={setSectionIsVisible}
        {/* ... */}
      />
      {!sectionIsVisible && !!searchWords.length && <p className="text-center">No results</p>}
    </>
  );
}
```

</TabItem>
<TabItem value="multiple" label="multiple SearchableSections">
If there are multiple `SearchableSection`s, assign a key to each of them and maintain a `Set` of the keys of currently
visible sections.

```tsx
function Page() {
  const [visibleSections, setVisibleSections] = useState<Set<string>>(new Set());
  const [searchWords, setSearchWords] = useState<string[]>([]);

  const handleVisibilityChange = useCallback(
    (key: string, isVisible: boolean) =>
      setVisibleSections((prevState) => {
        if (isVisible) prevState.add(key);
        else prevState.delete(key);
        return prevState;
      }),
    []
  );

  // ...

  return (
    <>
      {/* ... */}
      <SearchableSection
        // highlight-next-line
        onVisibilityChange={(isVisible) => handleVisibilityChange('key1', isVisible)}
        {/* ... */}
      />
      <SearchableSection
        // highlight-next-line
        onVisibilityChange={(isVisible) => handleVisibilityChange('key2', isVisible)}
        {/* ... */}
      />
      {!visibleSections.size && !!searchWords.length && <p className="text-center">No results</p>}
    </>
  );
}
```

</TabItem>
</Tabs>

