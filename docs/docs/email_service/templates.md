# Templates

Author: `Shaurya Sharma`

The Email Service uses HTML and CSS templates stored in `email_templates/`. Each template consists of:
- An `.html` file (e.g., `button_template.html`)
- A corresponding `_styles.css` file (e.g., `button_template_styles.css`)

These templates are uploaded into MongoDB via `upload_templates.py`. The script:
- Reads each `.html` file.
- Checks if a corresponding `_styles.css` file exists.
- Generates and stores a hash to detect changes.

## How Templates Work
1. **Storage**: Templates are inserted into the `templates` collection, alongside fields such as `template_name`, `content`, and `css`.
2. **Retrieval**: When sending an email, the service fetches the template from the database using `template_id`.
3. **Rendering**: The HTML is rendered with Jinja2, then CSS is optionally inlined for better email client support using a library such as `premailer`.

## Updating or Adding Templates

Note: Please ensure the each template name(Template file name) is unique.

To update existing templates or add new ones:
1. Place the `.html` and `_styles.css` files (with matching base filenames) into the `email_templates/` directory.
2. Run the `upload_templates.py` script.
   - If the hashes differ, the content is updated.
   - Otherwise, a new template document is created if it doesn’t exist.

## Example Use Case
- An admin wants a new layout for newsletters. They create `newsletter_template.html` and `newsletter_template_styles.css`.
- They run `python upload_templates.py`.
- The new template is now stored in MongoDB and can be referenced when generating email content for the new newsletter.
