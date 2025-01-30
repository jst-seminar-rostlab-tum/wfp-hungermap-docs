# Database Overview

Author: `Cansu Moran`

### **`prediction_data`**

| **Column Name**            | **Data Type** | **Description**                                                                                          | **Key**       |
|-----------------------------|---------------|----------------------------------------------------------------------------------------------------------|---------------|
| `fcs_score`                | INT           | Predicted Food Consumption Score (FCS) value.                                                           | -             |
| `fcs_score_upper_bound`    | INT           | Upper bound for the predicted FCS value.                                                                | -             |
| `fcs_score_lower_bound`    | INT           | Lower bound for the predicted FCS value.                                                                | -             |
| `fcs_people`               | INT           | Number of people affected based on FCS predictions.                                                     | -             |
| `fcs_people_upper_bound`   | INT           | Upper bound of the population affected based on FCS predictions.                                        | -             |
| `fcs_people_lower_bound`   | INT           | Lower bound of the population affected based on FCS predictions.                                        | -             |
| `rcsi_score`               | INT           | Predicted Reduced Coping Strategy Index (RCSI) value.                                                   | -             |
| `rcsi_score_upper_bound`   | INT           | Upper bound for the predicted RCSI value.                                                               | -             |
| `rcsi_score_lower_bound`   | INT           | Lower bound for the predicted RCSI value.                                                               | -             |
| `rcsi_people`              | INT           | Number of people affected based on RCSI predictions.                                                    | -             |
| `rcsi_people_upper_bound`  | INT           | Upper bound of the population affected based on RCSI predictions.                                       | -             |
| `rcsi_people_lower_bound`  | INT           | Lower bound of the population affected based on RCSI predictions.                                       | -             |
| `adm0_code`                | VARCHAR(10)   | Administrative level 0 code (country code).                                                             | Primary Key   |
| `prediction_date`          | DATE          | The date of the prediction.                                                                             | Primary Key   |
| `update_date`              | TIMESTAMP     | Timestamp when the prediction data was last updated.                                                    | -             |

---

### Queries Used

#### 1. Get all unique country codes:
```sql
SELECT DISTINCT adm0_code
FROM datav_fcs_rcsi_aggregation_bootstrap;
```

- **Purpose**: Retrieves all unique country codes (`adm0_code`).
- **Use**: Lists countries for which data is available.

#### 2. Fetch FCS/RCSI graph data:
```sql
SELECT adm0_code, sum(fcs_people) as fcs, sum(fcs_people_upper_bound) as fcs_upper_bound,
       sum(fcs_people_lower_bound) as fcs_lower_bound, sum(rcsi_people) as rcsi,
       sum(rcsi_people_upper_bound) as rcsi_upper_bound, sum(rcsi_people_lower_bound) as rcsi_lower_bound,
       date1 as 'x'
FROM (
    SELECT adm0_code, fcs_people, fcs_people_upper_bound, fcs_people_lower_bound,
           rcsi_people, rcsi_people_upper_bound, rcsi_people_lower_bound,
           CONCAT_WS('-', year, lpad(month, 2, 0), lpad(day + 0, 2, 0)) as 'date1'
    FROM datav_fcs_rcsi_aggregation_bootstrap
    WHERE adm0_code = %s
) t1
WHERE date1 BETWEEN DATE_SUB((SELECT CONCAT_WS('-', year, lpad(month, 2, 0), lpad(day + 0, 2, 0))
                               FROM datav_fcs_rcsi_aggregation_bootstrap
                               WHERE adm0_code = %s AND data_type = 'ACTUAL DATA'
                               ORDER BY date2 DESC LIMIT 1), INTERVAL 3 MONTH)
      AND (SELECT CONCAT_WS('-', year, lpad(month, 2, 0), lpad(day + 0, 2, 0))
           FROM datav_fcs_rcsi_aggregation_bootstrap
           WHERE adm0_code = %s AND data_type = 'ACTUAL DATA'
           ORDER BY date2 DESC LIMIT 1)
GROUP BY adm0_code, date1
ORDER BY date1;
```

- **Purpose**: Retrieves recorded FCS and RCSI data, aggregated over a 3-month period.
- **Use**: Provides the training dataset for the forecast model.


#### 3. Fetch FCS/RCSI prediction data:
```sql
SELECT fcs_score, fcs_score_upper_bound, fcs_score_lower_bound, fcs_people,
       fcs_people_upper_bound, fcs_people_lower_bound, rcsi_score, rcsi_score_upper_bound,
       rcsi_score_lower_bound, rcsi_people, rcsi_people_upper_bound, rcsi_people_lower_bound,
       adm0_code, prediction_date, update_date
FROM prediction_data
WHERE adm0_code = %s
ORDER BY prediction_date;
```

- **Purpose**: Retrieves forecasted FCS and RCSI data for a specific country (`adm0_code`).

#### 4. Upsert prediction data:
```sql
INSERT INTO `prediction_data` (`adm0_code`, `prediction_date`, `fcs_score`, `rcsi_score`, ...)
VALUES (%s, %s, %s, %s, ...)
ON DUPLICATE KEY UPDATE `fcs_score` = VALUES(`fcs_score`), `rcsi_score` = VALUES(`rcsi_score`), ...;
```
- **Purpose**: Saves forecast results to the database.
