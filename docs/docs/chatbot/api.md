# Chatbot API Documentation

This document provides detailed information about the WFP Chatbot API endpoints and their usage.

## Base URL

The API is available at the following base URLs depending on the environment:

- Development: `http://localhost:5000`
- Production: `https://wfp-hungermap.netlify.app`

## Authentication and CORS

The API uses CORS (Cross-Origin Resource Sharing) for security. The following restrictions apply:
- **Allowed Origins:**
  - `http://localhost:3000`
  - `http://127.0.0.1:3000`
  - `http://127.0.0.1:5000`
  - `https://wfp-hungermap.netlify.app`
- **Allowed Methods:** POST only
- **Allowed Headers:** Content-Type, Authorization
- **Exposed Headers:** Content-Type
- **Max Age:** 600 seconds

## Endpoints

### Query Endpoint

Used to send queries to the chatbot and receive AI-generated responses.

**Endpoint:** `/query`  
**Method:** `POST`  
**Content-Type:** `application/json`

#### Request Body

```json
{
    "reports_country_name": "string | null",  // Optional: Country name for report-specific context
    "query": "string",                        // Required: User's question
    "version": 1,                            // Optional: API version (default: 1)
    "chatbot_type": "string",                // Optional but currently fixed to "gpt-4o"
    "limit": 5,                              // Optional: Max number of context documents (default: 5)
    "previous_messages": []                  // Optional: Reserved for future use
}
```

#### Response

```json
{
    "response": "string",      // AI-generated answer to the query
    "sources": [              // Array of document names used as sources
        "string"             // Document name/identifier
    ]
}
```

Note: The response is formatted in Markdown syntax for easy rendering on the frontend.

#### Error Responses

- `400 Bad Request`: When required parameters are missing
  ```json
  {
      "error": "query parameter is required"
  }
  ```

### Health Check Endpoint

Root endpoint that serves the API documentation page.

**Endpoint:** `/`  
**Method:** `GET`

#### Response
Returns an HTML page containing API documentation.

## Implementation Notes

1. **Model Type**
   - While the API accepts a `chatbot_type` parameter, it currently always uses "gpt-4o" internally
   - This is hardcoded for consistency and quality control

2. **Token Usage**
   - Token usage is tracked internally for monitoring purposes
   - This information is not exposed in the API response

3. **Previous Messages**
   - The `previous_messages` parameter is accepted but reserved for future implementation
   - Currently does not affect the response generation

## Example Usage

```javascript
// Development environment
const response = await fetch('http://localhost:5000/query', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        query: "What is the current food security situation in Somalia?",
        reports_country_name: "Somalia",
        version: 1,
        limit: 5,
        previous_messages: []
    })
});

const data = await response.json();
// Response will contain markdown-formatted text
console.log(data.response);  // AI-generated answer in markdown format
console.log(data.sources);   // Array of source document names

// Production environment
const prodResponse = await fetch('https://wfp-hungermap.netlify.app/query', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        query: "What are the food security trends in East Africa?",
        version: 1,
        limit: 5
    })
});

const prodData = await prodResponse.json();