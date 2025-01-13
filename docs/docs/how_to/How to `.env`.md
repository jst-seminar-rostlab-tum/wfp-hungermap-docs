# How to Configure and Extend a `.env` File?

Author: `Muhammed Emre Bayraktaroglu`

In this guide, we will focus on the essential environment variables the back-end of the HungerMap application requires and how to extend the `.env` file to include additional variables for local development.


## Essential Environment Variables
Ensure you have the following environment variables set in your `.env` file:
```plaintext
MONGODB_DB=db_NAME
MONGODB_COLLECTION=chatbot_data
MONGODB_DB_EMAIL_SERVICE=db_EMAIL_SERVICE
MONGODB_DB_PROMPT_COMPRESSOR_CACHE=db_PROMPT_COMPRESSOR_CACHE
OPENAI_API_KEY=your_openai_api_key
```

### Explanation of Variables
- `MONGODB_DB`: The name of the MongoDB database.
- `MONGODB_COLLECTION`: The name of the MongoDB collection for the chatbot data.
- `MONGODB_DB_EMAIL_SERVICE`: The name of the MongoDB collection for the email service.
- `MONGODB_DB_PROMPT_COMPRESSOR_CACHE`: The name of the MongoDB collection for the prompt compressor cache.
- `OPENAI_API_KEY`: Your OpenAI API key for accessing the OpenAI services.

### Adding Additional Variables and Extending the `.env` File
To extend the `.env` file for local development, you can add additional variables as needed, such as API endpoints, local database URIs, or any other configurations specific to your development environment.

For example:
```plaintext
# Additional variables for local development
# Local MongoDB URI
MONGODB_URI=mongodb://localhost:27017
# Local OpenAI API key (if different from the production key)
OPENAI_API_KEY_LOCAL=your_local_openai_api_key
# Any other custom variables
CUSTOM_VAR=custom_value
```

By organizing your environment variables in the `.env` file, you can easily manage and switch between different configurations for development, testing, and production environments.

## Example: Using the `.env` Variables in Python
Here's an example of how you can use these environment variables in a Python script:
```python
import os
from dotenv import load_dotenv

# Load environment variables from .env file
load_dotenv()

# Accessing environment variables
mongodb_db = os.getenv('MONGODB_DB')
mongodb_collection = os.getenv('MONGODB_COLLECTION')
...

``` 


