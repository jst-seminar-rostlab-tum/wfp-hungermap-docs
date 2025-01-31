---
id: context_retrieval
title: Context Retrieval System
slug: /chatbot/context_retrieval
---

# Context Retrieval System

Author: `Georgi Peev`

   ## Overview

   The Context Retrieval System manages conversation history and context awareness. The system enables the chatbot to maintain coherent conversations by efficiently storing, retrieving, and utilizing relevant parts of the conversation history.

   ---

   ## Environment Setup

   Before working with the Context Retrieval System:

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

   ## Implementation

   The system is implemented in the following key files:

   | Component | File Path |
   |-----------|-----------|
   | Core Implementation | `src/utils/context_resolver_utils.py` |
   | Performance Monitoring | `src/utils/monitoring_utils.py` |
   | Unit Tests | `src/tests/test_context.py` |

   ### Core Components

   #### ConversationTurn

   The `ConversationTurn` class represents a single interaction in the conversation history:

   ```python
   @dataclass
   class ConversationTurn:
      query: str          # User's input query
      response: str       # System's response
      timestamp: datetime # When the interaction occurred
      relevance_score: float = 0.0  # Dynamically calculated relevance to current query
      token_count: int = 0          # Token usage for this turn
   ```

   #### SimpleContextManager

   The `SimpleContextManager` class handles conversation history management and context optimization:

   ```python
   from utils.context_resolver_utils import SimpleContextManager

   context_manager = SimpleContextManager(
      max_history=5,    # Maximum conversation turns to retain
      max_tokens=2000   # Maximum total tokens in context
   )
   ```

   Key features:
   - Dynamic conversation history management
   - Token usage optimization
   - Relevance-based context selection
   - Automatic context pruning

   ---

   ### Context Management Process

   1. **Storage**: Each conversation turn is stored with metadata for efficient retrieval
   2. **Relevance Calculation**: Past conversations are scored based on relevance to current query
   3. **Context Selection**: Most relevant conversations are selected within token limits
   4. **Context Optimization**: Selected context is formatted for optimal token usage

   ---

   ### Relevance Scoring Algorithm

   The system uses a weighted combination of factors to calculate relevance scores:

   | Factor | Weight | Description                                                                                                                                                   |
   |--------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | Semantic Similarity | 40% | - Keyword overlap between queries<br/>!- Uses both query and response text<br/>- Formula: `similarity = overlap_words / total_unique_words` |
   | Time Decay | 40% | - Recent conversations weighted higher<br/>- 24-hour decay period<br/>- Formula: `time_weight = 1.0 / (1.0 + hours_difference / 24)`                          |
   | Token Efficiency | 20% | - Prefers shorter, relevant context<br/>- Formula: `token_weight = 1.0 - (turn_token_count / max_tokens)`                                                     |

   The final relevance score is calculated as:
   ```
   relevance_score = (similarity * 0.4) + (time_weight * 0.4) + (token_weight * 0.2)
   ```

   This scoring system ensures that:
   - Semantically relevant conversations are prioritized
   - Recent conversations are favored over older ones
   - Shorter, efficient contexts are preferred when relevance is similar

   ---

   ## Token Management

   The Context Retrieval System works in conjunction with the [Token Optimization Techniques](token_optimization_techniques.md) for efficient token management. While the Token Optimization system handles the compression of individual prompts, the Context Retrieval System manages token usage across conversation history.

   ### Token Usage Control

   The system includes built-in token management through the `TokenUsageManager`:
   - Automatically counts tokens for each conversation turn
   - Ensures context stays within specified token limits
   - Handles model-specific token counting
   - Provides token usage metrics for monitoring

   Token limits:
   - Minimum: 100 tokens
   - Default: 2000 tokens (adjustable based on model context window)

   ---

   ## Configuration

   ### Parameters

   | Parameter | Default | Description |
   |-----------|---------|-------------|
   | max_history | 5 | Maximum number of conversation turns to retain |
   | max_tokens | 2000 | Maximum total tokens allowed in context |

   ### Example Manual Configuration

   ```python
   context_manager = SimpleContextManager(
      max_history=3,    # Retain last 3 conversations
      max_tokens=1000   # Use maximum 1000 tokens
   )

   # Add new conversation
   context_manager.add_turn(
      query="What is the food security status in Nigeria?",
      response="Current food security analysis for Nigeria shows..."
   )

   # Retrieve context for new query
   context = context_manager.get_formatted_context(
      "Has the situation in Nigeria changed recently?"
   )
   ```

   ---

   ## Performance Monitoring

   The system includes built-in performance monitoring through the `ContextMonitor` class:

   ### Metrics Tracked
   - Token usage statistics
   - Response latency
   - Cache performance
   - Context truncation frequency
   - Daily usage patterns

   ### Accessing Metrics

   ```python
   # Get daily statistics
   stats = context_manager.monitor.get_daily_stats()

   # Get metrics for specific conversation
   metrics = context_manager.monitor.get_conversation_metrics(conversation_id)
   ```

   ---

   ## Performance Considerations

   ### Optimization Features
   - LRU caching for keyword extraction
   - Efficient relevance scoring algorithms
   - Smart context truncation
   - Integration with Prompt Compressor for token optimization
   - Automatic metric cleanup

   ### Resource Management
   1. Token Usage
      - Monitor token consumption through `ContextMonitor`
      - Adjust `max_tokens` based on LLM context window size
      - Consider token distribution across conversation turns
      - Utilize Prompt Compressor for long responses

   2. History Management
      - Balance between context depth and relevance
      - Monitor and adjust `max_history` based on use case requirements
      - Use monitoring data to optimize settings

   ---

   ## Integration Guidelines

   1. Import required components:
      ```python
      from utils.context_resolver_utils import SimpleContextManager
      from utils.monitoring_utils import ContextMonitor
      ```

   2. Initialize context manager with appropriate limits:
      ```python
      context_manager = SimpleContextManager(
         max_history=5,
         max_tokens=2000
      )
      ```

   3. Implement conversation handling:
      ```python
      # Store conversations
      context_manager.add_turn(query, response)

      # Retrieve relevant context
      context = context_manager.get_formatted_context(current_query)
      ```

   4. Monitor performance:
      ```python
      # Access monitoring metrics
      metrics = context_manager.monitor.get_daily_stats()
      ```
