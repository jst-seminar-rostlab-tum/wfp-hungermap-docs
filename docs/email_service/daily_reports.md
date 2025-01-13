# Daily Reports

The **Daily Reports** Module is designed to manage reports in a MongoDB database. It fetches, uploads, updates daily reports, 
and ensures that users are only subscribed to available reports.

## Overview
The following functionalities are available:
1. **upload_daily_reports_to_db**: Synchronizes the database with the latest daily reports available through the HungerMap API.
2. **check_if_topic_exists**: Ensures that the countries in the database are up-to-date by removing daily_reports and unsubscribing affected users.
3. **get_countries_with_daily_reports**: Retrieves a list of all countries that currently have daily reports stored in the database.

## Classes and Functions

**Function**: `upload_daily_reports_to_db()`:

Uploads or Updates all available daily reports to the MongoDB database. 

*Params*:
- `None`

*Returns*:
- `None`

*Workflow*:
1. Fetches available reports from `https://static.hungermapdata.org/insight-reports/latest/country.json`
2. Calls `check_if_topic_exists` to ensure that the database does not include any countries not present in the fetched reports. 
3. Uploads all daily reports to the database. If a country already has a daily report, the link is updated.

*Example Usage*:
```Python
    mock_report = {
        "report_name": "mock_country",
        "topic_id": ObjectId("6748573a86a5fede76848baa"),
        "report_link": "https://mock_report.com",
    }
    # Add to the database
    db.reports.insert_one(mock_report)
    # Get mock report ID
    mock_report = db.reports.find_one({"report_name": "mock_country"})
    mock_report_id = mock_report["_id"]
    # add the sample report id as string to the subscriber with 676052cf0fb082415b597a1c
    db.subscriptions.update_one(
        {"subscriber_id": ObjectId("676052cf0fb082415b597a1c")},
        {"$push": {"option_ids": str(mock_id)}},
    )
    upload_daily_reports_to_db()
```

---

**Function**: `check_if_topic_exists(country_arr)`:

Removes all daily reports from the database which have countries not included in `country_arr`. It also unsubscribes all from the country

*Params*:
- **country_arr** (`List[str]`): List of countries from the json

*Returns*:
- `None`

---

**Function**: `get_countries_with_daily_reports()`:

Returns all countries which have a daily report.

*Params*:
- `None`

*Returns*:
- `List[str]` or `Dict[str, str]`: A list of country names with daily reports or and error message if an exception occurs.

