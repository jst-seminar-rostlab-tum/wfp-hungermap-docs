---
id: prompt-compressor
title: Prompt Compressor
slug: /prompt-compressor
---

# Prompt Compressor

Author: `Muhammed Emre Bayraktaroglu`

This documentation provides an overview of the **PromptCompressor** module, which is designed to compress (summarize) large input prompts using low-cost Large Language Models, like `gpt-3.5-turbo` and store these compressed prompts in a MongoDB cache. By caching, it can help reduce repeated token usage and thus lower costs. It makes up a crucial part of the chatbot's token optimization strategy.

---

## Overview

The **PromptCompressor**:
- Summarizes longer prompts into concise, essential content while preserving key details.
- Caches the compressed results in a MongoDB collection, avoiding re-computation.
- Provides cost estimation for each compression step (based on the model used).
- Offers different compression models to suit various needs.

---
## Requirements and Setup

Before using the **PromptCompressor**, ensure you have the following:
- Python 3.8+  
- A valid **OpenAI API Key** or other model credentials (as needed by the compressor).
- A running **MongoDB** instance (local or cloud) and credentials/connection strings.

---
## Environment Variables

Configure these environment variables in your system or in a `.env` file:
- **OPENAI_API_KEY**: The API key for your OpenAI (or compatible) LLM.
- **MONGODB_URI**: Connection string for your MongoDB database.
- **MONGODB_DB_PROMPT_COMPRESSOR_CACHE**: The name of the database used to store the compressed prompt cache. (in our example, `prompt_compressor_db`)

Example `.env` file:
```plaintext
OPENAI_API_KEY=your_openai_api_key
MONGODB_URI=url_to_your_mongodb_instance
MONGODB_DB_PROMPT_COMPRESSOR_CACHE=prompt_compressor_db
```
---
## Implementation

### Models
Currently, the **PromptCompressor** supports three models: `gpt-4o`, `o1-mini`, and `gpt-3.5-turbo`. You can add more models as needed.

#### Costs for Different Models

| Model Name | Input Cost | Output Cost |
|------------|------------|-------------|
| GPT-3.5-Turbo | 0.000003 | 0,000006 |
| GPT-4o | 0.0000025 | 0,00001 |
| O1-Mini | 0.000003 | 0,000012 |

