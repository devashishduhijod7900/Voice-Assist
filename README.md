Multilingual AI Voice Assistant with RAG

A full-stack AI voice assistant built with React, FastAPI, Speech-to-Text (STT), Retrieval-Augmented Generation (RAG), and Text-to-Speech (TTS).

1. Project Overview

The assistant allows users to speak naturally, ask questions about documents and websites, and hear AI-generated answers in multiple languages.

Features
Speech-to-Text transcription
AI-generated answers using RAG
Text-to-Speech audio responses
Multilingual conversations
PDF, TXT, Markdown, DOCX, and website ingestion
Local vector database
Hosted AI models
React-based user interface
WebSocket communication
Conversation history during a session
2. Technology Stack
Component	Technology
Frontend	React + Vite
Backend	Python + FastAPI
Communication	WebSocket
Speech-to-Text	OpenAI transcription API
LLM	OpenAI chat model
Text-to-Speech	OpenAI speech API
RAG framework	Custom retrieval pipeline
Embeddings	Sentence Transformers
Vector database	ChromaDB
PDF processing	pypdf
Word documents	python-docx
Website extraction	BeautifulSoup
Configuration	python-dotenv
3. System Architecture
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


The initial implementation uses turn-based conversation: the user records an utterance, stops recording, and receives a spoken response.

Full-duplex conversation, partial transcripts, and interruption handling are future enhancements.

4. Project Structure
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

5. Prerequisites

Install the following:

Python 3.11 or newer
Node.js and npm
An OpenAI API key
Git (recommended)
6. Backend Installation

Create the project:

mkdir ai-voice-assistant
cd ai-voice-assistant
mkdir backend
cd backend


Create a virtual environment:

python -m venv .venv


Activate it on Windows:

.venv\Scripts\Activate.ps1


Activate it on macOS or Linux:

source .venv/bin/activate

Dependencies

Create backend/requirements.txt:

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


Install the packages:

pip install -r requirements.txt

7. Environment Configuration

Create backend/.env:

OPENAI_API_KEY=your_api_key_here

CHAT_MODEL=gpt-4.1-mini
STT_MODEL=gpt-4o-mini-transcribe
TTS_MODEL=gpt-4o-mini-tts
TTS_VOICE=coral

CHROMA_PATH=./data/chroma

EMBEDDING_MODEL=sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2

FRONTEND_ORIGIN=http://localhost:5173


Replace the API key placeholder with your own key.

Security: Never expose your API key in React code, commit .env to Git, or send it to the browser.

8. Backend Modules
config.py

Responsibilities:

Load environment variables.
Configure AI models.
Configure the ChromaDB path.
Set the document directory.
Configure the permitted frontend origin.
rag.py

Responsibilities:

Load the multilingual embedding model.
Split source text into overlapping chunks.
Generate embeddings.
Store chunks in ChromaDB.
Retrieve relevant chunks for each question.
Pass retrieved context to the LLM.
Return the generated answer and source identifiers.
ingestion.py

Supports ingestion of:

PDF documents
Plain text files
Markdown files
DOCX documents
Public website pages

Each source is extracted into text, split into chunks, embedded, and stored in ChromaDB.

voice.py

Provides two functions:

transcribe_audio(audio_bytes): transcribes recorded speech into text.
synthesize_speech(text): generates audio from the AI response.
main.py

Provides:

GET /health — health check.
POST /ingest/file — upload and index a document.
POST /ingest/url — index a website.
WS /ws/voice — process voice conversations.
9. Frontend Installation

From the project root:

npm create vite@latest frontend -- --template react
cd frontend
npm install


Create frontend/.env:

VITE_API_URL=http://localhost:8000
VITE_WS_URL=ws://localhost:8000/ws/voice


The frontend provides:

Microphone recording
Recording controls
Connection and processing status
Transcribed text
Generated answers
Audio playback
Retrieved source names
Document upload
Website ingestion

Use the React components and CSS described in this project's implementation guide for the complete interface.

10. RAG Workflow

The RAG workflow consists of two stages.

Indexing stage
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

Question-answering stage
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
Relevant text chunks
       |
       v
LLM + retrieved context
       |
       v
Generated answer
       |
       v
Speech synthesis


The assistant should use retrieved evidence for knowledge-base questions. If the available context is insufficient, it should explain that it cannot determine the answer from the indexed sources.

11. Multilingual Support

The system is designed for multilingual use.

Speech recognition converts spoken questions into text.
Multilingual embeddings support semantic retrieval across languages.
The LLM is instructed to answer in the user's language.
TTS generates audio from the response.

Test English, Hindi, mixed-language speech, and documents written in different languages.

Cross-language retrieval and pronunciation quality depend on the selected models and the actual documents. Validate these before deployment.

12. Running the Application

Run the backend in Terminal 1:

cd backend
uvicorn app.main:app --reload --port 8000


Run the frontend in Terminal 2:

cd frontend
npm run dev


Open:

Frontend: http://localhost:5173
Backend health check: http://localhost:8000/health
FastAPI documentation: http://localhost:8000/docs

The first backend launch may take additional time because the embedding model must be downloaded.

13. Testing Checklist
Backend starts successfully.
Frontend connects to the WebSocket.
Browser microphone permission works.
Audio is transcribed correctly.
A PDF can be uploaded.
A website can be indexed.
ChromaDB retrieves relevant passages.
The LLM answers from retrieved context.
The answer appears in the UI.
Audio playback works.
English questions work.
Hindi questions work.
Mixed-language questions are tested.
Errors are displayed clearly.
14. Production Improvements

Before deploying publicly, implement:

Authentication and per-user document permissions.
WebSocket authorization and rate limiting.
Secure website fetching to prevent SSRF.
File type validation and upload limits.
Retrieval relevance thresholds.
Better document chunking and source citations.
Persistent, isolated conversation histories.
Background document indexing.
Streaming speech recognition and TTS.
Voice activity detection and interruption handling.
HTTPS/WSS, logging, and monitoring.
API cost controls and timeouts.

The example implementation is a prototype and requires these protections before public deployment.

15. Future Enhancements
Continuous voice conversations
Streaming partial transcripts
Faster audio response delivery
Interruptible speech playback
Conversation memory
Authentication and user accounts
Database integration
Website crawling
Better retrieval with reranking
Source citations
Docker deployment
Cloud hosting
16. Useful Documentation
OpenAI API documentation: https://developers.openai.com/api/docs/
Speech-to-Text: https://developers.openai.com/api/docs/guides/transcription
Text-to-Speech: https://developers.openai.com/api/docs/guides/text-to-speech
FastAPI: https://fastapi.tiangolo.com/
React: https://react.dev/
Vite: https://vite.dev/
ChromaDB: https://docs.trychroma.com/
Sentence Transformers: https://www.sbert.net/
Conclusion

This project combines React, FastAPI, speech recognition, RAG, ChromaDB, an LLM, and speech synthesis into a multilingual voice assistant.

Start by getting document ingestion and one complete voice interaction working locally. Then improve retrieval quality and move toward low-latency streaming before deploying the application to production.
