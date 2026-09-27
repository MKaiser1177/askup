# AskUp Chatbot

AskUp is a small Flask chatbot with a browser-based chat interface. It uses Hugging Face Transformers and the `facebook/blenderbot-400M-distill` model to generate replies.

## Project Structure

```text
askup/
├── README.md
├── my_env/                     # Optional local Python virtual environment
└── LLM_application_chatbot/
   ├── app.py                   # Flask routes and model integration
   ├── requirements.txt
   ├── Dockerfile
   ├── static/                  # JavaScript, styles, and images
   └── templates/
      └── index.html
```

## Requirements

- Python 3.10 or newer
- Internet access on first startup to download the model and tokenizer from Hugging Face
- Enough memory and disk space to load the BlenderBot model

Python packages are listed in `LLM_application_chatbot/requirements.txt`.

## Set Up and Run on Windows

Run these commands from the repository root (`askup`):

```powershell
py -m venv my_env
.\my_env\Scripts\Activate.ps1
cd .\LLM_application_chatbot
python -m pip install -r requirements.txt
flask --app app run
```

If PowerShell blocks virtual-environment activation, use Command Prompt and run `my_env\Scripts\activate.bat` from the repository root, then continue with the remaining commands.

On first startup, Hugging Face downloads the model files; this can take a while. Once Flask is running, open [http://127.0.0.1:5000](http://127.0.0.1:5000) in your browser. Keep the terminal and server running while chatting. You can also start the server with `python app.py` from `LLM_application_chatbot`.

## Chat API

The browser sends requests to the Flask app on the same origin. The endpoint accepts a JSON object and responds with plain text.

```http
POST /chatbot
Content-Type: application/json
```

Request body:

```json
{
   "prompt": "Hello! How are you?"
}
```

Example request from a separate terminal:

```bash
curl -X POST http://127.0.0.1:5000/chatbot \
   -H "Content-Type: application/json" \
   -d '{"prompt": "Tell me a joke."}'
```

The response body is the generated reply as plain text.

## Conversation Behavior

- The app retains a short conversation history in server memory. It is not saved to disk and is cleared when the server restarts.
- The history is global to this Flask process, not separated by browser or user.
- The model is loaded when the Flask app starts, so startup and the first response can take time.
- This project is intended for local experimentation. It does not provide authentication or persistent, per-user conversation storage.

## Troubleshooting

### The browser reports “Failed to fetch”

- Confirm Flask is still running and finished loading the model.
- Open the chat page from the Flask address, `http://127.0.0.1:5000/`, rather than opening `index.html` directly or using a different host or port.
- Check the Flask terminal for a traceback when you submit a message. The page and `/chatbot` request should use the same host and port.

### Flask cannot find the app

Run `flask --app app run` from the `LLM_application_chatbot` directory, not from the repository root.

### Model download or import fails

Confirm internet access and install the dependencies from the app directory:

```powershell
python -m pip install -r requirements.txt
```

### The server cannot use port 5000

Start Flask on another port with `flask --app app run --port 5001`, then open `http://127.0.0.1:5001/`.
