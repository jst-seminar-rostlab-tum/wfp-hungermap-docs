# Changing the Forecasting Model

Author: `Cansu Moran`

Follow these steps to update the forecasting model in the project:

---

## **1. Locate the Forecasting Module**
- Identify the main file handling the current forecasting model, `forecast_model.py` under `forecast` directory.

---

## **2. Add the New Forecasting Model**
- Install any dependencies required for the new model (e.g., via `pip`):
  ```bash
  pip install <new-model-library>
  ```
- Create a new file for the model or modify the existing one:
  ```bash
  /forecast/<new_model_name>.py
  ```
- Implement the new model logic. Ensure it adheres to the expected input and output format used in the project.

---

## **3. Update the Model Selection Logic**
- Update `updatePredictions()` function in `index.py` under `forecast` directory so that the data is passed to your selected model. 
  ```python
  fcs_pred, rcsi_pred = new_forecast_model.predict(data)
  ```

### Key Points:
- `fcsGraph` contains data points for **Food Consumption Score (FCS)**, including upper and lower bounds.  
- `rcsiGraph` contains data points for **Reduced Coping Strategies Index (rCSI)**, also with upper and lower bounds.

Your new model's function should be able to handle this `data` format.

---

## **4: Ensure Proper Output Format**
The function should return predictions as `fcs_pred` and `rcsi_pred`. The values are retrieved from the prediction results in the following structure:
```python
{
    'fcs': round(fcs_pred[i][0]),
    'fcs_high': round(fcs_pred[i][1]),
    'fcs_low': round(fcs_pred[i][2]),
    'rcsi': round(rcsi_pred[i][0]),
    'rcsi_high': round(rcsi_pred[i][1]),
    'rcsi_low': round(rcsi_pred[i][2])
}
```
---