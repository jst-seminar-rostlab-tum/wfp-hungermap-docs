---
title: Daily Reports
sidebar_label: Daily Reports
---

# Available Daily Reports API

**Endpoint**: `/daily-reports/countries`  
**Method**: **GET**

Fetches a list of all countries which have a daily report saved in the database

## Response Example
```json
['Colombia', 'Dominican Republic', 'Ecuador', 'Guatemala', 'Honduras', 'Haiti', 'Iraq', 'El Salvador', 'Yemen']
```

Error Cases
	•	500 if a server or database exception occurs.

