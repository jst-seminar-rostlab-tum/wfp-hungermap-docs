# Implementation

Author: `Cansu Moran`

## **Overview**

This document provides detailed information about the implementation of the forecasting pipeline. It explains the
code structure, the main functions, how the endpoints are triggered, and the process for generating and updating
predictions.

---

## **Core Implementation Details**

### **1. Data Flow and Prediction Workflow**

1. **Daily Updates**
    - A cron job can be used to trigger the `updatePredictions()` function every morning.
    - Predictions are calculated for the next 90 days based on the latest available data.
    - Updated predictions are stored in the database, replacing old entries if they exist or inserting new ones if they
      don’t.

2. **Frontend Endpoint**
    - An endpoint provides the predictions to the frontend in JSON format:
      ```python
        # @app.get("/<adm0Id>/predictions.json")
        def get_prediction_data(event=None, adm0Id=None):
            return getCountryPredictions(adm0Id)
      ```
    - The frontend uses this data to display FCS and RCSI graphs.

3. **Forecast Notification Checks**
    - An endpoint is used to evaluate whether a specified country, identified by adm0Id, is
      predicted to experience a percentage change in data (e.g., food security indicators) within the next 90 days:
      ```python
       # @app.get("/<adm0Id>/notification-check.json")
          def get_notification_info(event=None, adm0Id=None):
            # Read JSON data from the body of the request
            try:
                if event is None:
                    return {"error": "Request body must be JSON"}, 400

                body_data = event.get('body', {})

                if "percentage" not in body_data or not isinstance(body_data["percentage"], int):
                    return ({"error": "'percentage' is required and must be an integer"}), 400

                percentage = body_data["percentage"]

                # Call the function with validated data
                return getNotification(adm0Id, percentage)

            except Exception as e:
                return {"error": "An error occurred", "details": str(e)}, 500
      ```

---

### **2. Key Functions**

#### **getCountryFcsRcsiGraphData(adm0_id)**

Retrieves historical FCS and RCSI data for a specific country.

- **Inputs:**
  `adm0_id` (country identifier).
- **Outputs:**
  Graph data for FCS and RCSI.

```python
def getCountryFcsRcsiGraphData(adm0_id):
    result = db_repo.query(queries.fcsDataTypesPerCountry, [adm0_id])
    data_types = int(result[0].get('data_types', None))
    r = db_repo.query(queries.fcsRcsiGraph(0, data_types), [adm0_id, adm0_id, adm0_id])
    return r
```

---

#### **getCountryFcsRcsiPredictionData(adm0_id)**

Fetches previously predicted FCS and RCSI data from the database.

- **Inputs:**
  `adm0_id` (country identifier).
- **Outputs:**
  Prediction data for FCS and RCSI.

```python
def getCountryFcsRcsiPredictionData(adm0_id):
    r = db_repo.query(queries.fcsRcsiPrediction(adm0_id))
    return r
```

---

#### **getCountryPredictions(adm0_id)**

Processes prediction data into a JSON format for the frontend.

- **Inputs:**
  `adm0_id` (country identifier).
- **Outputs:**
  JSON-formatted predictions for FCS and RCSI graphs.

```python
def getCountryPredictions(adm0_id):
    try:
        fcs_rcsi_prediction_data = getCountryFcsRcsiPredictionData(adm0_id)
        country_fcs = getCountryFcs(adm0_id, 0)
        country_fcs1 = getCountryFcs(adm0_id, 1)
        country_fcs3 = getCountryFcs(adm0_id, 3)

        # Check if any rcsi values are NULL or missing
        RcsiGraphExists = True
        for item in fcs_rcsi_prediction_data:
            if item['rcsi'] is None:
                RcsiGraphExists = False
                break

        rcsiGraph = []
        if RcsiGraphExists:
            rcsiGraph = [
                {'x': item['prediction_date'].strftime('%Y-%m-%d'), 'rcsi': int(item['rcsi']),
                 'rcsiHigh': int(item['rcsi_upper_bound']),
                 'rcsiLow': int(item['rcsi_lower_bound']), 'updateDate': item['update_date'].strftime('%Y-%m-%d')}
                for item in fcs_rcsi_prediction_data
            ]

        data = {
            'fcs': float(country_fcs[0]['value']) if len(country_fcs) == 1 else None,
            'fcsPlus1': float(country_fcs1[0]['value']) if len(country_fcs1) == 1 else None,
            'fcsPlus3': float(country_fcs3[0]['value']) if len(country_fcs3) == 1 else None,
            'fcsGraph': [
                {'x': item['prediction_date'].strftime('%Y-%m-%d'), 'fcs': int(item['fcs']),
                 'fcsHigh': int(item['fcs_upper_bound']),
                 'fcsLow': int(item['fcs_lower_bound']), 'updateDate': item['update_date'].strftime('%Y-%m-%d')}
                for item in fcs_rcsi_prediction_data
            ],
            'rcsiGraph': rcsiGraph
        }
        return json.dumps(data)
    except Exception as e:
        return json.dumps({'error': 'Error retrieving data', 'details': str(e)})
```

