# PDF Attachments

The PDF Attachments Module allows for a reliable attachment of PDFs to emails using the Brevo API.

## How it Works
1. **Receive PDF links:** A list of PDF URLs is received 
2. **Download PDFs:** From every received URL a PDF is fetched
3. **Encode PDFs:** The received PDFs are encoded into Base64
4. **Create Attachment Object:** A Brevo Attachment Object is created from every encoded PDF
5. **Send Attachment:** A List of all Attachment Objects is sent in the email 

## Classes and Functions

**Function**: `process_attachments(pdf_links)`:

*Params*:
- **pdf_links** (`List[str]`): List of PDF URLs.

*Returns*:
- `List[SendSmtpEmailAttachment]` or `None`: List of attachment objects or None if no links are provided.

