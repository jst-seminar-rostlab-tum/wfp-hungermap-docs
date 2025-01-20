# Data Retrievals

**Author:** `Marius Moldovan`

The **Data Retrieval** Module allows for the data retrieval from the HungerMap API and saves the fetched Data locally in CSV and JSON files.
This prepares the data for the upload to the database.

## Overview

Running one of the modules will generally result in the following:
1. Creation of the folders, CSV and JSON files
2. The Data will be fetched from the hungermapdata.org API
3. The fetched data will be stored in the CSV and JSON file. During this process some fields might be renamed or removed if they irrelevant.

All data is saved in the folder `assets/` and further subfolders.

## Usage and configuration
All retrieval functions can be executed by simply running the corresponding Python file. For Methods with parameters, a list of all available 
country IDs is automatically passed to the function insuring no countries are missed. The list of countries is sourced from the file
`assets/country_data/country_ids.csv`.

To modify the Endpoint from which the data is fetched, the relevant parameter can be changed in the corresponding Python file.

## Python Scripts and Functions

### General Country Data

**Name of Python Script**: `api_country_general_data.py`

---

**Function:** `parse_api_country_general_data(country_ids):`

Fetches the data from the Endpoint `https://api.hungermapdata.org/v2/adm0/{}/countryData.json` and
saves the relevant data to `country_data/countries_general.csv` and `country_data/countries_general.json`.

*Params*:
- **country_ids** (`List[str]`): List of country ids to retrieve data for.

*Returns*:
- `None`

*CSV File Structure:*:
- **adm0_code** (`int`): The country ID.
- **population** (`float`): The population of the country in Millions.
- **populationSource** (`str`): Source of the population data.
- **fcs** (`float`): Food Consumption Score.
- **fcsMinus1** (`float`): FCS minus 1 metric.
- **fcsMinus3** (`float`): FCS minus 3 metric.
- **importDependency** (`float `): Import dependency percentage.
- **fcsGraph** (`list[dict]`): FCS Graph containing fields: x, fcs, fcsHigh, fcsLow.
- **rcsiGraph** (`list[dict]`): RCSI Graph containing fields: x, rcsi, rcsiHigh, rcsiLow.
- **news** (`list[dict]`): News articles related to the country containing fields: author, title, description, url, image, publishedAt, content.

---

### Additional Country Data

**Name of Python Script**: `api_country_additional_data.py`

---

**Function:** `parse_api_country_additional_data(country_ids):`

Fetches the data from the Endpoint `https://api.hungermapdata.org/v2/adm0/{}/adm1data.json` and 
saves the relevant data to `country_data/countries_additional.csv` and `country_data/countries_additional.json`


*Params*:
- **country_ids** (`List[str]`): List of country ids to retrieve data for.

*Returns*:
- `None`

*CSV File Structure:*:
- **adm0_code** (`int`): The Country ID.
- **hc_transform** (`dict`): Highcharts transformation data.
- **features** (`list[dict]`): List of geographic features containing fields: type, geometry, properties, id.

---

### Conflicts Data

**Name of Python Script**: `api_country_conflict.py`

---

**Function:** `parse_conflicts():`

Fetches the data from the Endpoint `https://api.hungermapdata.org/v2/acled.geojson` and
saves the relevant Data to `conflict_data/conflicts.csv` and `conflict_data/conflicts.json`

*Params*:
- `None`

*Returns*:
- `None`

*CSV File Structure:*:
- **event_type** (`str`): Type of the event.
- **region** (`str`): Geographical region where the event occurred.
- **count** (`int`): Number of events.
- **latitude** (`float`): Latitude coordinate of the event location.
- **longitude** (`float`): Longitude coordinate of the event location.

---

**Function:** `parse_conflicts_per_country(parsed_csv):`

This function preprocesses the parsed CSV file to group the conflicts by country and saves the result in the new CSV files `conflict_data/countries/{country_name}.csv`

*Params*:
- **parsed_csv** (`str`): Path to the parsed csv file.

*Returns*:
- `None`

*CSV File Structure:*:
- **event_type** (`str`): Type of the event.
- **region** (`str`): Geographical region where the event occurred.
- **count** (`int`): Number of events.
- **latitude** (`float`): Latitude coordinate of the event location.
- **longitude** (`float`): Longitude coordinate of the event location.

