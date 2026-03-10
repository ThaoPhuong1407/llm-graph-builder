## LLM Graph Builder Onboarding

This guide is for running the project locally with Neo4j Desktop.

### Supported LLMs (current local setup)

- OpenAI
- Ollama

## 1. Prerequisites

- Neo4j Database 5.23+ with APOC
- Neo4j Desktop
- Node.js + Yarn
- Python 3.9+

## Core environment variables only

Use `backend/example.env` and `frontend/example.env` as templates, then set these core values.

### Backend (`backend/.env`)

- `NEO4J_URI`
- `NEO4J_USERNAME`
- `NEO4J_PASSWORD`
- `NEO4J_DATABASE`
- `EMBEDDING_MODEL`
- `LLM_MODEL_CONFIG_ollama_llama3` or `LLM_MODEL_CONFIG_openai-gpt-3.5` / `LLM_MODEL_CONFIG_openai-gpt-4o`
- `OPENAI_API_KEY` (only if you use OpenAI models)
- `OLLAMA_TIMEOUT_SECONDS` (recommended, default `120`)

### Frontend (`frontend/.env`)

- `VITE_BACKEND_API_URL`
- `VITE_BLOOM_URL`
- `VITE_REACT_APP_SOURCES`
- `VITE_LLM_MODELS`
- `VITE_CHAT_MODES`
- `VITE_ENV`

Notes:

- If using OpenAI model names from UI, keep the backend key format as dashes for these configs (example: `LLM_MODEL_CONFIG_openai-gpt-3.5`).
- Keep secrets in local `.env` only. Do not commit real API keys.

## 1. Run front-end

```bash
cd frontend
yarn
yarn run dev
```

## 2. Run back-end

```bash
cd backend
python -m venv [envName]
source [envName]/bin/activate
pip install -r requirements.txt
uvicorn score:app --reload
```

## 3. Run the Neo4j Desktop

- Install Neo4j Desktop
- Create a new project, and upload the dump file (search email) via "Add"
- Create a new DBMS from the uploaded dump
- Install Apoc
- Start the database

## 4. Go back to backend `.env` and edit:

```env
NEO4J_URI=bolt://localhost:7687
NEO4J_PASSWORD=<your-password>
```

## 5. Restart backend

```bash
uvicorn score:app --reload
```

## 6. Use the front-end

Once backend and Neo4j DB are running, open the frontend localhost URL in your browser.

1. Click `Connect to Neo4j`.
2. Upload PDF/text files, then start graph generation.
3. Select the correct LLM model from the bottom dropdown before chatting or creating graphs.
