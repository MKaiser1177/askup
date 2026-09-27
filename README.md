# AskUp Chatbot

A lightweight Flask-based chatbot API that uses the Hugging Face `facebook/blenderbot-400M-distill` model to generate conversational responses.

## Overview

This project exposes a single HTTP endpoint for sending a prompt and returning a chatbot reply. It keeps a short in-memory conversation history so the model can respond in context for recent turns.

The app is intentionally simple and is a good starting point for:

- local chatbot experimentation
- building a small backend around a Hugging Face conversational model
- learning how to serve a model through Flask

## Project Structure

```text
askup/
├── app.py
├── my_env/
├── README.md
└── __pycache__/
```

### Files

- `app.py` — Flask app, model loading, conversation handling, and API route
- `my_env/` — local Python virtual environment used for dependencies
- `README.md` — project documentation

## Features

- Flask API server
- Hugging Face Transformers model integration
- In-memory recent chat history for short context retention
- JSON request handling for chat prompts
- CORS enabled for cross-origin requests

## Requirements

This project uses Python 3.11 and the following main dependencies:

- Flask
- Flask-CORS
- transformers
- torch
- sentencepiece (sometimes needed depending on model tokenizer support)

The workspace already includes a local virtual environment in `my_env/`, which is the recommended environment to use.

## Setup

1. Open a terminal in the project root.
2. Activate the virtual environment:

   On Windows PowerShell:

   ```powershell
   .\my_env\Scripts\Activate.ps1
   ```

   On Command Prompt:

   ```bat
   my_env\Scripts\activate.bat
   ```

3. Confirm Python is available from the environment:

   ```powershell
   python --version
   ```

4. Install any missing dependencies if needed:

   ```powershell
   python -m pip install flask flask-cors transformers torch
   ```

> The first time the app runs, Hugging Face will download the BlenderBot model and tokenizer. This may take a few minutes depending on your internet connection.

## Running the App

From the project root:

```powershell
python app.py
```

By default, Flask will start a development server, usually at:

```text
http://127.0.0.1:5000
```

## API

### Endpoint

```http
POST /chatbot
```

### Request Body

```json
{
  "prompt": "Hello! How are you?"
}
```

### Response

The endpoint returns the generated chatbot reply as plain text.

Example response:

```text
Hi there! I'm doing well. How can I help you today?
```

## Example Requests

### cURL

```bash
curl -X POST http://127.0.0.1:5000/chatbot \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Tell me a joke."}'
```

### Python

```python
import requests

response = requests.post(
    "http://127.0.0.1:5000/chatbot",
    json={"prompt": "What is the capital of France?"}
)

print(response.text)
```

## How It Works

The app does the following on each request:

1. Reads the JSON payload from the request body.
2. Keeps only the last 6 conversation turns in memory.
3. Builds a prompt with the recent history and the latest user message.
4. Sends the prompt to the BlenderBot model using the tokenizer.
5. Generates a response with constrained decoding settings.
6. Stores the new user and bot messages in the conversation history.
7. Returns the generated reply.

## Notes

- The model is loaded at startup, so the first request may be slower than later ones.
- Conversation context is kept only in memory and resets when the server restarts.
- The app is intended for local development and experimentation rather than production deployment.
- The current implementation is a simple proof-of-concept and does not include authentication, persistent storage, or advanced conversation management.

## Troubleshooting

### Model download is slow or fails

- Check your internet connection.
- Make sure Hugging Face can access the model repository.
- Retry after confirming the environment has internet access.

### Import errors

Make sure your environment has the required packages installed:

```powershell
python -m pip install flask flask-cors transformers torch
```

### Server won’t start

- Ensure you are in the project root.
- Confirm the virtual environment is activated.
- Check whether another process is already using port 5000.

## Example Development Flow

```powershell
cd c:\Users\Vedant Agrawal\Desktop\askup
.\my_env\Scripts\Activate.ps1
python app.py
```

Then send requests to the endpoint using cURL or another HTTP client.

## License

This project does not include a specific license file. If you plan to share or distribute it, add a license that matches your intended usage.

## Next Ideas

Possible extensions include:

- adding a frontend interface
- saving chat history to a database
- supporting user-specific sessions
- adding streaming responses
- deploying the app to a production server

---

This README was written to match the current implementation in `app.py` and should help you run the project locally and interact with the chatbot API.