---

#### **updatePredictions()**

Main function to calculate predictions and update the database.

- **Process:**
    1. Retrieves all country codes.
    2. Fetches historical data using `getCountryFcsRcsiGraphData()`.
    3. Prepares data for the forecasting model (`forecast_model`).
    4. Calls the forecasting function to generate FCS and RCSI predictions.
    5. Saves predictions to the database using the `upsert` method.

```python
def updatePredictions():
    try:
        country_codes = db_repo.query(queries.allCountryCodes)
        today_date = datetime.now().strftime('%Y-%m-%d')

        # Predict and update data for all countries
        for adm0_data in country_codes:
            adm0_id = adm0_data['adm0_code']
            fcs_rcsi_graph_data = getCountryFcsRcsiGraphData(adm0_id)

            # Prepare data for forecasting
            data = {
                'fcsGraph': [
                    {
                        'x': item['x'] if item['x'] is not None else None,
                        'fcs': int(item['fcs']) if item['fcs'] is not None else None,
                        'fcsHigh': int(item['fcs_upper_bound']) if item['fcs_upper_bound'] is not None else None,
                        'fcsLow': int(item['fcs_lower_bound']) if item['fcs_lower_bound'] is not None else None
                    }
                    for item in fcs_rcsi_graph_data if item is not None
                ],
                'rcsiGraph': [
                    {
                        'x': item['x'] if item['x'] is not None else None,
                        'rcsi': int(item['rcsi']) if item['rcsi'] is not None else None,
                        'rcsiHigh': int(item['rcsi_upper_bound']) if item['rcsi_upper_bound'] is not None else None,
                        'rcsiLow': int(item['rcsi_lower_bound']) if item['rcsi_lower_bound'] is not None else None
                    }
                    for item in fcs_rcsi_graph_data if item is not None

                ]
            }

            # Get predictions for FCS and RCSI
            fcs_pred, rcsi_pred = forecast_model.getRcsiFcsForecast(data)

            # Get the latest date in the data for date incrementation
            try:
                latest_date_str = max(item['x'] for item in fcs_rcsi_graph_data if item['x'] is not None)
                latest_date = datetime.strptime(latest_date_str, '%Y-%m-%d')
            except ValueError:
                # If no valid 'x' values are found, skip everything
                print("No valid 'x' values found in the data. Skipping prediction.")
                continue

            for i in range(len(fcs_pred)):
                # Increment the date
                prediction_date = (latest_date + timedelta(days=(i + 1))).strftime('%Y-%m-%d')

                prediction_row = {
                    'prediction_date': prediction_date,
                    'update_date': today_date,
                    'adm0_code': adm0_id,
                    'fcs_people': round(fcs_pred[i][0]) if fcs_pred[i][0] is not None else None,
                    'fcs_people_upper_bound': round(fcs_pred[i][1]) if fcs_pred[i][1] is not None else None,
                    'fcs_people_lower_bound': round(fcs_pred[i][2]) if fcs_pred[i][2] is not None else None
                }

                if rcsi_pred is not None:
                    prediction_row.update({
                        'rcsi_people': round(rcsi_pred[i][0]) if rcsi_pred[i][0] is not None else None,
                        'rcsi_people_upper_bound': round(rcsi_pred[i][1]) if rcsi_pred[i][1] is not None else None,
                        'rcsi_people_lower_bound': round(rcsi_pred[i][2]) if rcsi_pred[i][2] is not None else None
                    })

                db_repo.upsert(queries.upsertPredictions(prediction_row))

        success_message = "Predictions updated successfully for all countries."
        print(success_message)
        return ({"message": "Data submitted successfully"}), 200

    except Exception as e:
        print(f"Error updating predictions: {e}")
        return json.dumps({'error': 'Error retrieving data', 'details': str(e)})
```

---

#### **getNotification(adm0_id, percentage)**

