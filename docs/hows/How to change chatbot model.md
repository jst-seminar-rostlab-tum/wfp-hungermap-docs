# Changing the Chatbot Model

### 1. Choose the New Model
Decide on the AI model you wish to use. In the code, the model is specified as a string in the `chatbot_type` field (e.g., `"gpt-4o"`). You can replace this with the model name you want to use, such as:

- `gpt-4`
- `gpt-3.5-turbo`

### 2. Update the Model in the Flask Route
To update the model in the Flask app, locate the `/query` route under `app.py`. The route currently accepts a `chatbot_type` parameter from the client request:

```python
chatbot_type = data.get("chatbot_type", "gpt-4o")
```

Change the default value (`"gpt-4o"`) to your new model if desired.

For example, to default to `gpt-4`, change it to:

```python
chatbot_type = data.get("chatbot_type", "gpt-4")
```

This will make the app use `gpt-4` by default unless another model is specified in the request.

### 3. Update `handle_user_query` to Handle the New Model
The `handle_user_query` function under `ai_model.py` is where the model is called via the OpenAI API. It uses the `chatbot_type` passed from the route. If you change the model to something different, make sure that the model name in the `openai.chat.completions.create()` method matches your new choice.

```python
completion = openai.chat.completions.create(
    model=chatbot_type,  # Ensure this is the updated model
    messages=messages,
    temperature=0.7,
)
```

### 4. Verify Token and Model Constraints
Different models have different token limits and behavior. Ensure that the new model you're using has the token constraints you need. For instance, the `gpt-4o` model mentioned has a 30,000-token limit, while other models may have smaller or larger limits.

In the `handle_user_query` function, check the `MAX_TOKENS` value to ensure that it works with your new model. For example, if you're using a model with a larger token limit, you might want to increase this value:

```python
MAX_TOKENS = 26000  # Update this value if needed based on your new model's token limit
```

### 5. Update Dependencies (if required)
If your new model requires any additional setup (such as a different version of OpenAI's API or additional libraries), update your `requirements.txt` file or install the necessary dependencies.

For instance, to ensure the OpenAI Python client is up-to-date, run:

```bash
pip install --upgrade openai
```

To complete the process of changing the chatbot model, you'll also need to ensure that the necessary API keys for the new model are included in your `.env` file. Here's how to do that:

### 6. Add API Keys to the `.env` File
Many chatbot models, such as OpenAI's models, require API keys to authenticate your requests. If you are switching to a different model that requires its own API key, follow these steps:

1. **Open the `.env` file** in your project directory.
   
2. **Add the API key** for the new model (if required). For example, if you are using OpenAI's GPT models, you'll need to add the OpenAI API key like this:

   ```env
   OPENAI_API_KEY=your-openai-api-key-here
   ```

   Replace `your-openai-api-key-here` with your actual API key. 

3. **Load the API key in the application**:
   The `.env` file will automatically load the environment variables, including the API key, because your app uses the `dotenv` library. Ensure this line is included at the top of your Python files:

   ```python
   from dotenv import load_dotenv
   load_dotenv()  # This loads the environment variables from the .env file
   ```

4. **Access the API key**:
   The `openai.api_key` is set using the `OPENAI_API_KEY` environment variable in the `handle_user_query` function:

   ```python
   openai.api_key = os.getenv("OPENAI_API_KEY")
   ```

   This will authenticate the API requests with the new model using the key provided in the `.env` file.

### Recap of Key Steps:
1. Update the `chatbot_type` in the `/query` route if you want a default model.
2. Ensure the correct model is passed in `openai.chat.completions.create()`.
3. Adjust token limits based on your new model.
4. Add and load the necessary API key to the `.env` file (e.g., `OPENAI_API_KEY=your-new-api-key-here`).