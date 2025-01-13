# How to Adjust Daily Token Limits?

Author: `Muhammed Emre Bayraktaroglu`

Daily Token Limits for the usage of the OpenAI API are managed through a combination of an environment variable(s) and a database collection, which keeps track of the daily used tokens. 

### Environment Variable
Located in `.env`, the following variable can be modified to set a new daily token limit:

```plaintext
DAILY_TOKEN_LIMIT=[num_of_tokens]
```
By default, this value is set to **26000** in accordance to compliance with your requirements. You can adjust this value to any number that fits your needs.

### Database Collection
The database collection (`DAILY_TOKEN_LIMIT`) is used to store the daily token usage data. This collection is automatically updated as soon as the AI model for the Chatbot is used. The collection contains the tokens per day, ordering them by date. 

In case the daily token limit is reached, the Chatbot will switch to a lighter model to reduce compute costs. This ensures that the Chatbot remains functional even when the daily token limit is reached.