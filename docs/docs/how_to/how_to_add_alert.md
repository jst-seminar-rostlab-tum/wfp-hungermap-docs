# How to add an alert

**Author:** `Lukas Fabio Weigmann`

## Overview
Alerts are [leaflet markers](https://leafletjs.com/reference.html#marker) that are rendered in the map. If you first want to get an overview of how alerts are implemented
in the hunger map you can read more on the [Alert Page](../frontend/leaflet_map/map_alerts.md). Adding a new alert
consists of adding a new alert type to the leaflet map and making it selectable through the legend component or sidebar. Additionally
also information about the new alert can be added to the legend accordion on the bottom of the page.

## 1. New Type
First of all we have to add the new alert type to the enum keeping track of all types. Go to ```src/domain/enums/AlertType.ts```
and add the new alert for example like this:
```ts
export enum AlertType {
    COUNTRY_ALERTS = 'countryAlerts',
    CONFLICTS = 'conflicts',
    HAZARDS = 'hazards',
    MYALERT = 'myAlert', // our new alert
}
```

## 2. Leaflet Map
Now we can add a new alert layer to ```src/components/Map/Alerts/AlertContainer.tsx```. Before doing this however we have to create a
new layer component for our alert. Mapping a marker for each data point is the easiest way but you can also use clustering
as it is used for example for the conflict layer. You can find an example of clustering on the [Alert Page](../frontend/leaflet_map/map_alerts.md).\
For now we will just use a simple marker:
```ts
export function MyAlertLayer() {
    const { data, isPending } = useMyAlertQuery(true);

    return (
        <div>
            {data &&
        !isPending &&
        data.map((myAlert) => {
            return <Marker position={myAlert.position} />;
        })}
    </div>
);
}
```
For fetching the data you can create a hook in src/domain/hooks/alertHooks.ts that could look like in the following.
Here you can find more information on [how to add a query](how_to_add_query.md).
```ts
export const useMyAlertQuery = (enabled: boolean) =>
    useQuery<MyAlertData[]>(
        {
            queryKey: ['fetchMyAlert'],
            queryFn: alertsRepo.getMyAlertData,
            enabled,
        },
        cachedQueryClient
    );
```
Now that we have finished these two things we can add our new layer component to the AlertContainer file:
```ts
export const AlertContainer = React.memo(({ countries, alertData }: AlertContainerProps) => {
    const { selectedAlert } = useSelectedAlert();

    switch (selectedAlert) {
        case AlertType.CONFLICTS:
            return <ConflictLayer />;
        case AlertType.HAZARDS:
            return <HazardLayer />;
        case AlertType.COUNTRY_ALERTS:
            return <CountryAlertsLayer countries={countries} alerts={alertData} />;
        case AlertType.MYALERT: // Our new layer
            return <MyAlertLayer />;
        default:
            return null;
    }
});
```

## 3. Legend / sidebar
First we have to add the new alert type with some information to sidebarAlertTypes in ```src/operations/sidebar/SidebarOperations.ts```:
```ts
static sidebarAlertTypes: SidebarAlertType[] = [
    {
        key: AlertType.COUNTRY_ALERTS,
        label: 'Country Alerts',
        icon: '/country_alerts.svg',
    },
    {
        key: AlertType.HAZARDS,
        label: 'Hazards',
        icon: '/menu_hazards.webp',
    },
    {
        key: AlertType.CONFLICTS,
        label: 'Conflicts',
        icon: '/menu_conflicts.webp',
    },
    {
        key: AlertType.MYALERT,
        label: 'MyAlert',
        icon: '/menu_myalert.webp',
    },
];
```
Also add the new alert type to the record in ```src/components/AlertsMenu/AlertsMenu.tsx``` holding information about if the data is currently being fetched:

```ts
const { isFetching: myAlertFetching} = useMyAlertQuery(false); // add this
const alertFetching: Record<AlertType, boolean> = {
    [AlertType.CONFLICTS]: conflictsFetching,
    [AlertType.HAZARDS]: hazardsFetching,
    [AlertType.COUNTRY_ALERTS]: false,
    [AlertType.MYALERT]: myAlertFetching, // add this
  };
```
Now a button for our new alert type will be rendered and the user can select it.

## 4. Legend component
For displaying information about the alert in the legend component on the bottom right add the new alert to ```src/domain/constant/legend/mapLegendData.tsx```:
```ts
case AlertType.MYALERT:
legendData.push({
    title: 'My alert',
    records: [{ label: 'An alert type', color: '#c95200e6' }],
});
break;
```
This would be a very simple new entry, but it can be extended with multiple records, an image as a renderItem and info
when hovering over it. An example for a more extensive legend component is the one for hazards:
```ts
case AlertType.HAZARDS:
      legendData.push({
        title: 'Severity of hazards',
        popoverInfo: <DataSourcePopover dataSourceKeys="hazards" />,
        records: [
          { label: HazardSeverity.WARNING, color: 'hazardWarning' },
          { label: HazardSeverity.WATCH, color: 'hazardWatch' },
          { label: HazardSeverity.ADVISORY, color: 'hazarAdvisory' },
          { label: HazardSeverity.INFORMATION, color: 'hazardInformation' },
          { label: HazardSeverity.TERMINATION, color: 'hazardTermination' },
        ],
        renderItem: HazardLegendItem,
      });
      break;
```


