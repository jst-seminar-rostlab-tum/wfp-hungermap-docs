---
title: Send Email
sidebar_label: Send Email
sidebar_position: 4
---

# Send Email API

Author: `Shaurya Sharma`

**Endpoint**: `/send-email`  
**Method**: **POST**

Sends a single email to one recipient for a specific `topic_id`.

1. Validate `topic_id` and fetch the relevant template.
2. Generate HTML content via `email_generator.py`.
3. Download PDFs (if any) and encode them for attachment.
4. Use Brevo’s Transactional API to send the email.
5. Insert a record into an `email_reports` or equivalent logging collection.

## Request Body
```json
{
  "email": "user@example.org",
  "topic_id": "64a888c0aaaabbbbccccddd1",
  "pdf_links": [
    "https://example.com/documents/file1.pdf",
    "https://example.com/documents/file2.pdf"
  ]
}
```
- `email`: The address of the recipient.
- `topic_id`: MongoDB `_id` of the topic that dictates which template is used.
- `pdf_links`: An optional array of URLs to be downloaded and attached as PDFs.

## Success Response
```json
{
  "message": "Email sent successfully"
}
```

## Failure Cases
- **400** if `topic_id` is invalid.
- **400** if any `pdf_links` cannot be downloaded.
- **500** if there is an error generating the email or sending via Brevo.