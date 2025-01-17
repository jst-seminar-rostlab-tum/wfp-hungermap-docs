---
sidebar_position: 3
---
# How to add a leaflet layer
## Overview
Leaflet layers are placed in the [Map Component](../leaflet_map/map_component.md) in the MapContainer component. A leaflet layer
can be or contain any component that works with [react-leaflet](https://react-leaflet.js.org/) such as displaying GeoJSON
data or markers.

## 1. Finding the right position
As layering suggests, most of the components are layered from back to front considering the order in the MapContainer component.
Some exceptions are elements like tooltips or markers that are displayed on top of basic data layers.

## 2. Adding a new component
For each new layer an own wrapper component should be created so that directly in the MapContainer only one component is used.
This improves the readability by hiding code that is not relevant in this part and making clear what layer is added.
For adding behavior when to show the layer the component could only be rendered when some condition is met such as for the
vegetation layer:
```ts
{selectedMapType === GlobalInsight.VEGETATION && (
        <Pane name="vegetation_raster" style={{ zIndex: 2 }}>
          <TileLayer url="https://dev.api.earthobservation.vam.wfp.org/tiles/latest/viq_dekad/{z}/{x}/{y}.png" />
        </Pane>
      )}
```
This is a suggestion but a more extended condition could also be put in the wrapper or its children components.