If the percentage threshold is met or exceeded, the predictions are processed and notifications are sent. If the
threshold is not met, the response indicates that no notifications will be sent, ensuring the email backend remains
inactive for such cases.

- **Process:**
    1. Retrieves all country codes.
    2. Fetches historical data using `getCountryFcsRcsiGraphData()`.
    3. Prepares data for the forecasting model (`forecast_model`).
    4. Calls the forecasting function to generate FCS and RCSI predictions.
    5. Saves predictions to the database using the `upsert` method.

```python
def getNotification(adm0_id, percentage):
    try:
        # Fetch the historical data and predictions
        fcs_rcsi_graph_data = getCountryFcsRcsiGraphData(adm0_id)
        fcs_rcsi_prediction_data = getCountryFcsRcsiPredictionData(adm0_id)

        if not fcs_rcsi_graph_data or not fcs_rcsi_prediction_data:
            return json.dumps({'error': 'No data available for the specified country'})

        last_historical_entry = fcs_rcsi_graph_data[-1]
        last_historical_fcs = last_historical_entry["fcs"]
        last_historical_rcsi = last_historical_entry["rcsi"]
        last_historical_entry_date = last_historical_entry['x']

        fcs_first_change_date = None
        rcsi_first_change_date = None
        check = False

        # Iterate over predictions to find the first significant change
        for prediction in fcs_rcsi_prediction_data:
            prediction_date = prediction['prediction_date'].strftime('%Y-%m-%d'),
            predicted_fcs = prediction["fcs"]
            predicted_rcsi = prediction["rcsi"]
            fcs_change = None
            rcsi_change = None

            # Calculate percentage changes for fcs and rcsi
            if (predicted_fcs or last_historical_fcs) is not None:
                fcs_change = abs(predicted_fcs - last_historical_fcs) / last_historical_fcs * 100

            if (predicted_rcsi or last_historical_rcsi) is not None:
                rcsi_change = abs(predicted_rcsi - last_historical_rcsi) / last_historical_rcsi * 100

            if fcs_change is not None and fcs_change >= percentage and fcs_first_change_date is None:
                fcs_first_change_date = prediction_date
                check = True
            if rcsi_change is not None and rcsi_change >= percentage and rcsi_first_change_date is None:
                rcsi_first_change_date = prediction_date
                check = True
            if rcsi_first_change_date is not None and fcs_first_change_date is not None:
                break

        result = {
            "check": check
        }
        if check is True:
            result.update(
                {

                    "last_date": last_historical_entry_date,
                    "last_fcs_value": last_historical_fcs,
                    "last_rcsi_value": last_historical_rcsi,
                    "first_date_percentage_change": {
                        "fcs_first_change_date": fcs_first_change_date,
                        "rcsi_first_change_date": rcsi_first_change_date,
                    },
                    "predictions": {
                        'fcsGraph': [
                            {
                                'x': item['prediction_date'].strftime('%Y-%m-%d') if item[
                                                                                         'prediction_date'] is not None else None,
                                'fcs': int(item['fcs']) if item['fcs'] is not None else None,
                                'fcsHigh': int(item['fcs_upper_bound']) if item[
                                                                               'fcs_upper_bound'] is not None else None,
                                'fcsLow': int(item['fcs_lower_bound']) if item['fcs_lower_bound'] is not None else None
                            }
                            for item in fcs_rcsi_prediction_data if item is not None
                        ],
                        'rcsiGraph': [
                            {
                                'x': item['prediction_date'].strftime('%Y-%m-%d') if item[
                                                                                         'prediction_date'] is not None else None,
                                'rcsi': int(item['rcsi']) if item['rcsi'] is not None else None,
                                'rcsiHigh': int(item['rcsi_upper_bound']) if item[
                                                                                 'rcsi_upper_bound'] is not None else None,
                                'rcsiLow': int(item['rcsi_lower_bound']) if item[
                                                                                'rcsi_lower_bound'] is not None else None
                            }
                            for item in fcs_rcsi_prediction_data if item is not None
                        ]
                    }
                }
            )

        return result
    except Exception as e:
        print(f"Error during notification check: {e}")
        return json.dumps({'error': 'Error during notification check', 'details': str(e)})
```

---

#### **forecast_model.getRcsiFcsForecast(data)**

This function takes the historical data as input and generates predictions. More details can be found below in the **Forecast Model Implementation**.

---

### **3. How to Use the Endpoints**

