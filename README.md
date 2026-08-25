# 🔍 AI Research Agent — Apify + RAG Pipeline

> Autonomous research agent that scrapes live web data, embeds content into a vector pipeline, and answers queries in real time — fully orchestrated in n8n.

![n8n](https://img.shields.io/badge/n8n-Workflow-orange?style=flat-square&logo=n8n)
![OpenAI](https://img.shields.io/badge/OpenAI-Embeddings-412991?style=flat-square&logo=openai)
![Apify](https://img.shields.io/badge/Apify-Web%20Scraping-00B2FF?style=flat-square)
![RAG](https://img.shields.io/badge/Architecture-RAG-green?style=flat-square)

---

## 📌 Overview

This project is a fully autonomous AI research agent built in **n8n** that:

1. Accepts a research query via webhook or manual trigger
2. Scrapes live web data using **Apify actors**
3. Chunks and embeds content using **OpenAI Embeddings** (`text-embedding-3-small`)
4. Stores vectors in a **vector store** for semantic search
5. Retrieves the most relevant chunks via similarity search
6. Generates a final answer using **GPT-4o** with full source attribution

No manual intervention required — end-to-end agentic pipeline.

---

## 🏗️ Architecture

```
Webhook Trigger
      │
      ▼
Apify Actor (Web Scraper)
      │
      ▼
Text Chunker (Recursive Split)
      │
      ▼
OpenAI Embeddings (text-embedding-3-small)
      │
      ▼
Vector Store (Pinecone / Qdrant / Supabase pgvector)
      │
      ▼
Query → Similarity Search
      │
      ▼
GPT-4o (Answer Generation with Sources)
      │
      ▼
Respond to Webhook / Slack / Telegram
```

---

## 🛠️ Tech Stack

| Component | Tool |
|-----------|------|
| Orchestration | n8n (self-hosted) |
| Web Scraping | Apify (Website Content Crawler) |
| Embeddings | OpenAI `text-embedding-3-small` |
| Vector Store | Pinecone / Qdrant |
| LLM | GPT-4o |
| Trigger | Webhook / Manual |

---

## ⚙️ Setup

### Prerequisites
- n8n instance (self-hosted or cloud)
- Apify account + API token
- OpenAI API key
- Vector store account (Pinecone / Qdrant)

### Credentials Required in n8n
```
- OpenAI API
- Apify API
- Pinecone / Qdrant API
```

### Import Workflow
1. Download `workflow.json`
2. Open n8n → **Workflows** → **Import from file**
3. Connect your credentials
4. Set your vector store index name
5. Execute ✅

---

## 🔄 How It Works

### Step 1 — Trigger
Send a POST request with your research query:
```json
{
  "query": "What are the latest trends in agentic AI 2025?",
  "max_pages": 5
}
```

### Step 2 — Scraping
Apify's **Website Content Crawler** actor fetches live web pages based on the query. Results include page title, URL, and full text content.

### Step 3 — Embedding & Indexing
Content is split into ~500 token chunks with 50-token overlap, then embedded and stored in the vector store with metadata (source URL, timestamp).

### Step 4 — Retrieval & Answer
Top-k similar chunks are retrieved and passed to GPT-4o with a structured prompt. The final response includes the answer + source citations.

---

## 📊 Sample Output

```json
{
  "answer": "Agentic AI in 2025 is characterized by...",
  "sources": [
    { "url": "https://example.com/article1", "relevance": 0.94 },
    { "url": "https://example.com/article2", "relevance": 0.89 }
  ],
  "chunks_retrieved": 5,
  "tokens_used": 1842
}
```

---

## 💡 Use Cases

- Automated competitor research
- Real-time topic summarization
- Lead research enrichment
- Due diligence automation

---

## 👤 Author

**Zabir** — Freelance Automation Developer  
🔗 [Upwork Profile]([https://www.upwork.com](https://www.upwork.com/freelancers/~016038a1405b233337?mp_source=share) · 📧 Contact via GitHub

---

> Built with n8n + Apify + OpenAI | Chittagong, Bangladesh 🇧🇩
