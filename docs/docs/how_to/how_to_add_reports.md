# How to Add New Reports for the Report Chatting Functionality

Author: `Ahmet Selman Güclü`

## Adding Reports

To include new reports, follow these steps:

1. Locate the `REPORTS` list in `reports_utils.py` file. Each report entry in the list must contain the country name and the PDF URL. For example:
   ```
   REPORTS = [
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/hti-summary.pdf",
           "country_name": "Haiti",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/hnd-summary.pdf",
           "country_name": "Honduras",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/yem-summary.pdf",
           "country_name": "Yemen",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/col-summary.pdf",
           "country_name": "Colombia",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/dom-summary.pdf",
           "country_name": "Dominican Republic",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/ecu-summary.pdf",
           "country_name": "Ecuador",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/slv-summary.pdf",
           "country_name": "El Salvador",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/gtm-summary.pdf",
           "country_name": "Guatemala",
       },
       {
           "url": "https://static.hungermapdata.org/insight-reports/latest/irq-summary.pdf",
           "country_name": "Iraq",
       },
   ]
   ```
2. Add the new report entry with its respective country name and PDF URL to this list.
3. Run the script `report_chatting/upload_all_reports.py` to process the new document. This script will:
   * Extract the document’s data and summary.
   * Upload the extracted information to the `report_chatting` collection in the main database.

### Demonstration Example

Suppose you want to add a report for "Kenya" with the following URL: `https://static.hungermapdata.org/insight-reports/latest/ken-summary.pdf`.

* Add the entry to `REPORTS`:
  ```
  {
      "url": "https://static.hungermapdata.org/insight-reports/latest/ken-summary.pdf",
      "country_name": "Kenya",
  }
  ```
* Execute the script:
  ```
  python report_chatting/upload_all_reports.py
  ```

After running the script, the Kenya report will be included in the functionality.

## Adding a New Type of Report

While the steps above are sufficient for adding standard reports, introducing a completely new type of report may require additional changes:

1. **Front-End Identification:**
   * The current system identifies reports using their country name. For a new report type, consider implementing an enhanced identification mechanism on the front end.
2. **Data Storage:**
   * Evaluate if the new report type requires separate storage or categorization in the database for better management.
