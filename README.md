# PhishGuard AI: Backend API (lightweight deployment)

The FastAPI backend that serves **PhishGuard AI's** phishing-detection models. This is a slimmed-down version of the backend, packaged for cloud deployment (Gunicorn + Uvicorn).

➡️ **Full project** (frontend, browser extension, visual clone detection): [PHISHGUARD-AI-CIPHERIX](https://github.com/Manyaved123/PHISHGUARD-AI-CIPHERIX)

## Endpoints

| Method | Route | Input | What it does |
|---|---|---|---|
| `GET` | `/` | – | Health check |
| `POST` | `/scan-url` | `{"url": "..."}` | URL phishing classifier |
| `POST` | `/scan-text` | `{"text": "..."}` | NLP classifier for phishing messages (email / SMS) |

Each scan returns:
```json
{"risk_score": 90, "verdict": "High Risk", "reasons": ["ML model detected phishing URL"]}
```

## Models

- **URL model** (`live_url_model.pkl`): uses lexical features of the URL: length, number of dots, hyphens and `@` signs, the presence of words like *login*, *verify* and *bank*, and plain `http://`.
- **Text model** (`text_model_advanced.pkl`): an NLP pipeline that classifies a message as phishing or safe.

## Run locally

```bash
pip install -r requirements.txt
uvicorn main:app --reload
# API docs: http://127.0.0.1:8000/docs
```

Production:
```bash
gunicorn main:app -k uvicorn.workers.UvicornWorker
```

## Known issues

- `main.py` defines `/scan-text` twice. FastAPI uses the first definition, so the second, confidence-based version (under the "IMAGE MODEL" comment) never runs. Removing the first definition would enable confidence scores.

## Tech

Python · FastAPI · scikit-learn · joblib · Gunicorn
