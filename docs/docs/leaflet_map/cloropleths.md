# Custom Choropleths
Author: ```Lukas Weigmann```

**Author:** `Lukas Fabio Weigmann`

## Overview
The map supports multiple thematic data visualizations for each mode. For the FCS, Nutrition and IPC view Cloropleth elements
are implemented in order to make countries clickable and add other behavior. The basic structure of these Cloropleth elements
is very similar for all three modes. A main cloropleth wrapper component (e.g. ```src/components/Map/FcsMap/FcsCloropleth.tsx```) is placed inside of the MapContainer of the
[Map Component](map_component). This component is visible in the global view and contains functionality that is needed for
behavior in the global view. Once a country is selected, a different component inside of the before mentioned component (that is being disabled now) is rendered.
This cloropleth element on country level (e.g. ```src/components/Map/FcsMap/FcsCountryCloropleth.tsx```) is providing all necessary functionality for rendering the data of the different
regions of one country. Since there are small differences between the three cloropleths we will further explain its details in the
following:

## FCS and Nutrition Cloropleth
The implementation for the Food Consumption and Nutrition View are very similar and fairly simple.
### Global View
Each cloropleth component directly placed in the MapContainer is rendering one single country. This means in the [Map Component](map_component)
to each country of all the data a single Cloropleth element is mapped. This Cloropleth element thus also contains functionality
that is needed in the global view such as binding a tooltip showing the name of the country to the component but also rendering the
data for this country in the needed style and with the needed functionality. In FCS mode this for example means displaying countries that have no further data and should not be clickable
with some grey overlay. Operations for this are located for example in ```src/operations/map/FcsChoroplethOperations.ts```.

### Country View
Once the user selects a country, a loading skeleton showing the shape of the country and the accordions (for very small screens skeletons instead) are shown while
the more detailed data for the country is loading. When the loading has finished the Country Cloropleth Component is rendered.
This component simply renders the data of the regions with needed style and functionality. Operations are located for example in
```src/operations/map/FcsCountryChoroplethOperations.tsx```.

## IPC Cloropleth
The main difference to the other Cloropleth elements mentioned above is that the component placed in the [Map Component](map_component)
renders all countries shown in the global view. That's why it contains a component called IpcGlobalCloropleth that renders
all the countries shown in the global view. The components for the country view are implemented analogously to FCS and Nutrition.
