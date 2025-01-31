# OpenAI Integration & Response

Author: `Georgi Peev`

This document details how the WFP Chatbot integrates with OpenAI's API and handles responses.

## Environment Setup

Before working with the OpenAI integration:

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

3. Set up OpenAI API key:
```bash
# Add to .env file
OPENAI_API_KEY=your_api_key_here
```

---

## OpenAI Integration

### Model Configuration

The chatbot supports multiple OpenAI models with different configurations:

| Model | Token Limit | Input Cost (USD/token) | Output Cost (USD/token) |
|-------|-------------|----------------------|------------------------|
| gpt-4o | 26,000 | $0.0000025 | $0.00001 |
| gpt-3.5-turbo | 4,096 | $0.0000005 | $0.0000015 |
| o1-mini | 26,000 | $0.0000025 | $0.00001 |

Note: The token limits for gpt-4o and o1-mini are set to 26,000 (below the organizational limit of 30,000) for safety.

---

### Authentication

The integration uses OpenAI's API key, which should be set in the environment variables:
```env
OPENAI_API_KEY=your_api_key_here
```

---

### Request Flow

1. **Query Processing**
   - User query is received
   - Relevant documents are retrieved via similarity search
     - If country report specified: limit=1 document
     - If no country report: uses user-specified limit (default: 5)
   - Documents are compressed to optimize token usage
   - Conversation context is gathered from recent interactions

2. **Message Construction**
   - System message defines the chatbot's role and guidelines
   - User message combines:
     - Recent conversation context
     - Country report context (if specified)
     - Relevant document content

3. **OpenAI API Call**
   ```python
   completion = openai.chat.completions.create(
       model=chatbot_type,
       messages=messages,
       temperature=0.7
   )
   ```

---

### Error Handling

1. **Rate Limit Handling**
   - On hitting rate limits, waits 20 seconds
   - Makes exactly one retry attempt
   - If retry fails, error is propagated

2. **Context Length Management**
   - Detects maximum context length errors
   - Automatically reduces context by half and retries
   - Preserves core functionality while adapting to limits

3. **General Error Handling**
   - Catches and logs all OpenAI API errors
   - Propagates errors for proper client notification

---

## Response Handling

### Token Management

1. **Daily Usage Tracking**
   - Monitors token usage in MongoDB
   - Enforces daily token limit (default: 26,000 tokens)
   - Tracks costs for both input and output tokens

2. **Token Optimization**
   - Compresses document content before API calls
   - Truncates text to stay within model limits
   - Caches compressed documents for efficiency

---

### Document Compression

The chatbot uses a compression system to maximize context while minimizing token usage:

1. **Compression Process**
   ```python
   # For each relevant document
   compressed_doc, res, is_compressed = prompt_compressor.compress_document(doc, doc["_id"])
   ```
   - Summarizes document content while preserving key information
   - Removes redundant or less relevant details
   - Maintains essential facts, statistics, and relationships
   - Uses GPT-based compression for intelligent summarization

2. **Caching Strategy**
   - Compressed versions are cached by document ID
   - Cache hits avoid recompression costs and API calls
   - Compression stats are tracked:
     ```python
     print(f"Compression stats:\n  Before: {real_tokens}\n  After: {compressed_tokens}")
     print(f"Cost saved: ${(input_cost - cost_compression):.8f}")
     ```

---

### Context Building

The chatbot builds context for OpenAI in several layers:

1. **Conversation History**
   - Maintains recent interactions (default: 5 turns)
   - More recent messages have higher priority
   - Used to maintain conversation coherence
   - Format:
     ```python
     f"Recent messages:\n{conversation_context}\n\n"
     ```

2. **Document Context**
   - Country-specific reports (if applicable):
     ```python
     f"Provide your answer solely based on the following context which is about a country report:\n{report_context}"
     ```
   - General knowledge base:
     ```python
     f"Use the following context:\n{search_result}"
     ```

---

### Response Formatting

The chatbot's responses follow strict formatting and content guidelines:

| Category | Guidelines                                                                                                           |
|----------|----------------------------------------------------------------------------------------------------------------------|
| Language | - Clear and concise<br/>- Professional tone<br/>- No technical jargon                                                |
| Content Restrictions | - No future predictions<br/>- No political opinions<br/>- No food aid recommendations<br/>- No sensitive information |
| Data Usage | - Only use provided context<br/>- No external data sources<br/>- Cite sources when available                         |
| Formatting | - Use markdown for structure<br/>- Include relevant headers<br/>- Maintain consistent style                          |

1. **Supported Markdown Syntax**
   - Headers (h1-h3) for section organization
   - Bold text for key statistics
   - Bullet points for listing facts
   - Ordered lists for sequential information
   - Blockquotes for data limitations
   - URLs (only for hungermap.wfp.org)
   - Tables for data comparison
   - Task lists for status indicators

2. **Content Requirements**
   - Use only data from provided context
   - Include precise statistics when available
   - State time periods for any trends
   - Acknowledge data limitations explicitly
   - Use English names for locations
   - Reference only WFP Hunger Map as source

3. **Content Restrictions**
   - No future predictions
   - No political opinions
   - No food aid recommendations
   - No sensitive information
   - No speculation beyond data
   - No unsupported markdown syntax

The formatting ensures responses are:
- Consistently structured
- Easy to read
- Suitable for frontend rendering
- Professional in tone
- Data-focused and factual

---

## Monitoring and Optimization

1. **Performance Monitoring**
   - Tracks compression statistics
   - Monitors token usage and costs
   - Records cache hit rates

2. **Cost Optimization**
   - Calculates and logs cost savings from compression
   - Uses appropriate models based on context size
   - Caches frequently used data

---

## Configuration

Key configuration options include:

```env
DAILY_TOKEN_LIMIT=26000
MONGODB_URI=your_mongodb_uri
MONGODB_DB=your_database_name
```

These settings can be adjusted to balance cost, performance, and functionality requirements.