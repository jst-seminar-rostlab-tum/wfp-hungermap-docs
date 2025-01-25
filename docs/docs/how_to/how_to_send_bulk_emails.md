---
title: "How to send bulk emails"
sidebar_label: "How to send bulk emails"
---

# Sending Bulk Emails - Step by Step

Author: `Shaurya Sharma`

This guide walks you through sending bulk emails using the Email Service.



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

## Sending Bulk Emails

Use the bulk endpoint to send emails to multiple subscribers at once, typically used for daily or weekly distributions.

### Step 1: Prepare the Request

- **Endpoint**: `/send-bulk-emails`  
- **Method**: `POST`

### Step 2: Craft the JSON Body

The exact format depends on your design, but a common example is:

```json
{
  "topic_id": "64a888c0aaaabbbbccccddd1"
}
```
The service will look up all `subscriptions` for the given `topic_id`, gather those subscriber emails, then send out an email to each of them.

### Step 3: Send the Request

Using **cURL** as an example:

```bash
curl -X POST \
  http://localhost:5001/send-bulk-emails \
  -H "Content-Type: application/json" \
  -d '{
    "topic_id": "64a888c0aaaabbbbccccddd1"
  }'
```

### Step 4: Check the Response

A successful bulk send typically returns:
```json
{
  "message": "Bulk email sending started",
  "report_id": "64a888c0aaaabbbbccccmmm4"
}
```
If errors occur partway through sending, the service may continue for other recipients or halt, depending on how error handling is implemented.

### Step 5: Verify the Bulk Delivery

- Each email is typically logged in the database (e.g., `email_reports` collection) with a status of `"success"` or `"failed"`.
- Confirm that multiple recipients have indeed received the email.

---

## Troubleshooting

- **Topic or Template Not Found**: Ensure `topic_id` is valid and that the topic references an existing `template` (if required).
- **PDF Links Failing**: Verify the PDF URLs are correct and publicly accessible.
- **Email Not Delivered**: Double-check your **Brevo API Key**, **sender email**, and that your domain is verified in Brevo.
- **Server Errors (500)**: Inspect the Flask logs or the console output for Python traceback details.
