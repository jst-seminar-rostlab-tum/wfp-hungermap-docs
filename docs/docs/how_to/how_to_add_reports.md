# How to Add New Reports for the Report Chatting Functionality

Author: `Ahmet Selman Güclü`

## Types of Reports

The system currently supports two types of reports:
1. Country Reports - Reports specific to individual countries
2. Year in Review Reports - Annual global reports

## Adding Country Reports

To include new country reports, follow these steps:

1. Locate the `COUNTRY_REPORTS` list in `reports_utils.py`. Each report entry must contain the country name and PDF URL. For example:
   ```python
   {
       "url": "https://static.hungermapdata.org/insight-reports/latest/hti-summary.pdf",
       "country_name": "Haiti",
   }
   ```

2. Add the new report entry to the `COUNTRY_REPORTS` list.

3. Run the script `report_chatting/upload_all_reports.py` to process the new document. This script will:
   * Extract the document's data and summary
   * Upload the extracted information to the `report_chatting` collection in the main database

### Example: Adding a Country Report

Suppose you want to add a report for "Kenya":

1. Add this entry to `COUNTRY_REPORTS`:
   ```python
   {
       "url": "https://static.hungermapdata.org/insight-reports/latest/ken-summary.pdf",
       "country_name": "Kenya",
   }
   ```

2. Execute:
   ```bash
   python -m src.report_chatting.upload_all_reports
   ```

## Adding Year in Review Reports

To add new year-in-review reports:

1. Locate the `YEAR_IN_REVIEW_REPORTS` list in `reports_utils.py`. Each entry must contain the year and PDF URL. For example:
   ```python
   {
       "url": "https://static.hungermapdata.org/year-in-review/2023/Global.pdf",
       "year": 2023,
   }
   ```

2. Add the new report entry to the `YEAR_IN_REVIEW_REPORTS` list.

3. Run the upload script as described above.

### Example: Adding a Year in Review Report

To add a new year in review report:

1. Add this entry to `YEAR_IN_REVIEW_REPORTS`:
   ```python
   {
       "url": "https://static.hungermapdata.org/year-in-review/2024/Global.pdf",
       "year": 2024,
   }
   ```

2. Execute:
   ```bash
   python -m src.report_chatting.upload_all_reports
   ```

## Adding a New Type of Report

When introducing a completely new type of report beyond country reports and year-in-review reports:

1. **Code Updates:**
   * Add a new constant list in `reports_utils.py` to store the new report type

2. **Data Storage:**
    * Evaluate if the new report type requires separate storage or categorization in the database
    * Update database schemas if necessary with new parser and upload scripts and include those in the `report_chatting/upload_all_reports.py` script
    * Run `python -m src.report_chatting.upload_all_reports` to upload the new report type

3. **Front-End Identification:**
   * Add the new report type to the `IReportContext` in `src/domain/entities/chatbot/Chatbot.ts` and use it when creating a new chat and sending message queries to the backend.
   ```javascript
        export interface IReportContext {
            type: 'country' | 'year_in_review';
            value: string;
        }
   ```

4. **System Prompt Adjustments:**
   * Adjust `ai_model.py`, `db_similarity_search.py`, `app.py` to support the new report type.