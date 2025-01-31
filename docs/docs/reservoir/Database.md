## Database Overview

Author: `Cansu Moran`

### **`prediction_data`**

| **Column Name**           | **Data Type** | **Description**                                                   | **Key**     |
|---------------------------|---------------|-------------------------------------------------------------------|-------------|
| `fcs_people`              | INT           | Number of people affected based on FCS predictions.               | -           |
| `fcs_people_upper_bound`  | INT           | Upper bound of the population affected based on FCS predictions.  | -           |
| `fcs_people_lower_bound`  | INT           | Lower bound of the population affected based on FCS predictions.  | -           |
| `rcsi_people`             | INT           | Number of people affected based on RCSI predictions.              | -           |
| `rcsi_people_upper_bound` | INT           | Upper bound of the population affected based on RCSI predictions. | -           |
| `rcsi_people_lower_bound` | INT           | Lower bound of the population affected based on RCSI predictions. | -           |
| `adm0_code`               | VARCHAR(10)   | Administrative level 0 code (country code).                       | Primary Key |
| `prediction_date`         | DATE          | The date of the prediction.                                       | Primary Key |
| `update_date`             | TIMESTAMP     | Timestamp when the prediction data was last updated.              | -           |

---

Note: The combination of `prediction_date` and `adm0_code` serves as the **Primary Key** for the `prediction_data` table, ensuring each prediction is uniquely identified by its country code and date.

### Queries Used:

#### 1. Get all unique country codes:

```sql
SELECT DISTINCT adm0_code
FROM datav_fcs_rcsi_aggregation_bootstrap;
```

- **Purpose**: Retrieves all unique country codes (`adm0_code`).
- **Use**: Lists countries for which data is available.

#### 2. Fetch FCS/RCSI graph data:

```sql
  SELECT {admField} sum (fcs_people) as fcs, sum (fcs_people_upper_bound) as fcs_upper_bound, sum (fcs_people_lower_bound) as fcs_lower_bound, sum (rcsi_people) as rcsi, sum (rcsi_people_upper_bound) as rcsi_upper_bound, sum (rcsi_people_lower_bound) as rcsi_lower_bound, date1 as 'x'
  FROM (
      SELECT {admField} fcs_people, fcs_people_upper_bound, fcs_people_lower_bound, rcsi_people, rcsi_people_upper_bound, rcsi_people_lower_bound, CONCAT_WS('-', year, lpad(month, 2, 0), lpad(day + 0, 2, 0)) as 'date1'
      FROM {data_table}
      WHERE adm0_code = %s
      ) t1
  WHERE date1 BETWEEN DATE_SUB(
      (SELECT CONCAT_WS('-'
      , year
      , lpad(month
      , 2
      , 0)
      , lpad(day + 0
      , 2
      , 0)) as 'date2'
      FROM {data_table} WHERE adm0_code = %s {typeFilter}
      GROUP BY date2 ORDER BY date2 DESC LIMIT 1)
      , INTERVAL 3 MONTH)
    AND
      (SELECT CONCAT_WS('-'
      , year
      , lpad(month
      , 2
      , 0)
      , lpad(day + 0
      , 2
      , 0)) as 'date2'
      FROM {data_table} WHERE adm0_code = %s {typeFilter}
      GROUP BY date2 ORDER BY date2 DESC LIMIT 1)
  GROUP BY {admField} date1
  ORDER BY date1
```

- **Purpose**: Retrieves recorded FCS and RCSI data, aggregated over a 3-month period.
- **Use**: Provides the training dataset for the forecast model.

#### 3. Fetch FCS/RCSI prediction data:

```sql
          SELECT (fcs_people)              as fcs,
                 (fcs_people_upper_bound)  as fcs_upper_bound,
                 (fcs_people_lower_bound)  as fcs_lower_bound,
                 (rcsi_people)             as rcsi,
                 (rcsi_people_upper_bound) as rcsi_upper_bound,
                 (rcsi_people_lower_bound) as rcsi_lower_bound,
                 adm0_code,
                 prediction_date,
                 update_date
          FROM {prediction_table}
          WHERE adm0_code = {adm0_code}
            AND prediction_date BETWEEN (
              SELECT DATE_SUB(MAX (prediction_date)
              , INTERVAL 90 DAY)
              FROM {prediction_table}
              WHERE adm0_code = {adm0_code}
              )
            AND (
              SELECT MAX (prediction_date)
              FROM {prediction_table}
              WHERE adm0_code = {adm0_code}
              )
          ORDER BY prediction_date
```

- **Purpose**: Retrieves forecasted FCS and RCSI data for a specific country (`adm0_code`).

#### 4. Upsert prediction data:

Generated through the following function:

```python
    def upsertPredictions(prediction_row):
        """
          Generates an SQL query to insert or update a prediction row in the database.
    
          Args:
              prediction_row (dict): A dictionary representing a prediction row, where keys are column names.
    
          Returns:
              str: The SQL query string.
    
          Raises:
              ValueError: If `prediction_row` is not a dictionary or required unique columns are missing.
          """
    
        if not isinstance(prediction_row, dict):
            raise ValueError("Predictions must be a dictionary.")
    
        # Columns used for checking existence
        unique_columns = ["adm0_code", "prediction_date"]
        if not all(col in prediction_row for col in unique_columns):
            raise ValueError(f"Missing required unique columns: {unique_columns}")
    
        columns = list(prediction_row.keys())
        values = list(prediction_row.values())
    
        values_clause = ", ".join(
            f"'{value}'" if isinstance(value, str) else str(value)
            for value in values
        )
    
        update_clause = ", ".join(
            "`{}` = {}".format(
                col,
                f"'{prediction_row[col]}'" if isinstance(prediction_row[col], str) else prediction_row[col]
            )
            for col in columns
            if col not in unique_columns
        )
    
        columns_clause = ", ".join(f"`{col}`" for col in columns)
    
        query = (
            f"INSERT INTO `{prediction_table}` ({columns_clause}) "
            f"VALUES ({values_clause}) "
            f"ON DUPLICATE KEY UPDATE {update_clause};"
        )
    
        return query
```

- **Purpose**: Saves forecast results to the database.
