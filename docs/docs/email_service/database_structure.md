---
title: Database Structure
sidebar_label: Database Structure
sidebar_position: 3
---

# Database Structure

Author: `Shaurya Sharma`

Below is a overview of the collections (tables) used by this Email Service, alongside their relationships. 

- The database is hosted with [MongoDB Atlas](https://www.mongodb.com/products/platform/atlas-database) on AWS.

- The database(MongoDB) although a No-SQL database, is structured to support the relational nature of the data i.e. SQL-like relationships to ensure smooth migration to a SQL database in the future.

- The database is called `email_service` located within `WFP-cluster` on MongoDB Atlas.

An schema diagram is shown here:

![Database Schema](./assets/database_schema.svg)

## Collections

### 1. Subscribers
Holds subscriber details.
- **_id** (ObjectId)  
- **name** (string)  
- **organization** (string)  
- **email** (string)  
- **created_at** (timestamp)

### 2. Subscriptions
Captures which subscriber is subscribed to which topic.
- **_id** (ObjectId)  
- **subscriber_id** (ObjectId) → references **Subscribers**  
- **topic_id** (ObjectId) → references **Topics**  
- **option_ids** (string[]) → references **Reports** if options are enabled  
- **created_at**, **updated_at** (timestamp)

### 3. Topics
Defines each unique topic or category of emails.
- **_id** (ObjectId)  
- **topic_name** (string)  
- **topic_description** (string)  
- **template_id** (ObjectId) → references **Templates**  
- **topic_display_name** (string)  
- **options_available** (bool)  

### 4. Reports
Usually contains links to daily or periodic reports for a given topic (e.g., countries).
- **_id** (ObjectId)  
- **report_name** (string)  
- **topic_id** (ObjectId) → references **Topics**  
- **report_link** (string)

### 5. Templates
Email templates for each type of email.
- **_id** (ObjectId)  
- **template_name** (string)  
- **content** (string; HTML)  
- **css** (string; CSS inlined later)  
- **hash** / **css_hash** (string; used to detect changes)  
- **created_at**, **updated_at** (timestamp)

### 6. email_reports (Optional Logging)
Stores logs of sent emails.
- **_id** (ObjectId)
- **type** (string; e.g., "single" or "bulk")
- **email** (string)
- **topic_id** (ObjectId)
- **status** (string; e.g., "success" or "failed")
- **created_at** (timestamp)
- **error** (string; optional error message)

This schema supports flexible subscription models (with or without sub-options), dynamic email templates, and logging of email deliveries.
