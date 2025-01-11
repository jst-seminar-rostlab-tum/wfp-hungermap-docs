---
sidebar_position: 4
---
# How to add an alert
## Overview
Alerts are [leaflet markers](https://leafletjs.com/reference.html#marker) that are rendered in the map. If you first want to get an overview of how alerts are implemented
in the hunger map you can read more on the [Alert page](../leaflet_map/map_alerts.md). Adding a new alert
consists of adding a new alert type to the leaflet map and making it selectable through the legend component or sidebar.
The components created for the 

## 1. Adding the alert as a new type
First of all we have to add the new alert type to the enum keeping track of all types. Go to src/domain/enums/AlertType.ts
and add the new alert for example like this:
```ts
export enum AlertType {
    COUNTRY_ALERTS = 'countryAlerts',
    CONFLICTS = 'conflicts',
    HAZARDS = 'hazards',
    MYALERT = 'myAlert', // our new alert
}
```

## 2. Adding the alert to leaflet
Now we can add a new alert layer to src/components/Map/Alerts/AlertContainer.tsx. Before doing this however we have to create a
new layer component for our alert. Mapping a marker for each data point is the easiest way but you can also use clustering
as it is used for example for the conflict layer. You can also find an example of clustering on the [Alert Page](../leaflet_map/map_alerts.md).\
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

## 3. Adding the alert to the legend / sidebar



