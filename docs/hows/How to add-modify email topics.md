## How to Add or Modify Email Topics in MongoDB

Email topics are stored in the `email_service` collection within the WFP database. To add or modify a topic, follow these steps:

### Prerequisites:
1. You need access to MongoDB, either through a MongoDB UI (e.g., MongoDB Compass) or through a command-line interface.
2. You need the `template_id`, which corresponds to the object ID (`_id` field) of a template related to the topic. You can find this in the `email_service > templates` collection.

### Steps to Add/Modify an Email Topic:

1. **Navigate to the `email_service` collection**: 
   Inside the WFP database, go to the `email_service` collection. This is where all email topics are stored.


2. **Add a New Topic or Modify an Existing One**:
   - To **add a new topic**: Insert a new document with the following fields:
     - `topic_name`: A unique identifier for the topic (e.g., `test_daily_reports`).
     - `template_id`: The object ID of the related template. You can find this ID in the `email_service > templates` collection under the `_id` field.
     - `topic_display_name`: A human-readable name for the topic (e.g., `"Country Summary Report(s)"`). This will be the name that is included in the emails and the dropdown options during subscription.
     - `topic_description`: A short description of the topic (e.g., `"Daily Reports"`).
     - `options_available`: A boolean value indicating whether the topic has different subscribable options (e.g., `true` or `false`).

     Example document for a new topic:
     ```json
     {
       "topic_name": "daily_reports",
       "template_id": "000000000000000000000000",
       "topic_display_name": "Country Summary Report(s)",
       "topic_description": "Daily Reports",
       "options_available": true
     }
     ```

   - To **modify an existing topic**: 
     - Use the `topic_name` to locate the topic.
     - Edit the fields as needed (e.g., changing `template_id`, `topic_display_name`, etc.).
     - In MongoDB Compass, click on the document to edit it. In the CLI, use the `update` or `findAndModify` command.

     Example of a MongoDB update command:
     ```bash
     db.email_service.update(
       { "topic_name": "test_daily_reports" },
       { $set: { "topic_display_name": "Updated Country Summary Report" } }
     )
     ```

3**Understanding the `options_available` Field**:
   - `options_available` is a boolean value that indicates whether users can subscribe to different options within the topic.
     - If `options_available` is **`true`**, it means the topic supports different subscription options (e.g., a user can subscribe to daily reports for specific countries).
     - If `options_available` is **`false`**, it means the topic has a single subscription option, and all users subscribing to the topic will receive the same report (e.g., a Global Report topic).

### Key Points:
- **`template_id`**: The `template_id` refers to the ObjectId of a template from the `email_service > templates` collection. Ensure that the template exists and is correctly linked to the topic.
- **`options_available`**: This should be set to `true` for topics with multiple subscription options (like Daily Reports for different countries), and `false` for topics with a single report (like a Global Report).