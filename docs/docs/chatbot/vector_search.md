# Vector search

Author: `Cansu Moran`

This documentation provides an overview of the Vector Search feature implemented in the RAG (Retrieval-Augmented
Generation) chatbot. The vector search enables efficient retrieval of relevant documents from a database
using semantic similarity, making it a core component of the chatbot's information retrieval pipeline.

In this implementation, the query provided by the user is transformed into a vector embedding. This embedding is
compared against precomputed embeddings of the `data` field in the database using cosine similarity to measure how
closely the query matches each document. The most relevant documents are retrieved based on this similarity score.

The retrieved documents are passed to GPT so that the model can base its answers on the most relevant contextual
information. This ensures that responses are not only accurate but also grounded in the provided data, making the system
more reliable and context-aware.

---

## Overview

The **Vector Search** feature:

- Transforms the **user query** into a vector embedding for comparison.
- Uses **cosine similarity** to compare the query embedding against document embeddings.
- Retrieves **relevant documents** using a **threshold-based filtering** mechanism.
- Dynamically reduces the threshold if no documents meet the initial score criteria.

---

## Requirements and Setup

Before using the **Vector Search** feature, ensure you have the following:

- **Python 3.8+**
- **MongoDB Atlas** or a similar MongoDB instance with vector search enabled.
- Installed dependencies from `requirements.txt`

### MongoDB Configuration

The vector search feature requires:

- A MongoDB collection with an index named `vector_index` for storing embeddings.
- A field named `data_embedding` where the embedded representations of documents are stored.

---

### Environment Variables

Set up the following environment variables in your system or in a `.env` file:

- **MONGODB_URI**: Connection string for your MongoDB database.
- **MONGODB_DB**: The name of the MongoDB database.
- **MONGODB_COLLECTION**: The name of the MongoDB collection for the chatbot data.
- **OPENAI_API_KEY**: Your OpenAI API key for accessing the OpenAI services.

Example `.env` file:

```plaintext
MONGODB_URI=url_to_your_mongodb_instance
MONGODB_DB=db_NAME
MONGODB_COLLECTION=chatbot_data
OPENAI_API_KEY=your_openai_api_key
```

---

## Implementation

### Function: `create_embedding`

Generates vector embeddings for text inputs using OpenAI's API.

#### **Definition**

```python
def create_embedding(text: str) -> list[float]:
```

#### **Arguments**

- `text` (str): The input text for which an embedding is created. This can be a document's content during the upload
  process or a user query during a similarity search.

#### **Returns**

- `list[float]`: A vector embedding representing the semantic content of the input text.

#### **Logic**

1. **Query Embedding:** The query is converted into a vector embedding using the `create_embedding` function.
2. **Database Search:** The query embedding is compared to the `data_embedding` field of documents using cosine
   similarity. The MongoDB `$vectorSearch` operator retrieves documents with the highest similarity scores.
3. **Threshold Filtering:** Only documents with a similarity score above the threshold (initially 0.7) are returned. If
   no documents meet the criteria, the threshold is dynamically reduced by 0.1 until documents are found or the
   threshold reaches 0.
4. **Result:** Returns a list of filtered documents with their scores.


1. **Text Input:** Accepts the input text to be embedded.
2. **Embedding Generation:** Calls the OpenAI `text-embedding-ada-002` model to generate an embedding vector for the
   input text.
3. **Output:** Returns the generated embedding as a list of floating-point numbers.

### Function: `similarity_search`

Performs a similarity search in the MongoDB database by embedding the query and comparing it against document embeddings
using cosine similarity.

#### **Definition**

```python
def similarity_search(query: str, limit: int = 5) -> list[dict]:
```

#### **Arguments**

- `query` (str): The search query.
- `limit` (int): The maximum number of results to return. Defaults to 5.

#### **Returns**

- `list[dict]`: A list of similar documents (with respect to the query input) with their scores.

#### **Logic**

1. **Query Embedding:** The query is converted into a vector embedding using the `create_embedding` function.
2. **Database Search:** The query embedding is compared to the `data_embedding` field of documents using cosine
   similarity. The MongoDB `$vectorSearch` operator retrieves documents with the highest similarity scores.
3. **Threshold Filtering:** Only documents with a similarity score above the threshold (initially 0.7) are returned. If
   no documents meet the criteria, the threshold is dynamically reduced by 0.1 until documents are found or the
   threshold reaches 0.
4. **Result:** Returns a list of filtered documents with their scores.

---

## Code Snippets

### Create Embedding

```python
response = (
   openai.embeddings.create(model="text-embedding-ada-002", input=text)
   .data[0]
   .embedding
)
return response
```

### MongoDB `$vectorSearch` Query

The MongoDB aggregation pipeline for vector search:

```python
    filter_condition = {}
    if report_context:
        if report_context["type"] == "country":
            filter_condition = {"country_name": {"$eq": report_context["value"]}}
        elif report_context["type"] == "year_in_review":
            filter_condition = {"year": {"$eq": report_context["value"]}}

    # Perform the similarity search once
    results = db_collection.aggregate(
        [
            {
                "$vectorSearch": {
                    "index": "vector_index",
                    "path": "data_embedding",
                    "queryVector": create_embedding(query),
                    "filter": filter_condition,
                    "numCandidates": 1000,  # Fetch enough candidates
                    "limit": limit,
                }
            },
            {"$addFields": {"score": {"$meta": "vectorSearchScore"}}},
            {"$sort": {"score": -1}},
            {"$project": {"data_embedding": 0}},
        ]
    )
```

The `filter` is specifically used for `report_chatting` collection. For `chatbot_data` searches, it is not relevant.

### Threshold Logic

```python
threshold = 0.7
filtered_results = [doc for doc in all_results if doc["score"] > threshold]

while not filtered_results and threshold >= 0:
    threshold -= 0.1
    filtered_results = [doc for doc in all_results if doc["score"] > threshold]

print(f"Threshold used: {threshold}")
```

---

## Conclusion

The **Vector Search** feature is a powerful tool for retrieving relevant documents using embeddings and cosine
similarity. By embedding the user query and comparing it to document embeddings, this approach ensures high-quality
retrieval of contextually relevant data. Passing these documents to GPT allows the model to base its answers on specific
and relevant information, enhancing the quality and reliability of responses.

This approach:

- **Enhances retrieval accuracy** through cosine similarity.
- **Adapts to sparse data** by dynamically reducing the threshold.
- Provides a **scalable solution** for embedding-based document search.