---

**Function:** `parse_all_locations(path_to_countries_folder):`

A function to print all locations in the given in csv files for each country according to coordinates.


*Params*:
- **path_to_countries_folder** (`str`): Path to the countries folder.

*Returns*:
- `None`

---

### IPC Data

**Name of Python Script**: `api_country_ipc.py`

---

**Function:** `parse_api_ipc():`

Fetches the data from the Endpoint `https://api.hungermapdata.org/v2/ipc.json` and
saves the relevant Data to `ipc_data/ipc.csv` and `ipc_data/ipc.json`

*Params*:
- `None`

*Returns*:
- `None`

*CSV File Structure:*:
- **adm0_code** (`int`): The country ID.
- **adm0_name** (`str`): The name of the country.
- **ipc_population_affected** (`int`): The number of people affected by IPC.
- **ipc_percent** (`float`): The percentage of the population affected by IPC.
- **source** (`str`): Source of the IPC data.
- **analysis_period** (`str`): The period during which the analysis was conducted.
- **date_of_analysis** (`str`): The date when the analysis was performed.
- **phase_4_plus_population** (`int`): Population in Phase 4 and above.
- **phase_5_population** (`int`): Population in Phase 5.

---

### PDC Data

**Name of Python Script**: `api_country_pdc.py`

---

**Function:** `parse_pdc():`

Fetches the data from the Endpoint `https://api.hungermapdata.org/v2/pdc.json` and
saves the relevant Data to `pdc_data/pdc.csv` and `pdc_data/pdc.json`

*Params*:
- `None`

*Returns*:
- `None`

*CSV File Structure:*:
- **id** (`int`): The unique hazard ID.
- **type** (`str`): The type of hazard.
- **severityId** (`str`): The severity level ID.
- **hazardName** (`str`): The name of the hazard.
- **latitude** (`float`): The latitude coordinate.
- **longitude** (`float`): The longitude coordinate.
- **createDate** (`str`): The creation date and time.
- **lastUpdate** (`str`): The last update date and time.

---

### Country Reports

**Name of Python Script**: `api_country_reports.py`

---

**Function:** `parse_reports():`

Fetches the data from the Endpoint `https://static.hungermapdata.org/insight-reports/latest/country.json` and
saves the relevant Data to `reports_data/reports.csv` and `reports_data/reports.json`

*Params*:
- `None`

*Returns*:
- `None`

*CSV File Structure:*:
- **country_name** (`str`): The name of the country.
- **country_code** (`str`): The ISO3 code of the country.
- **report_url** (`str`): The URL of the summary report.

---

### ISO3 Data

**Name of Python Script**: `api_iso3_data.py`

---

**Function:** `parse_api_iso3(country_codes):`

Fetches the data from the Endpoint `https://api.hungermapdata.org/v2/iso3/{}/countryIso3Data.json` and
saves the relevant Data to `reports_data/reports.csv` and `reports_data/reports.json`


*Params*:
- **country_codes** (`List[str]`): List of country ISO3 codes to retrieve data for.

*Returns*:
- `None`

*CSV File Structure:*:
- **countryCode** (`str`): The country ISO3 Code.
- **nutrition** (`dict`): Nutrition data containing fields: wasting, stunting and source
- **currencyExchangeGraph** (`dict`): Currency exchange graph containing fields: name, source, updated, data.
- **balanceOfTradeGraph** (`dict`): Balance of trade graph containing fields: data.
- **inflationGraphs** (`dict`): Headline and food inflation graphs containing fields: headline, food.

---

### Yearly Reviews

**Name of Python Script**: `api_yearly_review.py`

---

**Function:** `parse_yearly_reports():`

Fetches the data from the Endpoint `https://static.hungermapdata.org/year-in-review/config.json` and
saves the relevant Data to `yearly_reports/yearly_reports.csv` and `yearly_reports/yearly_reports.json`

*Params*:
- `None`

*Returns*:
- `None`

*CSV File Structure:*:
- **label** (`str`): Name of the report.
- **URL** (`str`): Link to the report.

---

### Running all Data Retrievals

**Function:** `run_all_parsers():`

Runs all Data Retrievals inside the folder `src/parsers`.

*Params*:
- `None`

*Returns*:
- `None`

