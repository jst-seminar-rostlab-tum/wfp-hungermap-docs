# Map Operations

**Author:** `Lukas Weigmann`

## Overview
The MapOperations class provides a set of methods for managing and interacting with geographical data on the map.
It contains functionality for the main [MapComponent](map_component) but also for smaller components such as [Cloropleths](cloropleths)
that are used in the map.

## Class Details
### convertCountriesToFeatureCollection()
#### Description
This method converts a list of country features into a GeoJSON FeatureCollection.

#### Parameters
- ```countryFeatures```: List of country features

#### Returns
A GeoJSON FeatureCollection.

### updateRegionLabelTooltip()
#### Description
Updates the text content of a tooltip based on the zoom level and the properties of the feature. This is for example needed
when the user is zooming in or out.

#### Parameters
- ```feature```: GeoJSON feature providing tooltip data
- ```map```: Leaflet map instance
- ```tooltip```: Tooltip instance to be updated

#### Workflow
You can find details about the region labeling under [region labels](region_labels).

### setupRegionLabelTooltip()
#### Description
The method configures a permanent tooltip for region labels and updates it dynamically during zoom events.

#### Parameters
- ```feature```: GeoJSON feature representing the label
- ```regionLabelData```: Data for region labels
- ```countryIso3```: iso3 code for matching the regions with the country
- ```map```: Leaflet map instance

#### Returns
The method returns the created tooltip

#### Workflow
You can find details about the region labeling under [region labels](region_labels).

### attachCountryNameTooltip()
#### Description
This method attaches a very basic tooltip showing the name of the passed country to the passed layer.

#### Parameters
```feature```: country feature with the name that has to be shown in the tooltip
```layer```: layer the tooltip is attached to
