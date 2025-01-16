# Sidebar Component

The Sidebar component provides a collapsible navigation and control panel, offering country search, global insights and alerts selection, and additional navigation options.

<div align="center">
  <img src="/img/sidebar/sidebar_dark.png" alt="Sidebar in dark mode" width="45%" />
  <img src="/img/sidebar/sidebar_light.png" alt="Sidebar in light mode" width="45%" />
</div>

## Features

- Collapsible
- Country search
- Global insights (map types) selection
- Alerts selection
- Theme switching
- Newsletter subscription
- Quick navigation links

## Component Structure

### Header Section

- WFP Logo with text
- Sidebar collapse button
- Theme switcher
- Country search

### Body Section

1. **Global Insights Section** (Map types)

   - Food consumption
   - Nutrition
   - Vegetation
   - Rainfall
   - IPC/CH

2. **Alerts Section**

   - Country alerts
   - Hazards
   - Conflicts

3. **Footer Section**
   - Newsletter subscription button
   - Navigation links grid (quick access to important pages)

## Alerts Menu

<img src="/img/sidebar/alerts_menu.png" alt="ALerts menu" width="300" />

The AlertsMenu component serves as a flexible alert selection interface with support for nested alerts. Only one alert can be selected at a time. Per default the country alerts are selected. Clicking on an alert category with multiple alert subtypes will expand the nested alerts if there are any. Clicking again on a selected alert will deselect it.

Uses the `SelectedAlertContext`:

```tsx
const { isAlertSelected, toggleAlert } = useSelectedAlert();
```

- Manages which alert type is currently selected
- Used to highlight the active alert button
- When an alert is selected `toggleAlert()` updates the selected alert which is used by other components throughout the app

Key Features:

- Popover menus for nested alerts
- Tooltips for accessibility
- Loading indicators

## Country Search

<img src="/img/sidebar/country_search.png" alt="Country search" width="300" />

The component serves as the primary country selection interface, with built-in data validation and availability checks. Instead of manually searching and clicking on a country on the map, users can quickly find and select a country from the list. The map will automatically zoom to the selected country and display country-specific data.

Key Features:

- Alphabetically sorted country list
- Countries without data are disabled
- Integration with country selection context

## Collapsed Sidebar

Minimized version of the main sidebar. The CollapsedSidebar serves as a compact navigation interface, maintaining core functionality while maximizing screen space.

It contains the Global Insights (Map type) quick-access buttons with icons only to save space. Everything else is hidden. The alerts menu pops out of the sidebar and floats on the bottom left of the screen.

<img src="/img/sidebar/sidebar_collapsed.png" alt="Collapsed Sidebar" width="200" />

## Navigaton

The sidebar contains a footer section with quick navigation links to important pages. The links are displayed in a grid layout for easy access. On these other pages, the sidebar is not visible and a top navigation bar is used instead.

## Responsive Behavior

<img src="/img/sidebar/responsive.png" alt="Responsive Sidebar" width="300" />

- Full screen width on mobile (`w-screen`)
- Fixed width of 280px on desktop (`sm:w-[280px]`)
- Automatic collapse on mobile after interaction
- Scrollable body on short screens

## Contexts Integration

1. `SidebarContext`:

```tsx
const { isSidebarOpen, toggleSidebar, closeSidebar } = useSidebar();
```

- Controls the sidebar's open/closed state
- When the sidebar toggle button is clicked: `toggleSidebar()` is called
- On mobile, `closeSidebar()` is called to close the sidebar, when global insights or alerts are selected

2. `SelectedMapContext`:

```tsx
const { selectedMapType, setSelectedMapType } = useSelectedMap();
```

- Manages which map type (Global Insight) is currently selected
- Used to highlight the active map button
- When a map type is selected `setSelectedMapType()` updates the selected map which is used by other components throughout the app
- Only one map type can be selected at a time
- Default map type is Food Consumption

3. `SelectedCountryIdContext`:

```tsx
const { selectedCountryId, setSelectedCountryId } = useSelectedCountryId();
```

- Manages the currently selected country in the country search
- Controls the selected state of the country search
- When a country is selected: `setSelectedCountryId()` updates the selected country

4. `AccodionsModalContext`:

```tsx
const { clearAccordionModal } = useAccordionsModal();
```

- Used to clear any open accordion modals when switching map types
- Ensures clean state transitions between different views

## Props

```typescript title="/src/domain/props/SidebarProps.ts"
interface SidebarProps {
  countryMapData: CountryMapDataWrapper;
  fcsData: Record<string, CountryFcsData>;
}
```

- **countryMapData** - Contains geographical and metadata information for all countries. Powers the country search autocomplete and map rendering
- **fcsData** - Contains Food Consumption Score data for each country. Used to determine data availability for countries

The props interact with several data-fetching hooks:

- `useIpcQuery()`: For IPC/CH data
- `useNutritionQuery()`: For nutrition data

These combined with the props determine:

1. Country search functionality
2. Data availability indicators
3. Map layer rendering
4. Country selection validation

## Usage

```tsx
import { Sidebar } from "@/components/Sidebar/Sidebar";

<Sidebar countryMapData={countryData} fcsData={foodConsumptionData} />;
```
