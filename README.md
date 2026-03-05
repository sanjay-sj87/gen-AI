# 📄 Document QA System — RAG Application

A **Retrieval-Augmented Generation (RAG)** system that lets you upload any PDF and ask natural language questions about it — powered by **Cohere** (free tier), **FAISS** vector search, and a **Streamlit** UI.

![Python](https://img.shields.io/badge/Python-3.8+-blue?logo=python)
![Streamlit](https://img.shields.io/badge/Streamlit-UI-red?logo=streamlit)
![LangChain](https://img.shields.io/badge/LangChain-LCEL-blueviolet)
![Cohere](https://img.shields.io/badge/LLM-Cohere-coral)
![FAISS](https://img.shields.io/badge/VectorDB-FAISS-yellow)
![License](https://img.shields.io/badge/License-MIT-green)

---

## 🚀 Features

- 📁 Upload any PDF and index it instantly
- 🔍 Semantic search via FAISS + HuggingFace embeddings (`all-MiniLM-L6-v2`)
- 🤖 Answers grounded strictly in document context — no hallucination
- 🟠 Powered by Cohere `command-r` family — **free tier, no credit card needed**
- ⚙️ Fully configurable at runtime — chunk size, overlap, top-k, temperature, model
- 📚 Shows retrieved source chunks with page numbers for full transparency
- 🔄 Auto re-indexes when settings are changed

---

## 🏗️ Architecture

```
PDF Upload
    │
    ▼
PyPDFLoader  ──►  RecursiveCharacterTextSplitter  (chunk_size=1000, overlap=200)
                              │
                              ▼
              HuggingFaceEmbeddings (all-MiniLM-L6-v2)
                              │
                              ▼
                    FAISS Vector Store
                              │
                              ▼  (on each query)
              Retriever (top-k most similar chunks)
                              │
                              ▼
              Cohere LLM  ←── Prompt Template + Context
                              │
                              ▼
                    Answer + Source Chunks → Streamlit UI
```

**Stack:** Streamlit · LangChain LCEL · FAISS · HuggingFace Sentence Transformers · Cohere API

---

## 🔑 Prerequisites

- Python 3.8+
- A free Cohere API key → [dashboard.cohere.com/api-keys](https://dashboard.cohere.com/api-keys) *(email signup only, no payment)*

---

## ⚙️ Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/document-qa-rag.git
cd document-qa-rag

# 2. Install dependencies
pip install -r requirements.txt

# 3. Set your Cohere API key
echo "COHERE_API_KEY=your_key_here" > .env

# 4. Launch the app
streamlit run app.py
```

---

## 📦 Key Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| `langchain` | 0.1.16 | RAG pipeline orchestration |
| `langchain-community` | 0.0.33 | PyPDFLoader, FAISS, HuggingFace integrations |
| `langchain-core` | 0.1.44 | LCEL chain composition |
| `streamlit` | 1.28.1 | Web UI framework |
| `cohere` | ≥ 5.0 | Cohere V2 client for LLM calls |
| `faiss-cpu` | 1.7.4 | Vector indexing and similarity search |
| `sentence-transformers` | 2.2.2 | HuggingFace embedding model runtime |
| `pypdf` | 3.17.1 | PDF text extraction |
| `python-dotenv` | 1.0.0 | `.env` file support for API key |

> Full list in `requirements.txt`

---

## 🛠️ Configuration

All settings are adjustable from the Streamlit sidebar at runtime:

| Parameter | Default | Range | Description |
|-----------|---------|-------|-------------|
| `llm_model` | `command-r-plus-08-2024` | — | Cohere model selection |
| `chunk_size` | `1000` | 300–2000 | Characters per document chunk |
| `chunk_overlap` | `200` | 50–500 | Character overlap between chunks |
| `retrieval_k` | `3` | 1–8 | Top-k chunks retrieved per query |
| `temperature` | `0.5` | 0.0–1.0 | LLM response randomness |
| `max_tokens` | `1024` | 256–4096 | Max tokens in generated answer |

---

## 🤖 Supported Cohere Models (Free Tier)

| Model | Notes |
|-------|-------|
| `command-r-plus-08-2024` | Best quality · RAG-optimised · **Recommended** ✅ |
| `command-r` | Fastest · Reliable ✅ |
| `command-r-plus` | High quality ✅ |
| `command-a-03-2025` | Latest · Most capable ✅ |

All models: **20 RPM · 128K context · $0 cost**

---

## 💡 How It Works

1. **Ingest** — PDF is loaded with `PyPDFLoader` and split into overlapping text chunks via `RecursiveCharacterTextSplitter`.
2. **Embed** — Each chunk is converted to a 384-dimensional dense vector using `all-MiniLM-L6-v2` (runs locally on CPU).
3. **Index** — All vectors are stored in a FAISS in-memory index using cosine similarity.
4. **Retrieve** — On each query, the top-k most semantically similar chunks are fetched from FAISS.
5. **Generate** — Retrieved chunks are injected as context into a prompt, and Cohere's LLM generates a grounded answer.
6. **Display** — The answer and all source chunks (with page numbers) are shown in the Streamlit UI.

---

## 📂 Project Structure

```
document-qa-rag/
├── app.py               # Main Streamlit application
├── requirements.txt     # Python dependencies (pinned versions)
├── .env                 # API key — not committed to git
├── .env.example         # Example env template
├── .gitignore
└── README.md
```

---

## 🔒 Environment Variables

Create a `.env` file in the project root:

```env
COHERE_API_KEY=your_cohere_api_key_here
```

You can also enter the key directly in the sidebar — it is never stored between sessions.

---

## 🆓 Cohere Free Tier

| Limit | Value |
|-------|-------|
| Requests per minute | 20 RPM |
| Context window | 128,000 tokens |
| Cost | **$0 — always free** |
| Signup requirement | Email only |

---

## 📊 Functional Requirements Checklist

| Requirement | Status |
|-------------|--------|
| Upload PDF document | ✅ |
| Chunk with RecursiveCharacterTextSplitter | ✅ |
| Embed with all-MiniLM-L6-v2 | ✅ |
| Store in FAISS | ✅ |
| Retrieve top-k chunks | ✅ |
| Generate answer via LLM | ✅ |
| Display answer + source chunks in Streamlit | ✅ |
| Loading spinner | ✅ |
| Configurable parameters via sidebar | ✅ |

---

## 🧩 Possible Extensions

- **Conversation memory** — Maintain chat history for follow-up questions
- **Multi-document upload** — Merge indexes from multiple PDFs
- **Chunk similarity scores** — Display cosine similarity alongside each retrieved chunk
- **Cross-encoder reranking** — Re-rank retrieved chunks for higher precision
- **Persistent vector store** — Replace FAISS in-memory with Chroma or Pinecone
- **Query rewriting** — Use the LLM to rephrase the query before retrieval

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

## 🙏 Acknowledgements

- [Cohere](https://cohere.com) — free LLM API optimised for RAG
- [LangChain](https://langchain.com) — RAG pipeline and LCEL orchestration
- [Streamlit](https://streamlit.io) — rapid web UI framework
- [HuggingFace](https://huggingface.co) — open-source embedding models
- [FAISS](https://github.com/facebookresearch/faiss) — fast vector similarity search by Meta AI
