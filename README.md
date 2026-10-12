# Multilingual AI Voice Assistant with RAG

A full-stack AI voice assistant built using React, FastAPI, Speech-to-Text (STT), Retrieval-Augmented Generation (RAG), and Text-to-Speech (TTS).

## 1. Project Overview

The application allows users to speak naturally, ask questions about documents or websites, and hear AI-generated answers in multiple languages.

### Features

- Speech-to-Text transcription
- AI-powered question answering with RAG
- Text-to-Speech audio responses
- Multilingual conversations
- PDF, TXT, Markdown, DOCX, and website ingestion
- Local vector database using ChromaDB
- Hosted AI models
- React frontend
- WebSocket communication
- Conversation history

## 2. Technology Stack

| Component | Technology |
|---|---|
| Frontend | React + Vite |
| Backend | Python + FastAPI |
| Communication | WebSocket |
| Speech-to-Text | OpenAI transcription API |
| LLM | OpenAI chat model |
| Text-to-Speech | OpenAI speech API |
| Embeddings | Sentence Transformers |
| Vector Database | ChromaDB |
| PDF Processing | pypdf |
| DOCX Processing | python-docx |
| Website Extraction | BeautifulSoup |
| Configuration | python-dotenv |

## 3. System Architecture

```text
User speaks
    |
    v
React microphone interface
    |
    v
WebSocket connection
    |
    v
Speech-to-Text
    |
    v
User question
    |
    v
Multilingual embeddings
    |
    v
ChromaDB similarity search
    |
    v
Relevant document chunks
    |
    v
LLM generates an answer
    |
    v
Text-to-Speech
    |
    v
Audio response
    |
    v
User hears the answer
```

The initial version processes one recorded utterance per turn. Streaming partial transcripts, continuous listening, and interruption handling can be added later.

## 4. Project Structure

```text
ai-voice-assistant/
├── backend/
│   ├── app/
│   │   ├── __init__.py
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── voice.py
│   │   ├── rag.py
│   │   └── ingestion.py
│   ├── documents/
│   ├── data/
│   │   └── chroma/
│   ├── .env
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── App.css
│   │   └── main.jsx
│   ├── .env
│   └── package.json
├── .gitignore
└── README.md
```

## 5. Prerequisites

Install the following:

- Python 3.11 or newer
- Node.js and npm
- An OpenAI API key
- Git (recommended)

## 6. Backend Installation

Create the project directory:

```bash
mkdir ai-voice-assistant
cd ai-voice-assistant
mkdir backend
cd backend
```

Create a Python virtual environment:

```bash
python -m venv .venv
```

Activate it on Windows:

```powershell
.venv\Scripts\Activate.ps1
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

### Install Dependencies

Create `backend/requirements.txt`:

```text
fastapi
uvicorn[standard]
python-multipart
openai
python-dotenv
chromadb
sentence-transformers
pypdf
beautifulsoup4
requests
python-docx
```

Install packages:

```bash
pip install -r requirements.txt
```

## 7. Environment Configuration

Create `backend/.env`:

```dotenv
OPENAI_API_KEY=your_api_key_here

CHAT_MODEL=gpt-4.1-mini
STT_MODEL=gpt-4o-mini-transcribe
TTS_MODEL=gpt-4o-mini-tts
TTS_VOICE=coral

CHROMA_PATH=./data/chroma

EMBEDDING_MODEL=sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2

FRONTEND_ORIGIN=http://localhost:5173
```

Replace `your_api_key_here` with your API key.

**Security:** Never expose your API key in frontend code or commit `.env` to Git.

## 8. Backend Modules

### config.py

Responsible for:

- Loading environment variables
- Configuring AI models
- Setting the ChromaDB storage path
- Configuring document storage
- Setting the frontend origin

### rag.py

Responsible for:

1. Loading the multilingual embedding model.
2. Splitting text into overlapping chunks.
3. Generating local embeddings.
4. Storing embeddings in ChromaDB.
5. Retrieving relevant document chunks.
6. Sending retrieved context to the LLM.
7. Returning answers and source identifiers.

### ingestion.py

Supports these knowledge sources:

- PDF documents
- TXT files
- Markdown files
- DOCX documents
- Public website pages

Each source is converted to text, divided into chunks, embedded, and stored in ChromaDB.

### voice.py

Provides two main functions:

- `transcribe_audio(audio_bytes)`: converts speech into text.
- `synthesize_speech(text)`: converts generated text into audio.

### main.py

Provides the following endpoints:

| Endpoint | Purpose |
|---|---|
| `GET /health` | Health check |
| `POST /ingest/file` | Upload and index a document |
| `POST /ingest/url` | Index a website |
| `WS /ws/voice` | Process voice conversations |

## 9. Frontend Installation

From the project root:

```bash
npm create vite@latest frontend -- --template react
cd frontend
npm install
```

Create `frontend/.env`:

```dotenv
VITE_API_URL=http://localhost:8000
VITE_WS_URL=ws://localhost:8000/ws/voice
```

The frontend includes:

- Microphone recording controls
- Connection and processing status
- Speech transcript display
- AI response display
- Audio playback
- Retrieved source names
- Document upload
- Website ingestion

Implement the React interface using `App.jsx`, `App.css`, and `main.jsx`.

## 10. RAG Workflow

### Document Indexing

```text
PDF / DOCX / TXT / Website
          |
          v
      Text extraction
          |
          v
      Text chunking
          |
          v
    Local embeddings
          |
          v
       ChromaDB
