# 🚀 RAG Knowledgebase ChatBot

A Retrieval-Augmented-Generation (RAG) chatbot designed to turn your documents into an intelligent, answerable knowledgebase. Combine vector search over your documents with a powerful language model to provide accurate, context-aware answers — ideal for docs, support knowledgebases, internal wikis, and prototypes.

> Short: ingest documents → build vectors → query with a familiar chat interface backed by an LLM.

---

## ✨ Highlights

- Retrieval-Augmented Generation (RAG) architecture: answers grounded in your data, not hallucinated.
- Pluggable vector store: FAISS, Milvus, Weaviate, Pinecone, etc.
- Provider-agnostic LLM support: OpenAI, Azure OpenAI, Anthropic, or local LLMs (Llama, Mistral).
- Document ingestion pipeline: PDFs, Markdown, plain text, HTML, Office docs.
- Simple API + optional web UI for conversational flows.
- Tools for chunking, embedding, and context-management (LLM prompt templates, memory window).

---

## 📦 What’s inside

- Ingestion scripts for converting documents to text and creating embeddings.
- Index builder (vector store) and retrieval layer.
- Chat service that combines retrieved context with an LLM prompt to generate answers.
- Example clients (curl / Python) and a minimal web UI (optional) to demo chat flows.
- Config-driven setup for providers and index backends.

---

## 🚀 Quickstart (local)

Prereqs:
- Python 3.10+ (or your project’s specified runtime)
- pip or poetry
- A vector DB or FAISS local index
- LLM API key (e.g., OpenAI)

1. Clone
```bash
git clone https://github.com/LeelaKarthik-26/RAG_Knowladgebase_ChatBot.git
cd RAG_Knowladgebase_ChatBot
```

2. Create virtual env & install
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

3. Environment variables (example .env)
```env
# LLM provider
LLM_PROVIDER=openai
OPENAI_API_KEY=sk-...

# Vector store choice: faiss | pinecone | weaviate | milvus
VECTOR_STORE=faiss

# If using Pinecone/Weaviate/Milvus supply keys/urls below
PINECONE_API_KEY=
PINECONE_ENV=
WEAVIATE_URL=
MILVUS_HOST=
```

4. Ingest your documents
```bash
# Example: ingest a folder of docs into the vector store
python scripts/ingest.py --source ./docs --index-name my_knowledgebase
```

5. Run the API server
```bash
uvicorn app.main:app --reload --port 8000
# or
python -m app.main
```

6. Chat with curl
```bash
curl -X POST "http://localhost:8000/chat" \
  -H "Content-Type: application/json" \
  -d '{"question":"How do I reset my password?", "index":"my_knowledgebase"}'
```

---

## 🧠 How it works (high level)

1. Ingest documents → split into chunks with overlap → compute embeddings.
2. Store embeddings in a vector database.
3. At query time:
   - Retrieve top-K nearest passages based on embedding similarity.
   - Construct a context-aware prompt (system + retrieved docs + user message).
   - Send prompt to the LLM to generate final answer that cites or references the source passages.

Simple architecture:
- Ingestion -> Embeddings -> Vector DB
- API/Chat -> Retriever -> Prompt builder -> LLM -> Response

---

## 🔌 Supported components (examples)

- Embeddings: OpenAI embeddings, local embedding models (e.g., sentence-transformers)
- Vector stores: FAISS, Pinecone, Weaviate, Milvus
- LLMs: OpenAI GPT, Azure OpenAI, Anthropic, local LLMs via LLM runtimes
- File types: .pdf, .md, .txt, .html, .docx

---

## 🔧 Configuration & Prompts

- Prompt templates live in `app/prompts/` (or similar); customize to control style, length, and instruction-following.
- Retrieval settings you’ll likely tweak:
  - top_k (how many passages to retrieve)
  - chunk_size and chunk_overlap during ingestion
  - temperature, max_tokens for LLM generation

Example: Increase top_k to surface more context for complex questions.

---

## ✅ Best practices

- Keep chunks reasonably small (500–1000 tokens) with overlaps of ~100–200 tokens for context continuity.
- Use citation format or source metadata in the prompt to improve traceability.
- Limit context length given LLM token limits; use recency or relevance filters if needed.
- Periodically re-index after document updates.

---

## ⚡ Example: Python client

```python
import requests

resp = requests.post(
    "http://localhost:8000/chat",
    json={"question": "What is the refund policy?", "index": "my_knowledgebase"}
)
print(resp.json())
```

---

## 📚 Example prompts

System:
"You are a helpful assistant. Use the provided documents to answer the user's question. If the answer cannot be found, say you don't know and provide steps to find the answer."

User + retrieved context:
"[RETRIEVED_PASSAGES]\n\nUser: How do I configure SSO for my org?"

---

## 🛠️ Development

- Code style: follow black / isort / flake8 (if present)
- Tests: run `pytest` (if tests exist)
- Add new ingestors under `scripts/` or `app/ingestors/`
- Add new vector backends by implementing the connector interface in `app/vector_store/`

---

## 🤝 Contributing

Contributions welcome! Please:
1. Open an issue describing the feature/bug.
2. Fork and create a feature branch.
3. Make the change, add tests, and update docs.
4. Open a pull request referencing the issue.

See CONTRIBUTING.md for more details (if present).

---

## 🧾 License

This project is provided under the MIT License. See LICENSE for details.

---

## 📬 Need help?

Open an issue or contact the maintainer via GitHub: [LeelaKarthik-26](https://github.com/LeelaKarthik-26)

---

Made with ❤️ for faster, fact-grounded answers from your docs.
