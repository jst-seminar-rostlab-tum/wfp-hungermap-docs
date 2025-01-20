---
title: Topics
sidebar_label: Topics
sidebar_position: 3
---

# Topics API

Author: `Shaurya Sharma`

**Endpoint**: `/topics`  
**Method**: **GET**

Fetches a list of all topics in the database, optionally including any related `reports` if `options_available` is true.

- Each topic document may have an attached `template_id` used to generate the actual email content.
- If `options_available` is false, `options` will be an empty list.

## Response Example
```json
[
  {
    "topic_id": "64a888c0aaaabbbbccccddd1",
    "topic_name": "daily_reports",
    "topic_display_name": "Daily Reports",
    "topic_description": "Provides daily stats and information",
    "is_country_selectable": true,
    "options": [
      {
        "report_id": "64a888c0aaaabbbbcccc1111",
        "report_name": "Kenya"
      },
      {
        "report_id": "64a888c0aaaabbbbcccc2222",
        "report_name": "Uganda"
      }
    ]
  },
  {
    "topic_id": "64a888c0aaaabbbbccccddd2",
    "topic_name": "newsletter",
    "topic_display_name": "Monthly Newsletter",
    "topic_description": "Updates on general topics",
    "is_country_selectable": false,
    "options": []
  }
]
```

## Error Cases
- **500** if a server or database exception occurs.