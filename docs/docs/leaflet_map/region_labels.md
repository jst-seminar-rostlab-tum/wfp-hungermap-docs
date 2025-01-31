# Region Labels
Author: ```Lukas Weigmann```

## Overview
The region labels are shown in the FCS and Nutrition view. Since for some countries like Vietnam labels get very long and would
have to fit in very small region shapes the display of the names switches between the full label and "...".
This is done based on an estimation on the label width and the borders of the region.

## Setup
The labels are set up once the user selects a country and the regions are rendered by the setupRegionLabelTooltip() method
that is located in [Map Operations](map_operations.md). This method is called inside of the [Cloropleth Elements](cloropleths)
on country level in the corresponding components. Let's have a closer look at how the labels are set up: 
```ts
static
setupRegionLabelTooltip(
    feature: Feature<CommonRegionProperties>,
    regionLabelData: FeatureCollection<Geometry, GeoJsonProperties>,
    countryIso3: string,
    map: L.Map): L.Tooltip | undefined
{
    /*----------------- 1 -------------------*/
    const featureLabelData = regionLabelData.features.find((labelItem) => {
        return (
            labelItem.properties?.iso3 === countryMapData.properties.iso3 &&
            labelItem.properties?.name === feature.properties?.Name
        );
    });
    /*----------------- 1 -------------------*/

    /*----------------- 2 -------------------*/
    if (featureLabelData && featureLabelData.geometry.type === 'Point') {
        const tooltip = L.tooltip({
            permanent: true,
            direction: 'center',
            className: 'text-background dark:text-foreground',
            content: '',
        }).setLatLng([featureLabelData.geometry.coordinates[1], featureLabelData.geometry.coordinates[0]]);
        tooltip.addTo(map);
        setRegionLabelTooltips((prevRegionLabelData) => [...prevRegionLabelData, tooltip]);
        /*----------------- 2 -------------------*/


        /*----------------- 3 -------------------*/
        const zoomListener = () => this.updateRegionLabelTooltip(feature, map, tooltip);

        this.updateRegionLabelTooltip(feature, map, tooltip);
        map.on('zoom', zoomListener);
        tooltip.on('remove', () => {
            map.off('zoom', zoomListener);
        });
        /*----------------- 3 -------------------*/
        return tooltip;
    }
    return undefined;
}
```
1: From the data containing information about the label and position for each label the right element is searched for.

2: The found label data is then used to create a tooltip element that is added to leaflet map element and also to a list that keeps track of
all the used tooltips in order to be able to remove them later again. This list is passed down from the main [Map Component](map_component).

3: Lastly listeners are added. When zooming, the labels should update and if given should switch between the full region name and "...". Also when the
tooltip is removed, the listener that was just setup should be removed so that no unnecessary listeners are running in the background for elements that are not being used anymore.

The method returns the created tooltip so that the calling component can add it to a list in order to keep track of it for later removal.

## Updating
The updating process of the labels mainly consist of reevaluating what the labels should display:
```ts
static updateRegionLabelTooltip(
    feature: GeoJsonFeature<Geometry, GeoJsonProperties>,
    map: L.Map,
    tooltip: L.Tooltip
  ) {
    /*----------------- 1 -------------------*/
    const bounds = L.geoJSON(feature).getBounds();
    const zoom = map.getZoom();
    const width = (bounds.getEast() - bounds.getWest()) * zoom;
    /*----------------- 1 -------------------*/

    /*----------------- 2 -------------------*/
    const isMaxZoom = zoom === MAP_MAX_ZOOM;
    const isZoomThreshold = zoom === SELECTED_COUNTRY_ZOOM_THRESHOLD;
    /*----------------- 2 -------------------*/

    /*----------------- 3 -------------------*/
    const text = feature.properties?.Name || '';
    const textWidth = text.length * REGION_LABEL_SENSITIVITY;
    /*----------------- 3 -------------------*/

    const truncatedText = isZoomThreshold || (textWidth > width && !isMaxZoom) ? '...' : text;
    tooltip.setContent(truncatedText);
  }
```
1: The width of the region is calculated by taking the most eastern and western point of its shape and considering the zoom level.

2: In case the map is fully zoomed in or exactly at the threshold before switching to the global view again a flag is set.

3: Now the width of the text is estimated by counting the letters and multiplying this number with a sensitivity value in order to
modify how wide one letter should be considered.

Lastly the text width is compared to the width of the shape of the region and the corresponding content is set. If the map is
fully zoomed in all labels are fully shown, if the map is at the threshold before switching to the global view all labels are
shown with "...".

## Removal
When the selected country or the selected mode are changing also the labels should be updated. Therefore, in the [Map Component](map_component)
there is a useEffect hook that contains the functionality for removing all tooltips from the map that were added to a list in the setup step.
```ts
if (mapRef.current) {
      regionLabelTooltips.forEach((tooltip) => {
        tooltip.removeFrom(mapRef.current as L.Map);
      });
      setRegionLabelTooltips([]);
    }
```

## Customization
In the map constants file located in ```src/domain/constant/map/Map.ts``` there is a property called **REGION_LABEL_SENSITIVITY**.
This value controls how sensitive the labels should be to zooming considering the display of the full name or "...".
A higher value means that labels are turning faster into "...".
