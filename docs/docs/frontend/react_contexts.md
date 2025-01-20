# React Contexts

## SelectedMapContext

Manages map type selection state and operations.

- Manages which map type (Global Insight) is currently selected
- Integrates with Google Analytics tracking
- Default selected map type: `GlobalInsight.FOOD`

**Interface:**

```typescript file="/src/domain/contexts/SelectedMapContext.tsx
interface SelectedMapTypeState {
  selectedMapType: GlobalInsight;                     // Currently selected map type. Default: GlobalInsight.FOOD.
  setSelectedMapType: (value: GlobalInsight) => void; // Update selected map type. Send an event to Google Analytics
}
```

**Provider:** `SelectedMapProvider`

- Initializes with `GlobalInsight.FOOD`
- Wrap components that need access to map selection state

**Hook:** `useSelectedMap()`

```typescript
const { selectedMapType, setSelectedMapType } = useSelectedMap();
```

- Use it inside a component to access and manage the selected map type

## SelectedAlertContext

Manages alert selection state and operations.

- Controls alert visibility on the map
- Integrates with Google Analytics tracking
- Default: `AlertType.COUNTRY_ALERTS`

**Interface:**

```typescript
interface SelectedAlertsState {
  selectedAlert: AlertType | null;                    // Currently selected alert. Default: AlertType.COUNTRY_ALERTS
  setSelectedAlert: (value: AlertType) => void;       // Set selected alert
  isAlertSelected: (alertType: AlertType) => boolean; // Check if alert is selected
  toggleAlert: (alertType: AlertType) => void;        // Set alert if not selected, otherwise reset. Send an event to Google Analytics
  resetAlert: () => void;                             // Sets selected alert to null
}
```

**Provider:** `SelectedAlertProvider`

- Initializes with `AlertType.COUNTRY_ALERTS`
- Wrap components that need access to alert state

**Hook:** `useSelectedAlert()`

```typescript
const { selectedAlert, toggleAlert, resetAlert } = useSelectedAlert();
```

- Use it inside a component to access and manage the selected alert type

## SelectedCountryIdContext

Manages the currently selected country by the country search and when clicking on the map.

- Handles country selection state
- Updates when a user selects a new country
- Allows null for no selection

**Interface:**

```typescript
interface SelectedCountryIdState {
  selectedCountryId: number | null;                         // Currently selected country ID. Default: null
  setSelectedCountryId: (countryId: number | null) => void; // Set selected country ID
}
```

**Provider:** `SelectedCountryIdProvider`

- Initializes with `null`
- Wrap components that need access to country selection state

**Hook:** `useSelectedCountryId()`

```typescript
const { selectedCountryId, setSelectedCountryId } = useSelectedCountryId();
```

- Use it inside a component to access and manage the selected country

## Usage Example

```tsx
function App() {
  return (
    <SelectedMapProvider>
      <SelectedCountryIdProvider>
        <SelectedAlertProvider>
          <MapComponent />
        </SelectedAlertProvider>
      </SelectedCountryIdProvider>
    </SelectedMapProvider>
  );
}

function MapComponent() {
  const { selectedMapType } = useSelectedMap();
  const { selectedCountryId } = useSelectedCountryId();
  const { selectedAlert } = useSelectedAlert();

  // Use the context values...
}
```

**Note:** All hooks must be used within their respective providers or they will throw an error.

## userRoleContext

See [Mock Authentication](/docs/frontend/mock-authentication).
