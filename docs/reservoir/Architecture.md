### Architecture

#### Pipeline Overview

The reservoir computing system comprises several components designed to streamline data preprocessing, prediction, and
storage. Below is an outline of the architecture:

1. **Data Ingestion**
    - Historical FCS and RCSI data for each country are fetched daily.
    - New data is used for the predictions, ensuring the model operates on the latest information.

2. **Preprocessing**
    - Data is structured for compatibility with the reservoir computing model.

3. **Reservoir Computing Model**
    - Built using the `scan` package from DLR.
    - Generates predictions for FCS and RCSI values, along with their bounds, for the next 90 days.

4. **Prediction Update and Storage**
    - Predictions are rounded to the nearest integer.
    - Data is saved to the database, with unique keys (`adm0_code`, `prediction_date`) ensuring accurate updates and
      insertions.

5. **API Endpoint for Predictions**
    - Predictions are exposed through an API endpoint that provides structured data for frontend visualization.

#### Diagram

```
[ Data Ingestion ] ---> [ Preprocessing ] ---> [ Reservoir Computing Model ] ---> [ Database ] ---> [ API Endpoint ]
```