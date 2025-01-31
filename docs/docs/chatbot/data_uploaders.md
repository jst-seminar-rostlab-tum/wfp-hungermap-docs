---
id: data-uploaders
title: Data Uploaders
slug: /data-uploaders
---
# Data Uploaders

**Author:** `Muhammed Emre Bayraktaroglu`

This documentation provides an overview of various data uploaders used in the HungerMap application. Data uploaders are essential components that facilitate the ingestion of data (as collections) into the database, enabling the chatbot to utilize them through `Vector Search`

---

## Overview

Data Uploaders provide:
- Conversion of CSV files, resulting from parsers, with their respective headers and sorting to ready-to-use dictionaries.
- Uploading of these dictionaries into MongoDB collections for access and retrieval.
- Updating of existing collections with new data, ensuring data integrity and consistency.

---

## Requirements and Setup

Before using the various Data Uploaders, ensure you have the following:
- Python 3.8+
- Installed dependencies from `requirements.txt`, including `pymongo`, `dotenv`, `openai`, and other required libraries.
- A running MongoDB instance (local or cloud) and credentials/connection strings.

### Environment Variables
Set up the following environment variables in your system or in a `.env` file:
- **MONGODB_URI**: Connection string for your MongoDB database.
- **MONGODB_DB**: The name of the MongoDB database.
- **MONGODB_COLLECTION**: The name of the MongoDB collection for the chatbot data.
- **OPENAI_API_KEY**: Your OpenAI API key for accessing the OpenAI services.

Example `.env` file:
```plaintext
MONGODB_URI=url_to_your_mongodb_instance
MONGODB_DB=db_NAME
MONGODB_COLLECTION=chatbot_data
OPENAI_API_KEY=your_openai_api_key
```

---

## Overview of Data Uploaders

| Uploader Name | Description | Fields of Interest | Specialized Fields | Name of Script |
|--------------|-------------|-------------------|-------------------|----------------|
| **Yearly Reports Uploader** | Uploads yearly reports data into the MongoDB collection. | `year`, `title`, `url` | no special field | `db_upload_yearly_reports_data.py` |
| **Reports Data Uploader** | Uploads specific country reports URL in the Database (if there is a report for this certain country). | `country_code`, `country_name`, `report_url` | no special fields | `db_upload_reports_data.py` |
| **RSCI Data Uploader** | Uploads RSCI data into the MongoDB collection. | `country_id`, `country_name`, `rsci_graph` | no special fields | `db_upload_country_rsci_data.py` |
| **Country Population Data Uploader** | Uploads country population data into the MongoDB collection. | `country_id`, `population`, `population_source` | no special fields | `db_upload_country_population_data.py` |
| **Country PDC Data Uploader** | Uploads country PDC data into the MongoDB collection. | `type`, `country_name`, `occurrences` **Inside `occurrences`**: `id`, `type`, `severity_id`, `hazard_name`, `country`, `create_date`, `last_update` | The field `occurrences` is an array containing all incidents defined by `type`. | `db_upload_country_pdc_data.py` |
| **Country Nutrition Data Uploader** | Uploads country nutrition and food inflation data into the MongoDB collection. | `country_code`, `country_name`, `nutrition`, `food_inflation_graph` | no special fields | `db_upload_country_nutrition_data.py` |
| **Country News Data Uploader** | Uploads country news data into the MongoDB collection. | `country_id`, `country_name`, `news` | no special fields | `db_upload_country_news.py` |
| **Country IPC Data Uploader** | Uploads country IPC data into the MongoDB collection and also generates a global summary document. | `country_id`, `country_name`, `ipc_population_affected`, `ipc_percent`, `source`, `analysis_period`, `date_of_analysis`, `phase_4_plus_population`, `phase_5_population` | **Global Document**: A specialized document summarizing global IPC statistics (e.g., extremes, total affected). | `db_upload_country_ipc_data.py` |
| **Country FCS Data Uploader** | Uploads country FCS data into the MongoDB collection. | `country_name`, `country_id`, `fcs_graph`, `fcs_minus_1`, `fcs_minus_3`, `fcs` | no special fields | `db_upload_country_fcs_data.py` |
| **Country Economy Data Uploader** | Uploads country economy data into the MongoDB collection. | `country_name`, `currency_exchange_graph`, `balance_of_trade_graph`, `headline_inflation_graph` | no special fields | `db_upload_country_economy_data.py` |
| **Country Conflict Data Uploader** | Uploads country conflict events data into the MongoDB collection. | `event_type`, `country_name`, `occurrences` **Inside `occurrences`**: `region`, `count`, `latitude`, `longitude` | Removes existing records (same `event_type` & `country_name`) before insertion. | `db_upload_country_conflict_data.py` |
| **Country & Region Data Uploader** | Uploads region data per country into the MongoDB collection. | `country_id`, `country_name`, `regions`, `region_codes` | Groups multiple regions under a single country's entry. | `db_upload_country_and_region_data.py` |
| **Country Additional Data Uploader** | Uploads additional data about country regions (features, codes, FCS, rCSI, etc.) into the MongoDB collection. | `country_id`, `country_name`, `regions_data` | **Inside `regions_data`**: `Code`, `Name`, `fcs`, `rcsi`, `fcsGraph` | `db_upload_country_additional_data.py` |
| **Report Chatting Country Content Uploader** | Uploads parsed country report content into MongoDB. | `document_name`, `country_name`, `document_title`, `data_labels`, `data_description`, `document_page_number`, `data` | no special fields | `db_upload_report_chatting_data.py` |
| **Report Chatting Year-in-Review Content Uploader** | Uploads parsed year-in-review report content into MongoDB. | `document_name`, `year`, `document_title`, `data_labels`, `data_description`, `document_page_number`, `data` | Content is extracted and structured from PDF based on headings | `db_upload_year_in_review_report_data.py` |
| **Report Chatting Summary Uploader** | Uploads AI-generated summaries for both country reports and year-in-review reports. | For country reports: <br/> `document_name`, `country_name`, `data_labels`, `data_description`, `data` <br/><br/> For year-in-review: <br/> `document_name`, `year`, `data_labels`, `data_description`, `data` | Summaries are AI-generated from combined report content | `db_upload_report_summary.py` |


