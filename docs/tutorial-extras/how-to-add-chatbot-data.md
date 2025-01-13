# How to Add More Data to the Chatbot

The chatbot's data pipeline consists of three main components:
1. **Parsers**: Scripts that fetch and format data from the WFP Hungermap API
2. **Uploaders**: Scripts that upload the parsed data to the MongoDB database
3. **Upload All**: A utility file to run all parsers and uploaders in sequence

## Adding New Data Through Parsers

The parsers fetch the available data from the WFP Hungermap API. The acquired
raw data is saved in two formats in the `src/assets/` directory:
- A cleaned and structured CSV file for database upload
- A raw JSON file containing the complete API response for debugging and reference

### Available Parsers
Located in `src/parsers/`:
- `api_country_reports.py`: Parses country reports
- `api_country_general_data.py`: Parses general country data
- `api_country_additional_data.py`: Parses additional country metrics
- `api_country_pdc.py`: Parses PDC (Pacific Disaster Center) data
- `api_country_conflict.py`: Parses conflict data
- `api_country_ipc.py`: Parses IPC (Integrated Food Security Phase Classification) data
- `api_iso3_data.py`: Parses ISO3 country code mappings
- `api_yearly_review.py`: Parses yearly review reports

### Creating a New Parser

1. Create a new parser file in `src/parsers/`:
```python
# api_your_data.py

import csv
import json
import os
import requests

API_ENDPOINT = "https://api.hungermapdata.org/v2/your-endpoint"
SRC = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

def parse_your_data():
    # Setup output directory
    output_dir = os.path.join(SRC, "assets", "your_data_type")
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)

    # Define paths and clean existing files
    csv_path = os.path.join(output_dir, "your_data.csv")
    json_path = os.path.join(output_dir, "your_data.json")
    
    # Your parsing logic goes here...
    
if __name__ == "__main__":
    parse_your_data()
```

2. Once you have added the file to `src/parsers/`, you can run the `run_all_parsers.py` 
script which will automatically detect and upload all files from the parsers folder.

## Adding Data Through Uploaders

Once the data is parsed, it needs to be uploaded to the MongoDB database.

### Available Uploaders
Located in `src/data_uploaders/`:

**Country Data Uploaders:**
- `db_upload_country_additional_data.py`: Additional country metrics and features
- `db_upload_country_and_region_data.py`: Basic country and region information
- `db_upload_country_conflict_data.py`: Conflict events and statistics
- `db_upload_country_economy_data.py`: Economic indicators
- `db_upload_country_fcs_data.py`: Food Consumption Score data
- `db_upload_country_ipc_data.py`: Integrated Food Security Phase Classification
- `db_upload_country_news.py`: Country-specific news articles
- `db_upload_country_nutrition_data.py`: Nutrition statistics
- `db_upload_country_pdc_data.py`: Pacific Disaster Center events
- `db_upload_country_population_data.py`: Population statistics
- `db_upload_country_rcsi_data.py`: Reduced Coping Strategy Index data

**Report Uploaders:**
- `db_upload_reports_data.py`: Country-specific reports
- `db_upload_yearly_reports_data.py`: Annual review reports

### Creating a New Uploader

1. Create a new uploader in `src/data_uploaders/`:
```python
# db_upload_your_data.py

from ..database_prep.db_vectorizing import insert_or_update_all_embeddings
from ..utils.csv_utils import read_csv_data
from ..utils.db_utils import load_db_config, upload_chatbot_data

if __name__ == "__main__":
    data = read_csv_data("path/to/your_data.csv")
    # Logic for data processing and upload preparation goes here...
    upload_chatbot_data(processed_data)
    insert_or_update_all_embeddings()
```

2. Once you have added the file to `src/data_uploaders/`, you can run the `run_all_uploaders.py` 
script which will automatically detect and upload all files from the data uploaders folder.