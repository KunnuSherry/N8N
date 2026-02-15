<div align="center">

<!-- Animated Header Banner -->
<img src="https://capsule-render.vercel.app/api?type=venom&height=280&text=Local%20RAG%20Pipeline&fontSize=58&color=0:0f0c29,50:302b63,100:24243e&fontColor=ffffff&stroke=6c63ff&strokeWidth=2&animation=twinkling&desc=n8n%20%C2%B7%20Ollama%20%C2%B7%20PGVector%20%C2%B7%20Docker&descSize=22&descAlignY=75&descAlign=50" width="100%"/>

<br/>

<!-- Animated Typing SVG -->
<a href="https://git.io/typing-svg">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=18&pause=1000&color=6C63FF&center=true&vCenter=true&multiline=true&repeat=true&width=700&height=80&lines=100%25+Local+AI+%E2%80%94+No+API+Keys+Required+%F0%9F%94%92;Upload+Docs+%E2%86%92+Ask+Questions+%E2%86%92+Get+Answers+%F0%9F%A4%96;Self-Hosted+%C2%B7+Private+%C2%B7+Persistent+%C2%B7+Fast+%E2%9A%A1" alt="Typing SVG" />
</a>

<br/><br/>

<!-- Badges Row 1 -->
![Ollama](https://img.shields.io/badge/Ollama-llama3.2-FF6B6B?style=for-the-badge&logo=llvm&logoColor=white)
![pgvector](https://img.shields.io/badge/PostgreSQL-pgvector-4FC3F7?style=for-the-badge&logo=postgresql&logoColor=white)
![n8n](https://img.shields.io/badge/n8n-Workflow-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Container-2496ED?style=for-the-badge&logo=docker&logoColor=white)

<!-- Badges Row 2 -->
![Status](https://img.shields.io/badge/Status-Active-00D26A?style=flat-square&logo=statuspage&logoColor=white)
![Local AI](https://img.shields.io/badge/Cloud-Free%20%F0%9F%9A%AB-red?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey?style=flat-square)

</div>

---

<div align="center">

## ✨ What This Project Does

</div>

> **A fully private, self-hosted Retrieval Augmented Generation (RAG) system.**
> Upload any document, ask questions about it, and get accurate AI-powered answers — all running entirely on your own machine.

<br/>

<div align="center">

|  🔒 **100% Private**  |  🧠 **Local LLM**  |  🗃️ **Persistent DB**  |  ⚡ **Auto Ingestion**  |
|:---:|:---:|:---:|:---:|
| No cloud, no OpenAI | llama3.2 on-device | pgvector survives restarts | n8n orchestrates everything |

</div>

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                     LOCAL RAG PIPELINE                          │
│                                                                 │
│  📥 INGESTION FLOW                                              │
│  ─────────────────────────────────────────────────────────     │
│  Form Upload ──► Doc Loader ──► Text Splitter ──► Embeddings   │
│                                   (chunks)     (nomic-embed)   │
│                                                      │          │
│                                                      ▼          │
│                                              PGVector Store     │
│                                                      │          │
│  💬 QUERY FLOW                                       │          │
│  ─────────────────────────────────────────────────────────     │
│  Chat Input ──► Retriever ──► Context ──► llama3.2 ──► Answer  │
│                 (PGVector)   (top-k)    (AI Agent)              │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📋 Table of Contents

- [Step 1 — Install Ollama & Models](#step-1--install-ollama--models)
- [Step 2 — Setup Docker & PostgreSQL](#step-2--setup-docker--postgresql-pgvector)
- [Step 3 — Install Node.js & n8n](#step-3--install-nodejs--n8n)
- [Step 4 — Build the RAG Pipeline](#step-4--build-the-rag-pipeline)
- [Tech Stack](#-tech-stack)

---

## Step 1 — Install Ollama & Models

<img align="right" src="https://img.shields.io/badge/Required-Ollama-FF6B6B?style=for-the-badge&logo=llvm&logoColor=white"/>

### 📥 Install Ollama

Download and install from **[ollama.com/download](https://ollama.com/download)**

After installation, verify it's working:

```bash
ollama list
```

<br/>

### 🤖 Pull Required Models

We use two models:

| Model | Purpose | Command |
|-------|---------|---------|
| `llama3.2` | Text generation (LLM) | `ollama pull llama3.2` |
| `nomic-embed-text` | Vector embeddings | `ollama pull nomic-embed-text` |

```bash
# Pull the LLM
ollama pull llama3.2

# Pull the embedding model
ollama pull nomic-embed-text
```

<br/>

### 🚀 Start Ollama Server

```bash
ollama serve
```

> 💡 Ollama runs on `http://localhost:11434` by default. Keep this terminal open.

---

## Step 2 — Setup Docker & PostgreSQL (pgvector)

<img align="right" src="https://img.shields.io/badge/Required-Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white"/>

### ⚠️ Enable Virtualization (Windows Only)

> **This must be done before Docker will work on Windows.**

1. Restart your PC
2. Enter **BIOS** (press `DEL` or `F2` during boot)
3. Enable one of the following:
   - **SVM Mode** — for AMD processors
   - **Intel VT-x** — for Intel processors
4. Save and reboot

<br/>

### 🐧 Install WSL2 (Windows Only)

Open **PowerShell as Administrator** and run:

```powershell
wsl --install
```

Restart your system, then verify:

```powershell
wsl -l -v
```

<br/>

### 🐳 Install Docker Desktop

Download from **[docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)**

Start Docker Desktop and verify installation:

```bash
docker run hello-world
```

<br/>

### 🐘 Run PostgreSQL + pgvector Container

```bash
docker run -d \
  --name pgvector-container \
  -e POSTGRES_USER=myuser \
  -e POSTGRES_PASSWORD=mypassword \
  -e POSTGRES_DB=mydatabase \
  -p 5432:5432 \
  pgvector/pgvector:pg16
```

<br/>

### 🧩 Enable the Vector Extension

Connect to your database and enable pgvector:

```bash
# Connect to the container
docker exec -it pgvector-container psql -U myuser -d mydatabase
```

Then inside the `psql` prompt:

```sql
CREATE EXTENSION vector;
```

> ✅ You should see `CREATE EXTENSION` — the vector store is ready!

---

## Step 3 — Install Node.js & n8n

<img align="right" src="https://img.shields.io/badge/Required-Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white"/>

### 📦 Install Node.js

Download the **LTS version** from **[nodejs.org](https://nodejs.org)**

Verify installation:

```bash
node -v
npm -v
```

<br/>

### ⚡ Launch n8n

```bash
npx n8n
```

Once running, open your browser to:

```
http://localhost:5678
```

> 📁 n8n stores its local data at `C:\Users\<username>\.n8n` on Windows

---

## Step 4 — Build the RAG Pipeline

### 🔧 Workflow Components

<table>
<tr>
<td width="50%">

**📥 Ingestion Pipeline**

| # | Component | Role |
|---|-----------|------|
| 1 | Form Trigger | Accepts file uploads |
| 2 | Document Loader | Reads file content |
| 3 | Text Splitter | Chunks into pieces |
| 4 | Ollama Embeddings | Converts to vectors |
| 5 | PGVector Store | Persists vectors |

</td>
<td width="50%">

**💬 Query Pipeline**

| # | Component | Role |
|---|-----------|------|
| 1 | Chat Trigger | Receives question |
| 2 | KB Retrieval Tool | Searches PGVector |
| 3 | Context Builder | Prepares top chunks |
| 4 | AI Agent (llama3.2) | Generates answer |
| 5 | Response | Returns to user |

</td>
</tr>
</table>

<br/>

### 🔄 Complete Pipeline Flow

```
Document Upload
      │
      ▼
 ┌──────────┐     ┌──────────────┐     ┌─────────────┐     ┌────────────────────┐
 │  Doc     │────►│ Text         │────►│  Ollama     │────►│  PGVector Store    │
 │  Loader  │     │ Splitter     │     │  Embeddings │     │  (PostgreSQL)      │
 └──────────┘     └──────────────┘     └─────────────┘     └────────────────────┘
                    (chunks)             (nomic-embed)           (persisted)

User Question
      │
      ▼
 ┌──────────┐     ┌──────────────┐     ┌─────────────┐     ┌──────────┐
 │  Chat    │────►│  Retriever   │────►│  Context    │────►│ llama3.2 │────► Answer
 │  Trigger │     │  (PGVector)  │     │  (top-k)    │     │ AI Agent │
 └──────────┘     └──────────────┘     └─────────────┘     └──────────┘
```

### ⚙️ Connection Reference

```env
# Ollama
OLLAMA_HOST=http://localhost:11434
EMBED_MODEL=nomic-embed-text
LLM_MODEL=llama3.2

# PostgreSQL / pgvector
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_USER=myuser
POSTGRES_PASSWORD=mypassword
POSTGRES_DB=mydatabase

# n8n
N8N_URL=http://localhost:5678
```

---

## 🛠️ Tech Stack

<div align="center">

| Technology | Role | Version |
|:---:|:---:|:---:|
| <img src="https://img.shields.io/badge/Ollama-LLM%20%26%20Embeddings-FF6B6B?style=flat-square"/> | Local AI model server | Latest |
| <img src="https://img.shields.io/badge/llama3.2-Text%20Generation-FF8C42?style=flat-square"/> | Language model | 3.2 |
| <img src="https://img.shields.io/badge/nomic--embed--text-Embeddings-FFD166?style=flat-square"/> | Embedding model | Latest |
| <img src="https://img.shields.io/badge/PostgreSQL-Relational%20DB-4FC3F7?style=flat-square&logo=postgresql&logoColor=white"/> | Database engine | 16 |
| <img src="https://img.shields.io/badge/pgvector-Vector%20Extension-06D6A0?style=flat-square"/> | Vector similarity search | Latest |
| <img src="https://img.shields.io/badge/n8n-Workflow%20Automation-EA4B71?style=flat-square&logo=n8n&logoColor=white"/> | Pipeline orchestration | Latest |
| <img src="https://img.shields.io/badge/Docker-Containerization-2496ED?style=flat-square&logo=docker&logoColor=white"/> | Container runtime | Latest |
| <img src="https://img.shields.io/badge/Node.js-Runtime-339933?style=flat-square&logo=nodedotjs&logoColor=white"/> | JavaScript runtime | LTS |

</div>

---

<div align="center">

<!-- Animated footer wave -->
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:6c63ff,100:24243e&height=100&section=footer&animation=twinkling" width="100%"/>

**Built with ❤️ — 100% local, 100% private, 100% yours.**

`No OpenAI` · `No Cloud` · `No API Keys` · `Just your hardware`

</div>