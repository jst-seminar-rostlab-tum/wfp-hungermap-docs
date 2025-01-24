---
title: Architecture
sidebar_label: Architecture
slug: /email-service/architecture
sidebar_position: 2
---

# Architecture

Author: `Shaurya Sharma`

This **Email Service** is a Flask application that provides the ability to:
- Subscribe and unsubscribe users
- Retrieve topics (with optional sub-options)
- Send single or bulk emails
- Upload and manage email templates
- Manage and store daily reports, typically associated with topics

Key components:
- **Flask application** (`app.py`) acts as the entry point, registering routes from `routes.py`.
- **MongoDB** is used for storing subscribers, subscriptions, topics, templates, and daily reports data.
- **Brevo** is utilized for sending emails via their Transactional Emails API.

Please read the following documents to get the complete picture of how to run, customize, and expand the Email Service.


## Overview

1. **App Initialization**
   - The Flask application is created in `app.py`.
   - Cross-Origin Resource Sharing (CORS) is configured to allow requests from specified origins.

2. **Routing**
   - Primary routes are defined in `routes.py`.
     - **/subscribe** (POST)  
     - **/unsubscribe** (GET)  
     - **/topics** (GET)  
     - **/send-email** (POST)  
     - **/send-bulk-emails** (POST)  
     - **/daily-reports/countries** (GET)

3. **Database (MongoDB)**
   - The database used is MongoDB, hosted with [MongoDB Atlas](https://www.mongodb.com/products/platform/atlas-database) on AWS.
   - Collections used:
     - `subscribers`, `subscriptions`, `topics`, `templates`, `reports`, and `email_reports`.

4. **Templates & Rendering**
   - Email templates are stored in the `email_templates/` folder.
   - `upload_templates.py` loads or updates these templates into the `templates` collection.

5. **Business Logic**
   - Subscribing/Unsubscribing is handled by `subscribe_endpoint_v2.py` and `unsubscribe_endpoint.py`.
   - Retrieving topics and daily reports involves `get_topics_endpoint.py` and `daily_reports_countries_endpoint.py`.
   - Sending emails is carried out by `send_email_endpoint.py` and `send_bulk_emails_endpoint.py`.

## High-Level Flow

1. A request arrives at one of the routes.
2. The endpoint handler (e.g., `subscribe_endpoint_v2.py`) processes input data, reads/writes to MongoDB.
3. When sending an email, `generate_email_content.py` fetches the relevant template and merges it with data.
4. Brevo (STMP Server as Service, Third Party) handles the actual email delivery.
5. Logs and statuses are saved to the `email_reports` collection or used for debugging as needed.


## Additional Notes

- For environment configuration, ensure your `.env` file includes:
  ```txt
  MONGODB_URI=<your_connection_string>
  MONGODB_DB_EMAIL_SERVICE=email_service
  BREVO_API_KEY=<your_brevo_api_key>
  BREVO_SENDER_EMAIL=<sender_address>
  BREVO_SENDER_NAME=<sender_name>
  ```
- Before deploying, set the environment variables accordingly and secure your secrets.
- Run the server with `python app.py` to serve the Flask app at your configured port.