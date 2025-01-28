# Frontend and Backend Integration

Author: `Ahmet Selman Güclü`

The integration between the frontend and backend components of the Report Chatting feature is crucial for seamless communication and data exchange.

In general, the frontend is solely responsible for specifying that a chat is going to use the report chatting functionality, whereas data retrieval and response generation is being done in the backend.

## Front-end Structure

### Chat Interface

Every chat with the chatbot (general chatting as well as report chatting) has the following interface in the frontend:

```typescript
export interface IChat {
    id: string;
    title: string;
    isReportStarter?: boolean;
    messages: IMessage[];
    isTyping: boolean;
    timestamp: number;
    report_context?: IReportContext;
}
```

The optional `report_context` attribute specifies that a specific chat is about a report. It contains the type of the report (currently only `country` for country reports and `year_in_review` for year in review reports are supported) and the value of the report (e.g. the country name or the year).

```typescript
export interface IReportContext {
    type: 'country' | 'year_in_review';
    value: string;
}
```

### Query Request

When a user enters a prompt in the UI of the chatbot, the frontend sends a query to the backend via the `sendMessage` function:

```typescript
sendMessage(message: string, options: Partial<Omit<QueryRequest, 'query'>>): Promise<QueryResponse>;
```

The interface for a query has the following structure:

```typescript
export interface QueryRequest {
    chatId: string;
    report_context?: IReportContext;
    query: string;
    version?: number;
    chatbot_type?: string;
    limit?: number;
    previous_messages?: IMessage[];
}
```

Once again, `report_context` is used to specify the report chatting functionality.

All in all, report chatting is specified via setting the `report_context` attribute twice: once for the chat itself and once for each query send to the backend. This helps to distinguish each chat in the frontend and handling queries accordingly to their functionality in the backend.

## Back-end Processing

### Handling the Query

When a query for report chatting is sent from the frontend, further processing is being done with the `handle_user_query` function on the backend. It uses the `report_context` attribute to determine whether the query is related to a specific country report, allowing the system to provide contextually relevant responses and differentiate between general vs. report chatting.

```python
def handle_user_query(
    query: str,
    chatbot_type: str,
    report_context: dict = None,
    limit: int = 5,
    previous_message: str = "",
) -> tuple[str, list]:
    # ... existing code ...
    # Extract relevant documents using similarity search
    if report_context:
        docs_similarity_search = similarity_search(
            query,
            limit=2,
            report_context=report_context,
            collection_name="report_chatting",
        )
    else:
        docs_similarity_search = similarity_search(query, limit=limit)
    # ... existing code ...
```

### Similarity Search

The difference in general vs. report specific chatting in `handle_user_query` is the retrieved data with `similarity_search` to answer the query. 

For the report chatting functionality, `similarity_search` is generally used with `limit=2` and the specific collection, in which the report data is being stored (`collection_name="report_chatting"`). Reducing the limit to two results per search has shown to be a good enough for keeping the token usage low while producing high quality responses.

Another main difference is that a pre-filtering is being done in a report chatting based similarity search to solely get report entries from the corresponding country/year:

```python
... existing code ...
# Filter entries for the specified country. Especially for report chatting
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
... existing code ...
```

## Summary

All in all, the workflow of a report chatting based query can be summarized like the following:

1. **Receive Query**: The back-end receives the query request from the front-end.
2. **Identify Context**: It checks if `report_context` is present to identify report-specific queries.
3. **Similarity Search**: The system performs a similarity search to retrieve relevant report sections.
4. **Generate Response**: A response is generated and sent back to the front-end, providing users with precise and context-aware information.

This integration ensures that users can seamlessly interact with country-specific reports through a conversational interface. The use of the `report_context` attribute allows the system to maintain context and provide accurate, report-based responses.

In the next section, we'll explore how report data is processed and structured within the system.
