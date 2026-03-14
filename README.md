# 🧠 Max-Accuracy RAG Stack
## Free | CPU-Friendly | i5 13th Gen Ready

---

## 📁 Project Structure

```
rag_project/
├── docs/               ← DROP YOUR FILES HERE (.pdf, .txt, .md, .docx)
├── chroma_db/          ← Auto-created vector store (persists on disk)
├── graph_cache/        ← Auto-created GraphRAG cache (future use)
├── config.py           ← ALL your API keys go here
├── ingest.py           ← Run ONCE to index your documents
├── query.py            ← Run this to ask questions
├── pipeline.py         ← Core RAG pipeline (the brain)
├── requirements.txt    ← All dependencies
└── README.md           ← This file
```

---

## ⚡ Setup (5 minutes)

### Step 1 — Install dependencies
```bash
pip install -r requirements.txt
```

### Step 2 — Get your FREE API keys (all have free tiers)
| Service | Get Key At | Used For |
|---|---|---|
| Groq | https://console.groq.com | Fast routing, HyDE, CRAG gate |
| Gemini | https://aistudio.google.com | Final answer generation |
| Cohere | https://dashboard.cohere.com | Reranking (10k free/month) |
| Tavily | https://app.tavily.com | Web fallback (1000 free/month) |

### Step 3 — Add keys to config.py
```python
GROQ_API_KEY   = "gsk_..."
GEMINI_API_KEY = "AIza..."
COHERE_API_KEY = "..."
TAVILY_API_KEY = "tvly-..."
```

### Step 4 — Drop your documents into /docs
Supported: `.pdf`, `.txt`, `.md`, `.docx`, `.csv`

### Step 5 — Ingest
```bash
python ingest.py
```

### Step 6 — Query
```bash
python query.py
```

---

## 🏗️ How It Works (Pipeline)

```
User Query
    │
    ▼
[Groq] HyDE — generate hypothetical answer to improve retrieval
    │
    ▼
[Local CPU] Embed with BAAI/bge-large-en-v1.5 (no API needed)
    │
    ├──── Vector search ChromaDB   (top 15)
    ├──── BM25 keyword search      (top 10)
    │         └── Merge & dedupe → ~20 chunks
    ▼
[Cohere] Rerank → top 5 most relevant chunks
    │
    ▼
[Groq] CRAG Gate — "Is this context sufficient? Y/N"
    │         └── N → Tavily web search fallback
    ▼
[Groq] Self-critique — checks answer for hallucinations
    │
    ▼
[Gemini] Final answer generation
    │
    ▼
Answer + Source citations
```

---

## 💡 Tips

- Run `ingest.py` again whenever you add new documents
- Ingestion is the slow step (~2-5 min for large docs on CPU)
- Query is fast (~5-10 seconds end to end)
- Set `VERBOSE=True` in config.py to see every pipeline step
