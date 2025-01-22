---
title: Send Bulk Emails
sidebar_label: Send Bulk Emails
sidebar_position: 5
---

# Send Bulk Email API

Author: `Shaurya Sharma`

**Endpoint**: `/send-bulk-emails`  
**Method**: **POST**

Sends emails to multiple recipients at once (for instance, sending a daily or weekly digest to all subscribers of a topic).

- Internally, `send_bulk_emails_endpoint.py` uses similar logic to `send_email_endpoint.py` but iterates through multiple subscribers.
- Logging is done per api call in the `email_reports` collection.

## Request Body
Example usage might require either a `topic_id` or a list of multiple recipients:
```json
{
  "topic_id": "64a888c0aaaabbbbccccddd1"
}
```
Gather subscribers for a topic, generate content, and send each email in a loop.

## Success Response
```json
{
  "message": "Bulk email sending started",
  "report_id": "64a888c0aaaabbbbccccmmm4"
}
```

## Error Handling
- Similar to the single `send-email` endpoint, except that errors could happen partway through sending multiple emails. If email could not be send to a user, the system will continue sending the rest and add this information in the email report.