# How to Add a New Email Template

Author: `Georgi Peev`

This documentation covers the process of adding a new email template to the Email Service. Email templates are used to structure and style the cores of the emails sent to users. By following the steps outlined below, you can create a new template and upload it to the MongoDB database for use in the Email Service.

## Environment Setup

Before working with email templates:

1. Create and activate a Python virtual environment:
```bash
# Create venv
python -m venv venv

# Activate venv
# On Windows:
venv\Scripts\activate
# On Unix/MacOS:
source venv/bin/activate
```

2. Install required dependencies:
```bash
pip install -r requirements.txt
```

---

## Template Structure

Email templates are located in the `src/email_service/email_templates` directory and consist of two files:

| File Type | Example | Purpose |
|-----------|---------|----------|
| HTML Template | `template_name.html` | Main email content and structure |
| CSS Styles | `template_name_styles.css` | Styling for the template |

## Add a New Template

1. Create the HTML Template
   - Create a new HTML file in the `src/email_service/email_templates` directory
   - Use the following structure:
   ```html
   <!DOCTYPE html>
   <html>
     <head>
       <meta charset="UTF-8" />
       <title>{{title}}</title>
       {{css}}
     </head>
     <body>
       <div class="email-container">
         <div class="header">
           <img src="https://res.cloudinary.com/ddfjjf7xd/image/upload/v1733000256/wfp_logo_j7lefy.png" alt="Logo"/>
         </div>
         <div class="content">
           <!-- Your template content here -->
           <!-- Use {{variable_name}} for dynamic content -->
         </div>
         <div class="footer">
           <p>Don't want to receive these emails? <a href="{{unsubscribe_link}}">Unsubscribe here</a></p>
         </div>
       </div>
     </body>
   </html>
   ```

2. Create the CSS Styles
   - Create a new CSS file with the same base name as your HTML file
   - The CSS will be automatically injected into the template.
   ```css
   body {
     margin: 0;
     padding: 0;
     background-color: #f4f4f4;
     font-family: Arial, sans-serif;
   }
   
   .email-container {
     max-width: 600px;
     margin: auto;
     background-color: #ffffff;
     border: 1px solid #dddddd;
   }
   
   .header {
     background-color: #009edb;
     padding: 20px;
     text-align: center;
   }
   
   .content {
     padding: 20px;
     background-color: #e6ebed;
   }
   
   /* Add your custom styles here */
   ```

3. Available template variables:
- `{{title}}`: Email subject/title
- `{{topic_name}}`: Name of the topic
- `{{unsubscribe_link}}`: Unsubscribe URL
- `{{css}}`: Automatically injected styles (do not modify)

4. Upload to MongoDB:
- Ensure your `.env` file contains the required MongoDB configuration:
  ```python
  MONGODB_URI="your_mongodb_uri"
  MONGODB_DB_EMAIL_SERVICE="your_db_name"
  ```
- Upload templates
  ```bash
  python src/email_service/upload_templates.py
  ```