- **Frontend Endpoint:**
  Use `GET /<adm0Id>/predictions.json` to retrieve predictions for a country.
---
- **Update Predictions Endpoint:**
  Use `POST /update_predictions` to update predictions for all the countries.
---
- **Notification Check Endpoint:**
Use `GET /<adm0Id>/notification-check.json` to evaluate whether the specified country, identified by `adm0Id`, is
    predicted to have a significant percentage change in data within the next 90 days. If the threshold is met, predictions
    are processed, and notifications are sent. Otherwise, the response indicates no notifications will be triggered.

**Example Request Body**
```json
{
  "percentage": 10
}
```
- **`percentage`**: (integer) The threshold percentage to evaluate the data change. This field is required.

**Example Response**

```json
{
  "check": true,
  "first_date_percentage_change": {
    "fcs_first_change_date": [
      "2024-03-14"
    ],
    "rcsi_first_change_date": [
      "2024-03-13"
    ]
  },
  "last_date": "2024-03-12",
  "last_fcs_value": "1584534",
  "last_rcsi_value": "2331895",
  "predictions": {
    "fcsGraph": [],
    "rcsiGraph": []
  }
}
```
- **`check`**: (boolean) Indicates whether the threshold percentage change was met (`true`) or not (`false`).

The following fields are added if and only if `check` key is `true`.
- **`first_date_percentage_change`**: Dates when the first bigger than or equal to percentage changes occurred within the FCS and RCSI predictions.
  - **`fcs_first_change_date`**: Date of the first significant FCS change.
  - **`rcsi_first_change_date`**: Date of the first significant RCSI change.
- **`last_date`**: -The most recent date of historical data.
- **`last_fcs_value`**: The last recorded FCS value.
- **`last_rcsi_value`**: The last recorded RCSI value.
- **`predictions`**: Graph data for FCS and RCSI predictions.
  - **`fcsGraph`**: Graph data for FCS predictions.
  - **`rcsiGraph`**: Graph data for RCSI predictions (empty array if no data).
---

### **4. Forecast Model Implementation**

#### Data Preprocessing: **`getRcsiFcsForecast`**

This function retrieves and forecasts both **FCS** and **RCSI** data:

- It extracts `fcsGraph` and `rcsiGraph` data from the input `graphData` and checks their validity.
- Converts data to NumPy arrays for easier processing.
- Passes data to the `getForecast` function for prediction.

```python
def getRcsiFcsForecast(graphData):
    # Validate 'fcsGraph' data
    if not graphData.get('fcsGraph') or any(
        entry.get('fcs') is None or entry.get('fcsHigh') is None or entry.get('fcsLow') is None
        for entry in graphData['fcsGraph']
    ):
        fcs_pred = None
    else:
        fcs_data = np.array([[entry['fcs'], entry['fcsHigh'], entry['fcsLow']] for entry in graphData['fcsGraph']])
        fcs_pred = getForecast(fcs_data)

    # Validate 'rcsiGraph' data
    if not graphData.get('rcsiGraph') or any(
        entry.get('rcsi') is None or entry.get('rcsiHigh') is None or entry.get('rcsiLow') is None
        for entry in graphData['rcsiGraph']
    ):
        rcsi_pred = None
    else:
        rcsi_data = np.array([[entry['rcsi'], entry['rcsiHigh'], entry['rcsiLow']] for entry in graphData['rcsiGraph']])
        rcsi_pred = getForecast(rcsi_data)

    return fcs_pred, rcsi_pred
```

---

#### Predicting: **`getForecast`**

This function performs the core forecasting:

1. **Initialize ESNWrapper**:
    - Sets up an Echo State Network (ESN) model from the `scan` library.
2. **Train and Predict**:
    - Trains the model with the input data (`data`) and forecasts the next 90 time steps.
    - Uses the ESN's `train_and_predict` method with parameters:
        - `x_data`: Input data array.
        - `train_sync_steps`: Synchronization steps during training (set to 0).
        - `train_steps`: Number of training steps (length of input data).
        - `pred_steps`: Number of prediction steps (90).

```python
def getForecast(data):
    esn = scan.ESNWrapper()
    esn.create_network(n_seed=0)
    pred_steps = 90

    train_sync_steps = 0
    train_steps = data.shape[0]

    y_pred, y_test = esn.train_and_predict(
        x_data=data, train_sync_steps=train_sync_steps, train_steps=train_steps, pred_steps=pred_steps, w_in_seed=0
    )

    return y_pred
```

---
