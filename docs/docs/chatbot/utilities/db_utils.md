# Database utils

**Author:** `Marius Moldovan`

The **Database Utils module** provides a streamlined approach for managing data in a MongoDB collection.

## Overview

1. **`load_db_config`**: Dynamically loads database connection settings from environment variables.
2. **`upload_chatbot_data`**: Simplifies the process of inserting the chatbot data into the MongoDB collection.
3. **`clear_data`**: Provides a quick way to delete all documents in the collection.

## Classes and Functions

### Function: `load_db_config(field_to_embed="data")`

Load database configuration from environment variables.

*Params*:
- **field_to_embed** (`str`): The field in the document to create an embedding for. Defaults to "data".

*Returns*:
- `tuple`: A tuple containing:
    - **db_uri** (`str`): MongoDB connection string.
    - **db_name** (`str`): Name of the database.
    - **db_collection_name** (`str`): Name of the collection.
    - **field_to_embed** (`str`): Name of the field to embed.

*Raises*:
- `ValueError`: If any of the required environment variables are missing.

---

### Function: `upload_chatbot_data(data)`

Upload data to chatbot Collection MongoDB.

Updates each item's `data` field to a string containing `data_description` and `data_labels`.

*Params*:
- **data** (`List[dict]`): A list of dictionaries representing the data to upload. Each dictionary must include:
    - **data_description** (`str`): A description of the data.
    - **data_labels** (`List[str]`): A list of labels associated with the data.

*Returns*:
- `None`

*Behavior*:
- Updates each item's `data` field to a string containing `data_description` and `data_labels`.
- Prints the `_id` of each successfully inserted document.


---

### Function: `clear_data()`

Clear **all** data from the chatbot Collection MongoDB.

*Params*:
- `None`

*Returns*:
- `None`
