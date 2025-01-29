---
id: chatbot-api
title: Chatbot API Documentation
slug: /chatbot/api
---

# Chatbot API Documentation

Author: `Georgi Peev`

This document provides detailed information about the WFP Chatbot API endpoints and their usage.

---

## Environment Setup

Before running the API:

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

## Base URL

The API is available at the following base URLs depending on the environment:

| Environment | URL |
|------------|-----|
| Development | `http://localhost:5000` |
| Production | `https://wfp-hungermap.netlify.app` |

## Authentication and CORS

The API uses CORS (Cross-Origin Resource Sharing) for security. The following restrictions apply:

| Category | Values                                                                                                                  |
|----------|-------------------------------------------------------------------------------------------------------------------------|
| Allowed Origins | `http://localhost:3000`<br/>`http://127.0.0.1:3000`<br/>`http://127.0.0.1:5000`<br/>`https://wfp-hungermap.netlify.app` |
| Allowed Methods | POST only                                                                                                               |
| Allowed Headers | Content-Type, Authorization                                                                                             |
| Exposed Headers | Content-Type                                                                                                            |
| Max Age | 600 seconds                                                                                                             |

---

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
    "chatbot_type": "string",                // Optional but currently fixed to "gpt-4o" - see [Model Types](#implementation-notes) for more information
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

---

## Implementation Notes

| Category | Description                                                                                                                                    |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Model Type | - Accepts `chatbot_type` parameter<br/>- Currently fixed to "gpt-4o"<br/>- Hardcoded for consistency                                           |
| Token Usage | - Tracked internally<br/>- Used for monitoring<br/>- Daily limits enforced                                                                     |
| Previous Messages | - The `previous_messages` parameter is accepted but reserved for future implementation<br/>- Currently does not affect the response generation |

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