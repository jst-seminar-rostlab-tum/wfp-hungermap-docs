# Traffic measurement with Google Analytics

The traffic of the website is tracked with Google Analytics.

## Dynamically loading GA once consent is granted

Even though Google Analytics doesn't send or store any personal data, tracking only starts when the users consents to it with the `CookieConsentPopup.tsx`.

When the page first loads, only a base script is loaded that doesn't do any tracking, just initializes Google Analytics. This can be found in a Script tag in the base `layout.tsx`.

The `CookieConsentPopup` component is rendered by default. If the `cookie_consent` value is not set in local storage, it will render the popup. The user's choice is saved in local storage. If the user clicks yes, the Google Analytics script is loaded dynamically and tracking starts.

## Events

An event is sent by default to Google Analytics whenever the user opens a new page or downloads a file. We also send custom events when the user changes the selected map or alert, or clicks on a country. If events happen quickly, GA will group them and send them together, not one-by-one. To add a new custom event, see [How to add new GA event](/docs/how_to/how_to_add_new_ga_event).

### \<alert_type>_alert_selected

These events are sent when the user selectes an alert, where \<alert_type> is the new value of the `AlertType` enum (`countryAlerts`, `conflicts` or `hazards`). The event is sent from the `toggleAlert()` method of `SelectedAlertContext.tsx`.

### \<map_type>_map_selected

These events are sent when the user selectes a map, where \<map_type> is the new value of the `GlobalInsight` enum (`food`, `nutrition`, `vegetation`, `rainfall` or `ipc`). The event is sent from the `setSelectedMapType()` method of `SelectedMapContext.tsx`.

### \<country_iso3>_country_selected
These events are sent when the user selects a country on the map, where \<country_iso3> is the ISO3 code of the selected country. The event is sent from the `useEffect` hook of `Map.tsx`. The currently selected map type is also sent as the `selectedMap` parameter.

### \<country_iso3>_\<map_type>_countrymap_selected
These events are sent when the user selects a country on the map, where \<country_iso3> is the ISO3 code of the selected country and \<map_type> is the new value of the `GlobalInsight` enum (`food`, `nutrition`, `vegetation`, `rainfall` or `ipc`). The event is sent from the `useEffect` hook of `Map.tsx`. The exact same data is sent in this event then in the previous event, but the selected map is sent in the event name istead of the parameter, which is helpful for creating exploration on the GA dashboards.

## Dashboards and explorations

All this data can be analyzed and explored on the GA dashboard. Besides the default graphs and reports that show the users based on location, device type, etc, we've also created some custom explorations to display the data gathered from the custom events. To view them, click on Explore on the sidebar and select the one you want. Each has a line chart and a table variant, showing the number of times each custom event was called in the given time-period.