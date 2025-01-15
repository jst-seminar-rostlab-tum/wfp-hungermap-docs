---
sidebar_position: 2
---

# Map Component

## 1. MapContainer
The MapContainer component is the root container for the map.
It contains the different layers of the map and configures the general behavior.

### Map Initialization
The leaflet map component can be configured with properties in the MapContainer element:
```ts
<MapContainer
      ref={mapRef}
      center={[21.505, -0.09]}
      zoom={3}
      renderer={renderer}
      maxBounds={[
        [-90, -180],
        [90, 180],
      ]}
      minZoom={MAP_MIN_ZOOM}
      maxZoom={MAP_MAX_ZOOM}
      maxBoundsViscosity={1.0}
      zoomControl={false}
      style={{ height: '100%', width: '100%', zIndex: 1 }}
    >
```
Since the MapContainer component is a react-leaflet component you can easily add an customize the map behavior according to their [Map Documentation](https://leafletjs.com/reference.html#map).


The map is initialized using the following key properties of the ```Map.ts``` file located in the constants folder:

```ts
export const MAP_MAX_ZOOM = 8;
export const MAP_MIN_ZOOM = 3;
export const SELECTED_COUNTRY_ZOOM_THRESHOLD = 5;

export const REGION_LABEL_SENSITIVITY = 0.8;

export const oceanBounds: LatLngBoundsExpression = [
  [-90, -180],
  [90, 180],
];
export const countryBaseStyle = {
  fillColor: 'hsl(var(--nextui-countriesBase))',
  fillOpacity: 1,
  weight: 0,
};

export const countryBorderStyle = {
  color: 'hsl(var(--nextui-countryBorders))',
  weight: 1,
  fillOpacity: 0,
};
export const disputedAreaStyle = {
  fillOpacity: 0,
  color: 'black',
  weight: 1,
  dashArray: '5,5',
};
```

- **MAP_MAX_ZOOM:** how much you can zoom in the map
- **MAP_MIN_ZOOM:** how much you can zoom out the map
- **SELECTED_COUNTRY_ZOOM_THRESHOLD:** at what zoom level the map should switch from the country view mode to the global view when zooming out
- **REGION_LABEL_SENSITIVITY:** how sensitive the labels of the regions of countries should react to zooming when switching from displaying the name to "...". A higher value makes the labels turn faster into "...". More about this you can read in [region labels](map_region_labels.md)
- **oceanBounds:** sets the border for the ocean layer
- **countryBaseStyle:** sets the style for the base shapes of all the countries
- **countryBorderStyle:** sets the style for the country borders
- **disputedAreaStyle:** sets the style for the borders of disputed areas

### Layers
The map renders layers dynamically based on user interaction. Layers are stacked beginning from the first element in the MapContainer
wrapper component being in the background. Here you can read more about [how to add a new leaflet layer](../how_to/how_to_add_leaflet_layer).

#### Alert Layer
The AlertContainer component contains the functionality for displaying the alerts.

#### Controls Layer
The zoom control component and BackToGlobalButton contain the controls for navigating within the map.

#### Country Layer
For rendering the ocean a svg overlay is used. For the shapes of the countries the data in GeoJSON format is rendered as
an additional layer.

#### Map Type Layer
Depending on which mode is selected different contents should be displayed. This is achieved by only rendering the needed components when certain
conditions are fulfilled. These components again contain all the functionality needed for each individual mode. For the Vegetation Mode, Rainfall
Mode and Food Consumption Mode TileLayers are rendered using raster layers provided by the Backend. These are responsible for the colored
overlays.
In Food Consumption Mode, Nutrition Mode and IPC Mode also [Cloropleth Components](map_cloropleths) are used for providing
interaction functionality with the countries.

#### Border Layer
Regular borders and borders of disputed areas are rendered in two separate leaflet layers. You can customize their style
in the ```Map.ts``` constants file (see above).

## 2.Data Flow
The component processes geographic and contextual data using props and local state management. When the Map Type or
the value of the selectedCountryId context changes (i.e. when clicking on a country) new data is fetched. How these operations
work in detail is further explained in [Map Operations](map_operations.md).

### Props

- ```countries```: A FeatureCollection containing country-level geographic data used for the base shapes and other things
- ```disputedAreas```: A FeatureCollection of disputed territories
- ```fcsData```: Food security data used for choropleth
- ```alertData```: Alert-related data for display within the map

### State Management

#### useState manages various aspects of the map:
- ```countryData```: Holds details of selected countries
- ```regionData```: Holds details of the regions of the selected country
- ```countryIso3Data```: Holds details about nutrition data used for accordion elements in the FCS and IPC mode
- ```isLoadingCountry```: Tracks whether the data for a country is currently loading
- ```regionNutritionData```: Holds details of the regions of the selected country in Nutrition mode
- ```ipcRegionData```: Holds details of the regions of the selected country in IPC mode
- ```selectedCountryName```: Holds the name of the currently selected country if present
- ```regionLabelData```: Holds details (name, position) of all labels for all regions. It is only loaded once.
- ```regionLabelTooltips```: Holds the tooltips showing the labels of the regions of the currently selected country (More in [Region Labels](map_region_labels.md))
- ```isDataAvailable```: Tracks whether relevant data is present for the selected country

#### Contexts provide global state across the application:
- ```useSelectedCountryId```: Tracks the currently selected country
- ```useSelectedMap```: Handles the selected map type


## 3. Error Handling
The component handles missing data. When data is unavailable for a selected country the map zooms out by 4 levels for a broader view.
The state isDataAvailable tracks data presence and is set in the [Map Operations](map_operations.md).

