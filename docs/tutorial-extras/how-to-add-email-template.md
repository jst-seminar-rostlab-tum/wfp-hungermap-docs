# How to Add a New Email Template

## Template Structure

Email templates are located in the `src/email_service/email_templates` directory and consist of two files:
- An HTML file (e.g., `template_name.html`)
- A CSS file (e.g., `template_name_styles.css`)

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
