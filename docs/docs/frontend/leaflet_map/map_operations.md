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

### handleCountryTooltip()
#### Description
This method handles the functionality for the tooltips shown in the global view when hovering over a country.

#### Parameters
- ```geoJsonRef```: Reference to the cloropleth element i.e. the country the tooltip is being attached to
- ```map```: The leaflet map
- ```fcsData?```: Food consumption data - needs to be set when calling from FCS cloropleth
- ```nutritionData?```: Nutrition data - needs to be set when calling from nutrition cloropleth
- ```countryMapData?```: general data about the country the tooltip is being attached to - needs to be set when calling from nutrition cloropleth

#### Returns
A method destructing the map listeners that is used in the corresponding useEffect of the [Cloropleth Elments](cloropleths.md).

#### Workflow
The method binds and unbinds the tooltips to the leaflet map. When dragging the map however, there is a certain lag which can
lead to other tooltips being opened even though they are not supposed to. Therefore, when dragging, the method unbinds all the
tooltips of the countries except for the one that the mouse is currently hovering over and rebinds them once the dragging is
finished.
