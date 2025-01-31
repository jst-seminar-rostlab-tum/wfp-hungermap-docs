# Forecast Notifications

**Author:** `Marius Moldovan`

The **Forecast Notifications** module is designed to notify subscribers when the forecasted values for a country change by a certain percentage.

It can be executed through the `/send-bulk-emails` endpoint with the `topic_id` set to the mongoDB document ID of the `forecasts` topic.

## Environment Variables and Setup
- **DATA_CHANGE_THRESHOLD**: The percentage change threshold for the forecasted values to trigger an email notification. Default value is 15.

A cron job needs to be set up to run which calls the `/send-bulk-emails` endpoint.


## Process
1. `send_forecast_bulk_emails` checks for every subscriber of the topic if any of his subscribed countries have a forecasted value change greater than the threshold.
   This check if being performed by sending a request to the `/{adm0Id}/notification-check.json` endpoint on the HungerMap API.
   Responses from the HungerMap API are cached, resulting in a maximum of 1 request per country.
2. If yes, an email with all the relevant forecasted values is generated and sent to the subscriber.


## Classes and Functions

**Function**: `send_forecast_bulk_emails(topic)`:

Checks if subscribers to topic should be notified and if so, sends an email to all subscribers.

*Params*:
- **topic** (`dict`): The topic document

*Returns*:
- `None`

---

**Function**: `check_send_email_condition(country_id)`:

Checks if the email should be sent to the subscriber based on the data change threshold and
builds context object for the email generation.

*Params*:
- **country_id** (`str`): ID of the country to check for data change. This is the document ID of the country in the
  'countries' collection.

*Returns*:
- `tuple`: A tuple containing a boolean value indicating if the email should be sent and the email_context object.

```python
email_context = {
    "country_id": Int,
    "percentage_fcs": Int,
    "percentage_rcsi": Int,
    "country_name": String,
    "last_date": String,
    "last_fcs_value": Int,
    "last_rcsi_value": Int,
    "first_date_percentage_change_fcs": String,
    "first_date_percentage_chang_rcsi": String,
    "fcs_prediction": List containing the fcs prediction values (dict with fields: fcs, fcsHIgh, fcsLow, x),
    "rcsi_prediction": List containing the rcsi prediction values (dict with fields: rcsi, rcsiHIgh, rcsiLow, x),
}
```

---

**Function**: `calculate_percentage_change(old_value, new_value)`:

Calculates the percentage change between two values.

*Params*:
- **old_value** (`int`): The old value
- **new_value** (`int`): The new value

*Returns*:
- `int`: The percentage change between the two values