# 🔍 AI Research Agent — Apify + RAG Pipeline in n8n

> Autonomous research agent that scrapes live web data, embeds content into a RAG vector pipeline, and answers queries in real time — fully orchestrated in n8n with no manual intervention.

![n8n](https://img.shields.io/badge/n8n-Workflow-orange?style=flat-square&logo=n8n)
![OpenAI](https://img.shields.io/badge/OpenAI-Embeddings%20%2B%20GPT--4o-412991?style=flat-square&logo=openai)
![Apify](https://img.shields.io/badge/Apify-Web%20Scraping-00B2FF?style=flat-square)
![RAG](https://img.shields.io/badge/Architecture-RAG%20Pipeline-green?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)

---

## 📌 Overview

This project is a fully autonomous AI research agent built in **n8n** that eliminates manual research work. Given a query, it:

1. Accepts a research topic via webhook trigger
2. Scrapes live web data using **Apify's Website Content Crawler**
3. Chunks and embeds content using **OpenAI `text-embedding-3-small`**
4. Stores vectors in a **vector store** for semantic search
5. Retrieves the most relevant chunks via similarity search
6. Generates a grounded answer using **GPT-4o** with full source attribution

**No manual steps. No hallucination. Sources included in every response.**

---

## 🏗️ Architecture

```
Webhook Trigger (POST /research)
         │
         ▼
  Apify Actor
  (Website Content Crawler)
  — fetches live web pages
         │
         ▼
  Text Chunker
  (Recursive split, ~500 tokens, 50-token overlap)
         │
         ▼
  OpenAI Embeddings
  (text-embedding-3-small)
         │
         ▼
  Vector Store
  (Pinecone / Qdrant / Supabase pgvector)
         │
         ▼
  Similarity Search
  (Top-k relevant chunks)
         │
         ▼
  GPT-4o
  (Answer generation + source citations)
         │
         ▼
  Webhook Response / Slack / Telegram
```

---

## 🛠️ Tech Stack

| Component | Tool |
|---|---|
| Orchestration | n8n (self-hosted or cloud) |
| Web Scraping | Apify — Website Content Crawler |
| Embeddings Model | OpenAI `text-embedding-3-small` |
| Vector Store | Pinecone / Qdrant / Supabase pgvector |
| LLM | OpenAI GPT-4o |
| Trigger | Webhook (POST request) |
| Output | JSON response + optional Slack / Telegram |

---

## ⚙️ Setup & Installation

### Prerequisites

- **n8n** instance (self-hosted via Docker or n8n Cloud)
- **Apify** account + API token → [apify.com](https://apify.com)
- **OpenAI** API key → [platform.openai.com](https://platform.openai.com)
- **Vector store** account — choose one:
  - [Pinecone](https://pinecone.io) (recommended for beginners)
  - [Qdrant](https://qdrant.tech) (self-hostable)
  - [Supabase](https://supabase.com) (pgvector — free tier available)

### Step 1 — Clone the Repository

```bash
git clone https://github.com/omaralzabir/ai-research-agent-n8n.git
cd ai-research-agent-n8n
```

### Step 2 — Import Workflow into n8n

1. Open your n8n instance
2. Go to **Workflows** → **Import from File**
3. Select `workflow.json` from this repository
4. Click **Import**

### Step 3 — Connect Credentials

In n8n, add the following credentials:

| Credential | Where to get it |
|---|---|
| OpenAI API | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) |
| Apify API | [console.apify.com/account/integrations](https://console.apify.com/account/integrations) |
| Pinecone API | [app.pinecone.io](https://app.pinecone.io) |

### Step 4 — Configure Vector Store

Set your vector store **index name** and **dimension** (`1536` for `text-embedding-3-small`) in the n8n Vector Store node.

### Step 5 — Activate & Test

```bash
# Send a test request
curl -X POST https://your-n8n-instance/webhook/research \
  -H "Content-Type: application/json" \
  -d '{
    "query": "What are the latest trends in agentic AI 2025?",
    "max_pages": 5
  }'
```

---

## 🔄 How It Works

### 1. Trigger

Send a POST request with your research topic:

```json
{
  "query": "Latest developments in n8n automation 2025",
  "max_pages": 5
}
```

### 2. Scraping

Apify's **Website Content Crawler** fetches live web pages relevant to the query. Each result includes:
- Page title
- Source URL
- Full extracted text content

### 3. Chunking & Embedding

Content is split into **~500 token chunks** with **50-token overlap** to preserve context across chunk boundaries. Each chunk is embedded using `text-embedding-3-small` and stored in the vector store with metadata:

```json
{
  "text": "chunk content...",
  "source_url": "https://example.com/article",
  "timestamp": "2025-07-28T10:00:00Z"
}
```

### 4. Retrieval & Answer Generation

The query is embedded and used for **similarity search** (top-5 chunks). Retrieved chunks are passed to GPT-4o with a structured prompt that enforces source attribution.

### 5. Response

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

- **Competitor research** — automated market intelligence gathering
- **Lead enrichment** — research prospects before outreach
- **Topic summarisation** — real-time synthesis of any subject
- **Due diligence** — rapid background research for business decisions
- **Content research** — feed a writing agent with verified, sourced data

---

## 📁 Repository Structure

```
ai-research-agent-n8n/
├── workflow.json          # n8n workflow export (import this)
├── README.md
└── docs/
    └── architecture.png   # Architecture diagram
```

---

## 🔧 Customisation

| Parameter | Where to change | Default |
|---|---|---|
| Max pages to scrape | Apify actor input | `5` |
| Chunk size | Text Splitter node | `500 tokens` |
| Chunk overlap | Text Splitter node | `50 tokens` |
| Top-k chunks retrieved | Vector Store node | `5` |
| LLM model | OpenAI node | `gpt-4o` |

---

## 👤 Author

**Omar Al Zabir** — n8n Automation Engineer & AI Workflow Developer
📧 omaralzabir7@gmail.com
🔗 [LinkedIn](https://www.linkedin.com/in/omaralzabir) · [GitHub](https://github.com/omaralzabir)
📍 Chittagong, Bangladesh

---

> Built with n8n · Apify · OpenAI · RAG Architecture | 🇧🇩 Chittagong, Bangladesh