Source: **[OpenAI API Reference Pricing](https://platform.openai.com/pricing)**

The costs for the calculations are implemented in the `ModelCosts` class.

**Note**: These are example costs; update them according to your usage and actual model pricing.

---
### Class: PromptCompressor

This class defines the core functionality of the PromptCompressor module. It includes methods for compressing documents, caching compressed prompts, and retrieving them from the cache.

---
#### Constructor
```python
def __init__(self, model_name="microsoft/phi-2"):
    """
    Initialize the Prompt Compressor.

    Args:
        model_name (str): Pre-trained LLM model to be used for compression.
                          Default is "microsoft/phi-2".
    """
    self.model_name = model_name
    self.device = "cpu"
    self.oai_tokenizer = tiktoken.encoding_for_model(Models.GPT_4O)
```
- `model_name` (str): Defines which model to use when compressing your prompts. Default is `microsoft/phi-2`. This variable is for a possible LLMLingua implementation and can be ignored for now.
- `device` (str): Currently set to "cpu", but you can modify it for GPU usage if supported by your environment. This variable is for a possible LLMLingua implementation and can be ignored for now.
- `oai_tokenizer` (Encoding): An encoder/decoder for measuring token length to estimate usage.
- 
---
### Methods

**Function**: `cacheable_item(obj_id, compressed_prompt)`:

Creates a dictionary that can be inserted into MongoDB to store the compressed prompt.

*Arguments*:
-	`obj_id` (int): Unique ID for the document or prompt.
-	`compressed_prompt` (str): The summarized version of the input content.

*Returns:*
- a dictionary containing the compressed prompt and the document id

---
**Function:** `insert_cache(item)`:


Inserts a compressed prompt dictionary into MongoDB.

*Arguments*:
-	`item` (dict): A dictionary containing at least doc_id and compressed_prompt.

*Returns*:
- Nothing. A message is printed if the caching is successful

---
**Function:** `is_cached(obj_id)`:

Checks if a compressed prompt is already cached.

*Arguments*:
- `obj_id` (int): The ID of the document to look up.

*Returns*:
- a boolean value, if the document it already cached

---
**Function:** `get_cached_prompt(obj_id)`

Retrieves a compressed prompt from the MongoDB cache if it exists.

*Arguments*:
- `obj_id` (int): The ID of the document to retrieve.

*Returns*:
- The cached prompt

---
**Function:** `compress_document(content, id_of_document)`

Compresses a larger piece of text content using a chosen model. If the document has been compressed before, it uses the cached version (no additional token usage).

*Arguments*:
- `content` (str): The text content to be compressed.
- `id_of_document` (int): A unique identifier for the document to cache/retrieve.

*Returns*:
- The compressed prompt
- A dictionary containing the number of tokens of the uncompressed prompt, the number of tokens of the compressed prompt, the compression rate, and the cost of the compression

---
### Usage Example

Below is a minimal usage example demonstrating how to instantiate and use the PromptCompressor to compress a document.
```python
from prompt_compressor import PromptCompressor
```
#### Initialize compressor
```python
compressor = PromptCompressor()
```
#### Example content to compress
```python
content = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur lobortis..."
```


#### A unique identifier for the document to cache/retrieve
```python
id_of_document = "unique_document_id"

compressed_prompt, info_vector, success = compressor.compress_document(content, id_of_document)

if success:
    print("Compression successful!")
    print("Compressed Prompt:", compressed_prompt)
    print("Info Vector:", info_vector)
else:
    print("Compression failed or the document was already cached.")
```

---
### System Prompt for the Compressor
The Compressor utilizes a low-cost LLM to summarize the content. The system prompt used is as follows:
```plaintext
system_instruction = """
            You are tasked with compressing detailed documents into a
            concise and structured bullet-point summary format.
            Your goal is to retain all essential information,
            including numerical values, dates, locations, and event types,
            while omitting redundant descriptions.
            Follow these instructions carefully:
            1.	Include the following key details for each document:
            •	Document name
            •	Type of event (if applicable)
            •	Country name
            •	Key metrics (e.g., population affected, percentages, analysis periods, phases, or severities)
            •	Event details (e.g., IDs, hazard names, locations, dates of creation and updates)
            •	Data source or labels (if mentioned)
            2.	Format the summary in bullet points for clarity.
            3.	Ensure that no data is lost but avoid repeating the same information multiple times.
            4.	Use clear and concise language to capture the most relevant details.
        """
```
This prompt warrants that the compressor focuses on the most relevant details, ensuring that no data is lost while avoiding redundancy.

---
## What Happens Under the Hood?
1.	**Token Counting**: The content length (in tokens) is measured using the tokenizer.
2.	**Model Selection**: If the document is too large, a more robust (but more expensive) model (GPT-4o) may be used. Otherwise, GPT-3.5-Turbo (or your specified default model) is used.
3.	**Cache Check**: If the document has been compressed before (is_cached), no additional cost is incurred, and the cached summary is retrieved.
4.	**LLM Call**: If not cached, the content is sent to the Large Language Model with a system prompt for bullet-point summarization.
5.	**Summary and Caching**: The response is saved both for immediate usage and for future retrieval (insert_cache).
6.	**Cost Calculation**: The system calculates how many tokens were used (both input and output) and multiplies them by the relevant cost rates.

---
## Conclusion

The PromptCompressor module streamlines the process of summarizing lengthy prompts into manageable chunks and storing them in MongoDB. By leveraging caching, this approach:

- ***Saves*** on repeated LLM usage.
- Quickly ***retrieves*** previously summarized documents.
- ***Reduces*** token consumption and cost.

