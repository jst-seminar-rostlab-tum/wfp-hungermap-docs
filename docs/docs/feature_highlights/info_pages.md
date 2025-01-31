import ReactPlayer from 'react-player';

# Info pages

Author: `Jakob Schödl`

To help users understand what the HungerMap is and how it works, it contains
multiple [Info Pages](/docs/frontend/info_pages). Those go into detail on the used data sources and technologies. The
same information is also accessible using [tooltips](/docs/frontend/tooltip) throughout the map.

We worked on making this information more accessible by summarizing the explanations and improving the way they are
shown to the user.

<ReactPlayer
url="/videos/info_pages.mp4"
controls={true}
width="100%"
height="auto"
/>

## Content

* **About page:** General information on the HungerMap in a Q&A-Format
* **Data sources page:** Data types used for the HungerMap together with their source and update interval
* **Wiki page:** Detailed explanation on some of the data sources
* **Disclaimer:** Notes on ongoing disputes over political borders

## Features

* [Search feature](/docs/frontend/info_pages#search-handling) for _About_, _Data Sources_ and _Wiki_ pages
    * Filters the page contents to matches
    * Highlights the match keywords
    * Usage of query parameters, i.e. the current search is appended to the URL
* Revised explanations
    * Summaries of all data sources
    * Simplification of overcomplicated texts (in obvious cases)
    * Important abbreviations show the long form when hovered
* Clean and responsive design
  * Visual grouping of information using [accordions](/docs/frontend/accordions)
* All pages and tooltips are auto-generated from a format similar to JSON
    * No deep knowledge needed for editing the info pages
    * Everything is stored at one spot
    * Explanations throughout the map stay consistent

## Comparison of the HungerMap Versions

| Feature                                                     | Old HungerMap (v1)                                                  | New HungerMap (v2)                                                                                                                                                             |
|:------------------------------------------------------------|:--------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| List of data sources (including update interval and source) | displayed on the _Glossary_ page (single large table with headings) | displayed on the _Data Sources_ page (accordion with one table per category)                                                                                                   |
| Explanations for data sources                               | included in the _Glossary_ page                                     | _Data Sources_ page with summaries, _Wiki_ page with detailed descriptions (Modifications to the text to make it more readable in obvious cases)                               |
| General information on the HungerMap                        | displayed on the _Methodology_ page (single text with subheadings)  | Displayed on the _About_ page (Sections with accordions, Q&A Style)                                                                                                            |
| Search                                                      | Basic search on the _Glossary_ page (as filter for the table rows)  | Search on _About_, _Data Sources_ and _Wiki_ page (filters content to show matching sections only and highlights the results on the page), query parameter for the search text |
| Info tooltips throughout the map                            | Contain the explanations for all data sources (single long text)    | Start with a summary of the data source and continue with the detailed explanation                                                                                             |
| User Experience                                             | Dark mode only; unhandy, but usable on mobile devices               | Dark and Light mode; optimized for mobile devices                                                                                                                              |