These fields are used to structure the data in a consistent format, while also keeping the necessary context for each collection.
Each document also includes `document_name`, `data_labels`, `data_description` for internal indexing and labeling in the database.

---

## Functions

The Data Uploaders have common functions that are used across different scripts. They are named slightly different to fit the specific uploader's requirements and implement specific sorting or filtering logic.
Moreover, every uploader is using the function `load_db_config()` from `db_utils.py` to load the MongoDB configuration and connection settings.

### Common Functions

**Function**: `process_[speific_data_uploader]_data(data)`

Processes raw CSV data into dictonary format for the specific data uploader. It includes sorting and filtering tasks for the data.

*Arguments*:
- `data` (List[Dict[str,str]]): Corresponding raw CSV data for the specific data uploader.

*Returns*:
- `List[Dict[str,str]]`: Processed data in dictionary format.

---

### Specific Functions

*Affecting following scripts: `db_upload_yearly_reports_data.py`, `db_upload_reports_data.py`, `db_upload_country_pdc_data.py`, `db_upload_country_conflict_data.py`*

**Function**: `upload_[specific_data_uploader]_data(data)`

Uploads processed data into the MongoDB collection. It also performs checks to avoid duplicate entries and updates existing records in case a re-upload is imminent.

*Arguments*:
- `data` (List[Dict[str,str]]): Processed data in dictionary format.

*Returns*:
- No return value.
- Prints status messages for successful uploads or updates.

---
*Affecting following scripts: `db_upload_country_ipc_data.py`*

***Function***: `create_global_document(data)`

This function creates a specialized global document, or "global summary", based on IPC data for various countries.
It does the following:

1. **Aggregates global statistics** by calculating total IPC-affected population, total Phase 4+ and Phase 5 populations across countries. It also computes the average IPC-afflicted population
2. **Finds** the country with the highest IPC-affected population, the country with the highest Phase 4+ and Phase 5 population, but also the lowest affected ones.
3. **Builds a list** with countries that have no Phase 5 populations and counts the number of countries that have at least one Phase 4+ population.

*Arguments*:

- `data` (List[Dict[str,str]]): Raw CSV data for IPC data.

*Returns*:

- List[Dict]: The constructed list of dictionaries containing global, regional, and extremes insights.

---

*Affecting following scripts: `db_upload_report_chatting_data.py`, `db_upload_report_summary.py`, `db_upload_year_in_review_report_data.py`*

**Function**: `upload_report_chatting_data(data)`, `upload_report_summary(data)`, `upload_year_in_review_data(data)`

Uploads the processed report data (content and summary) into MongoDB for chatbot report chatting functionality. These functions work together through the `upload_all_reports.py` script, which orchestrates:

1. PDF parsing (using either `report_chatting_data_parser.py` or `year_in_review_parser.py`)
2. Content extraction and structuring
3. Database upload of content
4. AI summary generation
5. Database upload of summary
6. Embedding generation and updates for vector search functionality

*Arguments*:
- `data` (List[Dict[str,str]]): Processed data for uploading.

*Returns*:
- No return value.
- Prints status messages for successful uploads.

---

## What Happens Under the Hood?

Generally, a `main` function is mostly responsible for orchestrating the data processing and uploading tasks.

1. **Data Processing**: The uploader reads raw CSV data. In some scripts, it is updating the data with the parsers before reading the CSV files.
2. **Data Conversion**: The raw data is converted into a dictionary format using the `process_[specific_data_uploader]_data` function and saved in a variable.
3. **Data Uploading**: In case a script contains an `upload_[specific_data_uploader]_data` function, the processed data is uploaded to the MongoDB collection through this function. Else, a common `upload_chatbot_data`from `db_utils.py` is used to facilitate the upload. Conflict and PDC data for example use country code conversion in further break down data into country-specific documents.
4. **Data Embedding Insertion and Update**: At the end, the newly added documents get their own embeddings and the update of all embeddings are initiated. This is necessary for the Vector Search.

---

## Use of Libraries

The Data Uploaders rely on the following libraries:
- [**Location Utilities**](docs/chatbot/utilities/location_utilities): For country code conversion and country name retrieval. It is used to provide human-readable location names instead of coordinates.
- [**Country Utilities**](docs/chatbot/utilities/country_utilities): For country name retrieval based on country IDs. It is used to provide human-readable country names based on their IDs.
- **CSV Utilities**: For reading and processing CSV files. It is used to parse and process raw data from CSV files. (TODO: Add link to CSV Utilities)
- **Database Utilities**: This utility provides functions for loading MongoDB configurations and and uploading data to the database. It also provides a function to delete all records. (TODO: Add link to Database Utilities)

---

## Conclusion
The various Data Uploaders are essentially processing the raw data provided from the APIs through the API Parsers.

- **Sorted and well-formed data sets** as a result of thorough filtering and processing
- **Building** a data-rich collection database, which is the heart of the chatbot
