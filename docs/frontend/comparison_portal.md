# Comparison Portal

**Comparison Portal** allows users to compare two to five countries based on various indicators such as food security, import dependency, balance of trade, food and headline inflation. 

## Functionality

Comparison Portal consists of a [select](https://nextui.org/docs/components/select) component and an accordion. It component allows users to select up to five countries from all countries that have fcs data. When a country is selected the accordion gets updated with corresponding information. While updating, every accordion item shows a loading spinner. 
![Comparison Portal](/img/comparison_portal_closed.png)

Every accordion item can be expanded to show more detailed information. Categorical data like population, people with insufficient food consumption, and import dependency is displayed using a bar chart.

![Comparison Portal Bar Chart](/img/comparison_portal_bar_chart.png)

If comparison data is continuous, it is plotted on a line chart. 

![Comparison Portal Line Chart](/img/comparison_portal_line_chart.png)

In case there is no data for a specific chart, an alert is displayed.

![Comparison Portal Alert](/img/comparison_portal_alert.png)

In addition, there are certain countries that might have fcs data but our api does not have data for them. In this case, a snackbar is displayed and a country becomes disabled in the selection dropdown.

![Comparison Portal Snackbar](/img/comparison_portal_algeria.png)

## Technical Details

To enable selection only from countries that have fcs data, the page needs to fetch global fcs data from `/adm0/fcs.json`. In addition, country map data is fetched from `/adm0data.json` so that there is a adm0_id and iso3 code for every country. Both of these calls are executed on the server side in order to cache the data and avoid fetching it on every request. While the data is being fetched, a skeleton is displayed for select and accordion components.

In order to fetch data for a selected country, the page needs to fetch data from two endpoints: `/adm0/${countryId}/countryData.json` and `/iso3/${countryCode}/countryIso3Data.json`. To utilize client-side caching, two custom hooks are used: `useCountryDataListQuery` and `useCountryIso3DataListQuery`. They accept and array of country ids and country codes respectively and return the data for each country. In addition, they accept a callback function for the case when an error is returned for a specific country. The hooks are implemented using (useQueries)[https://tanstack.com/query/latest/docs/framework/react/reference/useQueries] hook from Tanstack Query library. It allows to execute a variable number of queries in parallel and return the results in an array.

```typescript
export const useCountryDataListQuery = (
  countryIds: number[] | undefined,
  onCountryDataNotFound: (countryId: number) => void
) =>
    useQueries<number[], UseQueryResult<CountryDataRecord | null>[]>(
        {
            queries: (countryIds ?? []).map((countryId) => ({
                queryKey: ['fetchCountryData', countryId],
                queryFn: async () => {
                    const countryData = await countryRepo.getCountryData(countryId);
                    if (isApiError(countryData)) {
                        onCountryDataNotFound(countryId);
                        return null;
                    }
                    return { ...countryData, id: countryId };
                },
            })),
        },
        cachedQueryClient
    );
```

```typescript
export const useCountryIso3DataListQuery = (
  countryCodes: string[] | undefined,
  onCountryDataNotFound: (countryCode: string) => void
) =>
    useQueries<string[], UseQueryResult<CountryIso3DataRecord | null>[]>(
        {
            queries: (countryCodes ?? []).map((countryCode) => ({
                queryKey: ['fetchCountryIso3Data', countryCode],
                queryFn: async () => {
                    const countryIso3Data = await countryRepo.getCountryIso3Data(countryCode);
                    if (isApiError(countryIso3Data)) {
                        onCountryDataNotFound(countryCode);
                        return null;
                    }
                    return { ...countryIso3Data, id: countryCode };
                },
            })),
        },
        cachedQueryClient
    );
```

Selected countries are stored in the url query string so that the user can share the comparison with others. In order to update the state of the page in sync with query parameters, a custom hook was implemented. It returns the current selected countries and a function to update them. The function updates both the state and the query string with the new value.

```typescript
export const useSelectedCountries = (countryMapData: CountryMapDataWrapper) => {
    const PARAM_NAME = 'countries';

    const router = useRouter();
    const pathname = usePathname();
    const searchParams = useSearchParams();
    const [selectedCountries, setSelectedCountries] = useState<CountryMapData[] | undefined>(undefined);

    // get state values from query params
    useEffect(() => {
        const searchParamCountryCodes = searchParams.get(PARAM_NAME)?.split(',') ?? [];
        const newSelectedCountries = countryMapData.features.filter((availableCountry) =>
            searchParamCountryCodes.includes(availableCountry.properties.adm0_id.toString())
        );
        setSelectedCountries(newSelectedCountries);
    }, [searchParams]);

    // update state and query params with new value
    const setSelectedCountriesFn = (newValue: CountryMapData[] | undefined) => {
        setSelectedCountries(newValue);
        const selectedCountryIds = newValue?.map((country) => country.properties.adm0_id) ?? [];
        router.push(`${pathname}?${PARAM_NAME}=${selectedCountryIds.join(',')}`);
    };

    return [selectedCountries, setSelectedCountriesFn] as const;
};
```