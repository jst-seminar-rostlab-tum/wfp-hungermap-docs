---
sidebar_position: 3
---

# Map Operations

## Overview
The MapOperations class provides a set of methods for managing and interacting with geographical data on the map.
It contains functionality for the main [MapComponent](map_component.md) but also for smaller components such as [Cloropleths](map_cloropleths.md)
that are used in the map.

## Class Details
### fetchCountryData()
#### Description
This method loads geographic and country-specific data based on the selected map type (GlobalInsight) and selected country (CountryMapData).
The data loaded here is then reused in the [Map Component](map_component.md) and then further passed down to the different components of the map.

#### Parameters
- selectedMapType: Type of map data to fetch (GlobalInsight)
- selectedCountryData: Data of the selected country
- setIsLoadingCountry: Updates the loading state
- setRegionData: Updates region data
- setCountryData: Updates country data
- setCountryIso3Data: Updates country ISO3 data
- setRegionNutritionData: Updates region nutrition data
- setIpcRegionData: Updates IPC (Integrated Phase Classification) region data
- regionLabelData: Data for region labels
- setRegionLabelData: Updates region label data
- setIsDataAvailable: Indicates whether data is available

#### Workflow
Depending on the map type (FOOD, IPC, or NUTRITION), the method loads and assigns the corresponding data.
It checks for specific data availability, such as nutrition or ipcPhase, and updates the respective states.

#### Error Handling
The method resets the loading state in case of errors, without causing the application to crash.

### resetSelectedCountryData()
#### Description
The method resets all country-specific data (regions, ISO3, nutrition, IPC) to undefined. This is for example needed after the selected country changes.

#### Parameters
The functions in the parameters are similar to the ones above and are used to update the various data states (setRegionData, setCountryData, etc.).

### convertCountriesToFeatureCollection()
#### Description
This method converts a list of country features into a GeoJSON FeatureCollection.

#### Parameters
- countryFeatures: List of country features

#### Returns
A GeoJSON FeatureCollection.

### createCountryNameTooltipElement()
#### Description
The method creates an HTML div element rendering the given country name within a CountryHoverPopover. This is required because Leaflet tooltips do not natively support React components.

#### Parameters
- countryName: Name of the country.

#### Returns
An HTMLDivElement containing the tooltip content.

### updateRegionLabelTooltip()
#### Description
Updates the text content of a tooltip based on the zoom level and the properties of the feature. This is for example needed
when the user is zooming in or out.

#### Parameters
- feature: GeoJSON feature providing tooltip data
- map: Leaflet map instance
- tooltip: Tooltip instance to be updated

### setupRegionLabelTooltip()
#### Description
The method configures a permanent tooltip for region labels and updates it dynamically during zoom events.

#### Parameters
- feature: GeoJSON feature representing the label.
- regionLabelData: Data for region labels.
- countryMapData: Data of the selected country.
- map: Leaflet map instance.
- setRegionLabelTooltips: Function to update the list of tooltips.

