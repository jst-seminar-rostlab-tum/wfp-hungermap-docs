---
title: "How to send a single email"
sidebar_label: "How to send a single email"
---

# Sending Emails - Step by Step

Author: `Shaurya Sharma`

This guide walks you through sending an email using the Email Service.

## Prerequisites

1. **Run the Flask Server**  
   - Make sure you have installed all required Python packages (e.g., `Flask`, `pymongo`, `brevo-python`, `jinja2`, `requests`, `python-dotenv`, `premailer`).
   - Ensure you have a proper `.env` file containing `MONGODB_URI`, `MONGODB_DB_EMAIL_SERVICE`, `BREVO_API_KEY`, `BREVO_SENDER_EMAIL`, and `BREVO_SENDER_NAME`.
   - Start the server, typically with:
     ```bash
     python app.py
     ```
   - The server should be running on the configured port (default is `5001` in the code).

2. **Confirm Your Topic Setup**  
   - Before sending an email, you need a valid topic in your database (`topics` collection). 
   - Each topic typically references a template.
   - If your topic supports sub-options (e.g., countries), ensure corresponding `reports` documents exist.

3. **Test the API**  
   - You can use cURL, Postman, or any other REST client to interact with the endpoints.

---

## Sending a Single Email

Follow these steps to send an email to **one** recipient.

### Step 1: Prepare the Request

- **Endpoint**: `/send-email`  
- **Method**: `POST`

### Step 2: Craft the JSON Body

Below is an example request body:
```json
{
  "email": "recipient@example.com",
  "topic_id": "64a888c0aaaabbbbccccddd1",
  "pdf_links": [
    "https://example.com/documents/file1.pdf",
    "https://example.com/documents/file2.pdf"
  ]
}
```

- **email**: The recipient’s email address.
- **topic_id**: The `_id` of the topic from your MongoDB `topics` collection.
- **pdf_links** *(optional)*: A list of URLs for PDFs to attach to the email.

### Step 3: Send the Request

Use any HTTP client tool. For example, with **cURL**:

```bash
curl -X POST \
  http://localhost:5001/send-email \
  -H "Content-Type: application/json" \
  -d '{
    "email": "recipient@example.com",
    "topic_id": "64a888c0aaaabbbbccccddd1",
    "pdf_links": [
      "https://example.com/documents/file1.pdf",
      "https://example.com/documents/file2.pdf"
    ]
  }'
```

### Step 4: Check the Response

A successful response typically returns:
```json
{
  "message": "Email sent successfully"
}
```
If an error occurs (e.g., invalid `topic_id`, PDF download failed), you will receive an error with a corresponding status code (e.g., `400` or `500`).

### Step 5: Verify the Email Delivery

- Check your email inbox (the address specified in the request).
- Review the `email_reports` collection for a log entry indicating success or failure.

---

## Troubleshooting

- **Topic or Template Not Found**: Ensure `topic_id` is valid and that the topic references an existing `template` (if required).
- **PDF Links Failing**: Verify the PDF URLs are correct and publicly accessible.
- **Email Not Delivered**: Double-check your **Brevo API Key**, **sender email**, and that your domain is verified in Brevo.
- **Server Errors (500)**: Inspect the Flask logs or the console output for Python traceback details.