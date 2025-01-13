### **Implementation**

#### **Overview**

This document provides detailed information about the implementation of the forecasting pipeline. It explains the
code structure, the main functions, how the endpoints are triggered, and the process for generating and updating
predictions.

---

### **Core Implementation Details**

#### **1. Data Flow and Prediction Workflow**

1. **Daily Updates with Cron Jobs**
    - A cron job triggers the `updatePredictions()` function every morning.
    - Predictions are calculated for the next 90 days based on the latest available data.
    - Updated predictions are stored in the database, replacing old entries if they exist or inserting new ones if they
      don’t.

2. **Frontend Endpoint**
    - An endpoint provides the predictions to the frontend in JSON format:
      ```python
      @app.get("/<adm0_id>/predictions.json")
      def get_prediction_data(event, adm0Id=None):
          return getCountryPredictions(adm0Id)
      ```
    - The frontend uses this data to display FCS and RCSI graphs.

---

#### **2. Key Functions**

##### **getCountryFcsRcsiGraphData(adm0_id)**

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

##### **getCountryFcsRcsiPredictionData(adm0_id)**

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

##### **getCountryPredictions(adm0_id)**

Processes prediction data into a JSON format for the frontend.

- **Inputs:**  
  `adm0_id` (country identifier).
- **Outputs:**  
  JSON-formatted predictions for FCS and RCSI graphs.

```python
def getCountryPredictions(adm0_id):
    try:
        fcs_rcsi_prediction_data = getCountryFcsRcsiPredictionData(adm0_id)
        data = {
            'fcsGraph': [
                {'x': item['prediction_date'], 'fcs': int(item['fcs']), 'fcsHigh': int(item['fcs_upper_bound']),
                 'fcsLow': int(item['fcs_lower_bound']), 'update_date': item['update_date']}
                for item in fcs_rcsi_prediction_data
            ],
            'rcsiGraph': [
                {'x': item['prediction_date'], 'rcsi': int(item['rcsi']), 'rcsiHigh': int(item['rcsi_upper_bound']),
                 'rcsiLow': int(item['rcsi_lower_bound']), 'update_date': item['update_date']}
                for item in fcs_rcsi_prediction_data
            ]
        }
        return data
    except Exception as e:
        return json.dumps({'error': 'Error retrieving data', 'details': str(e)})
```

---

##### **updatePredictions()**

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
                    {'x': item['x'], 'fcs': int(item['fcs']), 'fcsHigh': int(item['fcs_upper_bound']),
                     'fcsLow': int(item['fcs_lower_bound'])}
                    for item in fcs_rcsi_graph_data
                ],
                'rcsiGraph': [
                    {'x': item['x'], 'rcsi': int(item['rcsi']), 'rcsiHigh': int(item['rcsi_upper_bound']),
                     'rcsiLow': int(item['rcsi_lower_bound'])}
                    for item in fcs_rcsi_graph_data
                ]
            }

            # Get predictions for FCS and RCSI
            fcs_pred, rcsi_pred = forecast_model.getRcsiFcsForecast(data)

            # Get the latest date in the data for date incrementation
            latest_date_str = max(item['x'] for item in fcs_rcsi_graph_data)
            latest_date = datetime.strptime(latest_date_str, '%Y-%m-%d')

            # Iterate and save predictions
            for i in range(len(fcs_pred)):
                prediction_date = (latest_date + timedelta(days=i)).strftime('%Y-%m-%d')

                prediction_row = {
                    'prediction_date': prediction_date,
                    'update_date': today_date,
                    'adm0_code': adm0_id,
                    'fcs': round(fcs_pred[i][0]),
                    'fcs_high': round(fcs_pred[i][1]),
                    'fcs_low': round(fcs_pred[i][2]),
                    'rcsi': round(rcsi_pred[i][0]),
                    'rcsi_high': round(rcsi_pred[i][1]),
                    'rcsi_low': round(rcsi_pred[i][2])
                }

                db_repo.upsert(queries.upsertPredictions(prediction_row))

        success_message = "Predictions updated successfully for all countries."
        print(success_message)
        return json.dumps(success_message)

    except Exception as e:
        print(f"Error updating predictions: {e}")
        return json.dumps({'error': 'Error retrieving data', 'details': str(e)})
```

---

##### **forecast_model.getRcsiFcsForecast(data)**

This function takes the historical data as input and generates predictions. More details can be found below in the **Forecast Model Implementation**.

---

#### **3. How to Use the Endpoints**

- **Frontend Endpoint:**  
  Use `GET /<adm0_id>/predictions.json` to retrieve predictions for a country.

- **Cron Job Endpoint:**  
  The cron job triggers `updatePredictions()` daily.

---

#### **4. Forecast Model Implementation**


##### Data Preprocessing: **`getRcsiFcsForecast`**

This function retrieves and forecasts both **FCS** and **RCSI** data:

- It extracts `fcsGraph` and `rcsiGraph` data from the input `graphData`.
- Converts data to NumPy arrays for easier processing.
- Passes data to the `getForecast` function for prediction.

```python
def getRcsiFcsForecast(graphData):

    fcs_data = np.array([[entry['fcs'], entry['fcsHigh'], entry['fcsLow']] for entry in graphData['fcsGraph']])
    rcsi_data = np.array([[entry['rcsi'], entry['rcsiHigh'], entry['rcsiLow']] for entry in graphData['rcsiGraph']])

    fcs_pred = getForecast(fcs_data)
    rcsi_pred = getForecast(rcsi_data)

    return fcs_pred, rcsi_pred
```
---

##### Predicting: **`getForecast`**

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
