# Legends

**Author:** `Ahmed Farouk`

Legends play a vital role in providing contextual information for the data displayed on the map. There are two primary types of legends: **Gradient Legends** and **Point Legends**, both are always positioned at the bottom right of the screen.

1. **Gradient Legends**:

   - Display continuous data.
   - Example: Gradual changes in rainfall or vegetation indices.

2. **Point Legends**:
   - Represent non-continuous or categorical data.
   - Example: Specific alerts or categories like conflicts or hazards.

**Maximum Display:**
At any given time, only maximum of **two legends** can be displayed simultaneously. They may include:

- One legend for global alerts (e.g., conflicts , hazards, country alerts).
- One legend based on the selected map type (e.g., IPC, Nutrition, FCS).

---

## When Legends Are Displayed

The application operates in two primary views: **World View** and **Country View**.

1. **World View**: When no country is selected.
2. **Country View**: When a specific country is selected.

### Legend Display Scenarios:

1. **Gradient Legends** are shown for:

   - Rainfall (World View)
   - Vegetation (World View)
   - FCS (World and Country Views)
   - Nutrition (Country View)
   - IPC (World View)

2. **Point Legends** are shown for:
   - All types of alerts (conflicts , hazards, country alerts)
   - Nutrition (World View)
   - IPC (Country View)

---

## Main Components of Gradient Legend

In order to use gradient legend, the following properties are required:

1. **Title**: The legend’s title.
2. **Color Data**: Contains:
   - Color gradients.
   - Hover data for displaying exact values on hover.
3. **Start Label**: Indicates the starting value of the gradient.
4. **End Label**: Indicates the ending value of the gradient.
5. **Popup Info Data**: Additional information displayed when interacting with the legend info icon.
6. **Optional Prop**:
   - **`hasNotAnalyzedPoint`**: Adds a gray point to indicate regions without data.

![Large Screen Example](/img/legend/gradient_legend.png)

---

## Main Components of Point Legend

In order to use point legend, the following properties are required:

1. **Title**: The legend’s title.
2. **Records**: Includes:
   - Colors.
   - Labels for each point category.
3. **Popup Info Data**: Additional information displayed when interacting with the legend info icon.

![Large Screen Example](/img/legend/point_legend.png)

---

## Different Screen Sizes

The application is optimized for three screen sizes: **Large**, **Medium**, and **Small**.
Each screen size has a tailored component rendering, with variations in how legends and map data are displayed.

### 1. Large Screens

![Large Screen Example](/img/legend/legend_large.png)

### 2. Medium Screens

![Medium Screen Example](/img/legend/legend_medium.png)

### 3. Small Screens

![Small Screen Example](/img/legend/legend_small.png)

---

## How to Use Legends

The `MapLegend` component manages and renders the legends dynamically. It relies on three custom hooks to retrieve the necessary context:

1. **`useSelectedMap()`**: Provides the currently selected map type.
2. **`useSelectedAlert()`**: Provides the currently selected alert type.
3. **`useSelectedCountryId()`**: Provides the currently selected country ID, used specifically for Country View.

#### Process Flow:

1. The hooks retrieve the relevant context data.
2. This data is passed to the `mapLegendData` function, which processes the input using a switch-case.
3. The function generates a minimum of **one item** and a maximum of **two items**, depending on the input.
4. These items are stored in the `items` state and rendered inside the `LegendContainer` component.
