# Comparison Portal

**Authors:** `Bohdan Garchu`, `Jakob Schödl`

## Functionality

Use the tabs at the top of the Comparison Portal to toggle between two comparison modes:

* **Country Comparison:** detailed comparison of up to 5 countries against each other
* **Region Comparison:** comparison of FCS and rCSI values between some or all of the regions of a single country

Both tabs consist of a [Select](https://nextui.org/docs/components/select) component and
an [Accordion](/docs/frontend/accordions). The select component allows users to select the countries and regions that
should be compared. When a country is selected the accordion gets updated with corresponding information. While
updating, every accordion item shows a loading spinner. An accordion item can be expanded to show more detailed
information.

:::info
Only countries where FCS data is available are shown in the list. Comparisons of other countries are not supported.
:::
![Comparison Portal](/img/comparison_portal/loading.png)

### Bar charts

Categorical data like population, people with insufficient food consumption, and import dependency is displayed using a
bar chart.

![Comparison Portal Bar Chart](/img/comparison_portal/bar_chart.png)

### Line charts

If comparison data is continuous, it is plotted on a line chart. Areas with lighter colors denote error margins of error
as given in the underlying data.
:::info
If more than 5 regions are compared, error margins are hidden as they would likely make the chart look crowded,
affecting the comprehensibility.
:::

![Comparison Portal Line Chart](/img/comparison_portal/line_chart.png)


## Error Handling

If an error occurs while fetching data for a country, a snackbar is displayed. Then the country is disabled in the
selection dropdown.

![Comparison Portal Snackbar](/img/comparison_portal/algeria.png)

In addition, if a country or region has no data for a chart, a corresponding alert is displayed.

![Comparison Portal Alert](/img/comparison_portal/alert.png)

If data for all countries or regions for a chart is missing, an error message is displayed on that chart.

![Comparison Portal Error Message](/img/comparison_portal/chart_error_message.png)

## Technical Details

### Data Fetching (Country Comparison)

To enable selection only from countries that have FCS data, the page needs to fetch global FCS data from
`/adm0/fcs.json`. In addition, country map data is fetched from `/adm0data.json` so that there is a `adm0_id` and `iso3`
code for every country. Both of these calls are executed on the server side in order to cache the data and avoid
fetching it on every request. While the data is being fetched, a skeleton is displayed. This is implemented using Reacts
`Suspense` and `Skeleton` component from NextUI.

![Comparison Portal Skeleton](/img/comparison_portal/skeleton.png)

In order to get data for a selected country, the page needs to fetch data from two endpoints. To utilize client-side
caching, respective **custom hooks** are used, located in the `countryHooks.ts` file:

* `useCountryDataListQuery`: fetches `/adm0/${countryId}/countryData.json`
* `useCountryIso3DataListQuery`: fetches `/iso3/${countryCode}/countryIso3Data.json`

They accept an array of country IDs and country codes respectively and return the data for each
country. In addition, they accept a callback function for the case when an error is returned for a specific country. The
hooks are implemented using [useQueries](https://tanstack.com/query/latest/docs/framework/react/reference/useQueries)
hook from Tanstack Query library. It allows to execute a **variable number of queries in parallel** and return the
results
in an array.

### Data Fetching (Region Comparison)

After selection of a country for region comparison, the `useRegionDataQuery` hook is used to fetch additional country
data from `/adm0/${countryId}/adm1data.json`, which includes information on a single countries' regions (including e.g.
historic FCS data). During the fetch, a Skeleton is shown.

:::note
When opening a link to the Comparison portal where regions are specified, the skeleton might show an additional accordion item. This is to avoid layout shifts in case a warning as below will be shown above the other items.

![Warning message if too many regions are selected](/img/comparison_portal/warning.png)
:::

Similar to above, the implementation is based on the Tanstack Query library. When changing the region selection, no further fetch is necessary.

### Query parameters

Selected countries, regions and the current tab are stored in the URL query string so that our users can share the
comparison with others. Here is an example of a URL containing both country and region
selections: https://wfp-hungermap.netlify.app/comparison-portal?tab=country&regionComparisonCountry=43&regions=all&countries=23%2C29

| Parameter                 | Example                                                 | Explanation                                                          |
|:--------------------------|:--------------------------------------------------------|:---------------------------------------------------------------------|
| `tab`                     | `country`, `region`                                     | Viewed comparison type                                               |
| `countries`               | `31,29,35` (represented as `31%2C29%2C35`)              | Selected country IDs for country comparison, comma-seperated (`%2C`) |
| `regionComparisonCountry` | `31`                                                    | Selected country ID for region comparison                            |
| `regions`                 | `624,622,628` (represented as `624%2C622%2C628`), `all` | Selected region IDs for region comparison, comma-seperated (`%2C`)   |

In order to **keep the selections on the page in sync** with query parameters, custom hooks for each of them were
implemented in `queryParamsHooks.ts`.
Those return the currently selected value (either using the UI or using query params) and a function to update them (in
both UI and query), similar to the array `[value, setValue]`
returned by an ordinary `useState()` call.

:::note
Updating query params takes some time. Because of that, the implemented hooks pair them with a state value that can get
updated instantly.
:::

### Limitations

The number of compared countries is limited to five. In order to increase this limit, the `COUNTRY_LIMIT` constant can
be updated in `CountrySelection.tsx`.

Region comparison only supports regions within the same country by now. The number of compared regions is not limited,
although performance and comprehensibility of the charts might suffer for larger selection. If 30 or more regions are
selected, a warning is shown.
:::note
At time of writing, [Uganda](https://wfp-hungermap.netlify.app/comparison-portal?tab=region&regionComparisonCountry=253&regions=all) is the country with the highest number of regions (112).
:::
