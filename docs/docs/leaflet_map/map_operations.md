---
sidebar_position: 3
---

# Map Operations

## Overview
The MapOperations class provides a set of methods for managing and interacting with geographical data on the map.
It contains functionality for the main [MapComponent](map_component.md) but also for smaller components such as [Cloropleths](map_cloropleths.md)
that are used in the map.

## Class Details
### convertCountriesToFeatureCollection()
#### Description
This method converts a list of country features into a GeoJSON FeatureCollection.

#### Parameters
- ```countryFeatures```: List of country features

#### Returns
A GeoJSON FeatureCollection.

### createCountryNameTooltipElement()
#### Description
The method creates an HTML div element rendering the given country name within a CountryHoverPopover. This is required because Leaflet tooltips do not natively support React components.

#### Parameters
- ```countryName```: Name of the country.

#### Returns
An HTMLDivElement containing the tooltip content.

### updateRegionLabelTooltip()
#### Description
Updates the text content of a tooltip based on the zoom level and the properties of the feature. This is for example needed
when the user is zooming in or out.

#### Parameters
- ```feature```: GeoJSON feature providing tooltip data
- ```map```: Leaflet map instance
- ```tooltip```: Tooltip instance to be updated

#### Workflow
You can find details about the region labeling under [region labels](map_region_labels.md).

### setupRegionLabelTooltip()
#### Description
The method configures a permanent tooltip for region labels and updates it dynamically during zoom events.

#### Parameters
- ```feature```: GeoJSON feature representing the label.
- ```regionLabelData```: Data for region labels.
- ```countryMapData```: Data of the selected country.
- ```map```: Leaflet map instance.
- ```setRegionLabelTooltips```: Function to update the list of tooltips.

#### Workflow
You can find details about the region labeling under [region labels](map_region_labels.md).
