# 🧠 Orchestrated RAG
## High-Accuracy Document Q&A System | CPU-Friendly | Multi-LLM Support

A production-grade Retrieval-Augmented Generation (RAG) system designed for maximum accuracy in document-based question answering. Features advanced retrieval techniques, contextual enrichment, and multi-model LLM orchestration.

---

## 📁 Project Structure

```
rag_project/
├── docs/               ← Drop your documents here (.pdf, .txt, .md, .docx)
├── qdrant_db/          ← Auto-created Qdrant vector store (persists on disk)
├── graph_cache/        ← Cache for future GraphRAG features
├── enrich_cache.json   ← Contextual enrichment cache
├── config.py           ← Configuration (API keys via environment variables)
├── .env                ← Your API keys (local development, not committed)
├── ingest.py           ← Document ingestion and indexing pipeline
├── query.py            ← Interactive Q&A interface
├── pipeline.py         ← Core RAG orchestration logic
├── tests.py            ← Test suite for validation
├── requirements.txt    ← Python dependencies
└── README.md           ← This file
```

## ✨ Key Features

- **Hybrid Retrieval**: Combines vector search (Qdrant) + BM25 keyword search
- **Contextual Enrichment**: LLM-generated context for each document chunk (35-49% retrieval improvement)
- **Advanced Reranking**: Cohere reranking for top-k selection
- **CRAG Gate**: Intelligent decision for web search fallback
- **Multi-LLM Support**: Groq (fast routing), OpenRouter (final generation)
- **Local Embeddings**: BAAI/bge-large-en-v1.5 (CPU-only, no API required)
- **Document Parsing**: Docling for structured document processing
- **Self-Critique**: Hallucination detection and correction
- **CPU-Friendly**: Optimized for consumer hardware (i5 13th gen ready)

## 🚀 Quick Start

### 1. Install Dependencies
```bash
pip install -r requirements.txt
```

### 2. Set Up API Keys
Get free API keys from these services:

| Service | URL | Purpose | Free Tier |
|---|---|---|---|
| Groq | https://console.groq.com | Fast LLM for routing, HyDE, CRAG | 30 RPM |
| OpenRouter | https://openrouter.ai | Final answer generation | Pay-per-use |
| Cohere | https://dashboard.cohere.com | Document reranking | 10k/month |
| Tavily | https://app.tavily.com | Web search fallback | 1000/month |

Create a `.env` file in the project root:
```bash
GROQ_API_KEY=your_groq_key_here
OPENROUTER_API_KEY=your_openrouter_key_here
COHERE_API_KEY=your_cohere_key_here
TAVILY_API_KEY=your_tavily_key_here
```

### 3. Add Documents
Place your documents in the `docs/` folder. Supported formats:
- PDF (.pdf)
- Word documents (.docx)
- Text files (.txt)
- Markdown (.md)
- CSV (.csv)

### 4. Ingest Documents
```bash
python ingest.py
```
This will parse documents, generate contextual enrichments, and build the vector index.

### 5. Ask Questions
```bash
python query.py
```
Interactive Q&A interface with rich formatting.

## 🏗️ Pipeline Architecture

```
User Query
    │
    ▼
[Query Decomposition] Split complex queries into sub-questions
    │
    ▼
[HyDE] Generate hypothetical answers for better retrieval
    │
    ▼
[Local Embeddings] BAAI/bge-large-en-v1.5 on CPU
    │
    ├──── Vector Search (Qdrant) → top 15 chunks
    ├──── BM25 Keyword Search → top 10 chunks
    │         └── Merge & dedupe → ~20 chunks
    ▼
[Cohere Rerank] → top 6 most relevant chunks
    │
    ▼
[Context Compression] Cross-encoder filtering
    │
    ▼
[CRAG Gate] "Sufficient context?" Y/N
    │         └── N → Tavily web search + merge
    ▼
[OpenRouter] Generate final answer
    │
    ▼
[Self-Critique] Check for hallucinations
    │
    ▼
Final Answer
```

## 🧪 Testing

Run the test suite:
```bash
python tests.py
```

Or test specific components:
```bash
python tests.py --test P-02
```

## 🔧 Configuration

Edit `config.py` to customize:
- Model selections
- Retrieval parameters (top-k values)
- Chunking settings
- API endpoints

## 📊 Performance

- **Accuracy**: Advanced techniques reduce retrieval failures by 35-49%
- **Speed**: CPU-optimized with fast local embeddings
- **Cost**: Leverages free tiers of multiple APIs
- **Hardware**: Runs on i5 13th gen or equivalent

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests: `python tests.py`
5. Submit a pull request

## 📄 License

MIT License - see LICENSE file for details.

---

*Built with LlamaIndex, Qdrant, and modern RAG techniques.*

---

## 💡 Tips

- Run `ingest.py` again whenever you add new documents
- Ingestion is the slow step (~2-5 min for large docs on CPU)
- Query is fast (~5-10 seconds end to end)
- Set `VERBOSE=True` in config.py to see every pipeline step