```

### Question Answering

```text
Spoken question
       |
       v
Speech transcription
       |
       v
Question embedding
       |
       v
ChromaDB similarity search
       |
       v
Relevant document chunks
       |
       v
LLM + retrieved context
       |
       v
Generated answer
       |
       v
Speech synthesis
```

The assistant should answer knowledge-base questions using retrieved evidence. If the available context is insufficient, it should say that the answer cannot be determined from the indexed sources.

## 11. Multilingual Support

The system is designed for multilingual conversations.

- Speech recognition transcribes spoken questions.
- Multilingual embeddings help retrieve relevant information across languages.
- The LLM is instructed to answer in the user's language.
- TTS converts the answer into audio.

Test English, Hindi, mixed-language speech, and documents written in different languages.

Actual transcription accuracy, cross-language retrieval, and pronunciation depend on the chosen models and source documents.

## 12. Run the Application

### Start the Backend

Open Terminal 1:

```bash
cd backend
uvicorn app.main:app --reload --port 8000
```

### Start the Frontend

Open Terminal 2:

```bash
cd frontend
npm run dev
```

Open the application:

- Frontend: http://localhost:5173
- Backend health: http://localhost:8000/health
- API documentation: http://localhost:8000/docs

The first backend startup may take longer because the embedding model must be downloaded.

## 13. Testing Checklist

- [ ] Backend starts successfully.
- [ ] Frontend connects to the WebSocket.
- [ ] Microphone permissions work.
- [ ] Speech is transcribed correctly.
- [ ] PDF documents can be uploaded.
- [ ] Websites can be indexed.
- [ ] ChromaDB retrieves relevant passages.
- [ ] The LLM answers using retrieved context.
- [ ] The response appears in the UI.
- [ ] Audio playback works.
- [ ] English questions work.
- [ ] Hindi questions work.
- [ ] Mixed-language questions are tested.
- [ ] Errors are handled gracefully.

## 14. Production Improvements

Before deploying publicly, implement:

1. Authentication and document access controls.
2. WebSocket authorization and rate limiting.
3. Secure website fetching to prevent SSRF.
4. File validation and upload limits.
5. Retrieval relevance thresholds.
6. Source citations and improved chunking.
7. Persistent, isolated conversation histories.
8. Background document indexing.
9. Streaming speech recognition and TTS.
10. Voice activity detection and interruption handling.
11. HTTPS/WSS, monitoring, and logging.
12. API usage limits and cost controls.

The initial implementation is a prototype and needs these protections before public deployment.

## 15. Future Enhancements

- Continuous voice conversations
- Streaming partial transcripts
- Faster audio response delivery
- Interruptible speech playback
- Conversation memory
- User authentication
- Database integration
- Website crawling
- Retrieval reranking
- Source citations
- Docker deployment
- Cloud hosting

## 16. Useful Documentation

- OpenAI API: https://developers.openai.com/api/docs/
- Speech-to-Text: https://developers.openai.com/api/docs/guides/transcription
- Text-to-Speech: https://developers.openai.com/api/docs/guides/text-to-speech
- FastAPI: https://fastapi.tiangolo.com/
- React: https://react.dev/
- Vite: https://vite.dev/
- ChromaDB: https://docs.trychroma.com/
- Sentence Transformers: https://www.sbert.net/

## Conclusion

This project combines React, FastAPI, speech recognition, RAG, ChromaDB, an LLM, and speech synthesis into a multilingual AI voice assistant.

Start by getting document ingestion and one complete voice interaction working locally. Then improve retrieval quality, add streaming audio, and implement production security before deployment.
