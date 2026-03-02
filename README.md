# RetrievAI — Enterprise-Grade Access Control for AI Systems

> 🏆 **Distinguished Project Award** — Texas A&M University – Corpus Christi, December 2024

A dual-layer Role-Based Access Control (RBAC) system built on top of a Retrieval-Augmented Generation (RAG) pipeline, designed to solve a critical gap in enterprise AI deployments: **LLMs that hallucinate, leak sensitive data, and ignore organizational permissions.**

RetrievAI enforces fine-grained access control, eliminates unauthorized data disclosure, and delivers auditable, cited responses — making it suitable for security-sensitive enterprise environments.

---

## 🔒 Security-First Design

Most RAG systems focus on accuracy. RetrievAI focuses on **security and trust first.**

| Security Property | Implementation |
|---|---|
| Zero unauthorized disclosure | Dual-layer RBAC at retrieval + response time |
| No hallucinations | LLM restricted to retrieved evidence only |
| Auditability | Every response cites exact source files |
| Fail-safe behavior | Returns "Insufficient authorized information" when evidence is missing |
| Freshness without retraining | Hot-reload indexing on document updates |

---

## 📊 Quantitative Results

| Metric | Result |
|---|---|
| **RBAC Violations** | **0%** — zero unauthorized disclosures |
| Groundedness | 92% of answer sentences supported by retrieved evidence |
| Citation Accuracy | 95% file-title match under rehydration |
| Hallucination Reduction | 63% reduction vs. retrieval-only baseline |
| Refusal Rate | 17% — correct behavior when evidence is insufficient |
| Latency (local hardware) | P50: 180ms / P95: 430ms |

---

## 🏗️ How the Security Model Works

RetrievAI enforces permissions at **two independent layers**, preventing data leakage even in edge cases:

### Layer 1 — Folder-Level RBAC (Coarse-Grained)
Documents are stored under role-segregated directories:
```
Data/raw/
├── Public/       # All users
├── Internal/     # Internal + Private users only
└── Private/      # Admin only
```
Chunks outside a user's permission cascade are discarded before retrieval.

### Layer 2 — In-Document RBAC (Fine-Grained)
Mixed-sensitivity documents are split at section boundaries using explicit role markers:
```
CATEGORY: PUBLIC
CATEGORY: INTERNAL
CATEGORY: PRIVATE
```
Each chunk inherits its own section-level role — independent of the file's folder role.

### Permission Cascade
```
Public  →  Public chunks only
Internal  →  Internal + Public chunks
Private  →  Private + Internal + Public chunks
```

### Access Flow
```
User Login (Role) → RBAC Filter → Retriever (role-scoped) → LLM Generator → RBAC Filter → Cited Response
```
Even if a document lives in `Private/`, fine-grained Internal/Private chunks are withheld from lower-privilege users. **Zero leakage by design.**

---

## ✨ Key Features

- **Dual-RBAC Enforcement** — folder-level + in-document permission filtering
- **Dense Semantic Retrieval** — OpenAI `text-embedding-3-large` with NumPy vector similarity
- **Custom Chunker** — paragraph/sentence-aware segmentation with anchor tags (`FILE:<name> CATEGORY:<role>`) for exact rehydration and citation traceability
- **Hot-Reload Indexing** — Admin users can flag, upload, and trigger re-indexing with no server restart
- **Grounded Generation** — LLM answers only from retrieved evidence; refuses when context is insufficient
- **Token-Based Authentication** — role assignment enforced at login

---

## 🛠️ Tech Stack

| Layer | Tools |
|---|---|
| Backend | FastAPI, Uvicorn, Python 3.11 |
| Retrieval | NumPy, OpenAI Embeddings API |
| Document Handling | pypdf, regex-based role parser |
| Security | Dual RBAC, token-based authentication |
| Frontend | HTML / CSS / JS |
| Storage | Role-segregated local file system |

---

## ⚙️ Setup

```bash
# 1. Clone
git clone https://github.com/sreesus-1/RetrievAI.git
cd RetrievAI

# 2. Install dependencies
pip install -r requirements.txt

# 3. Configure environment
echo "OPENAI_API_KEY=your_key" > .env
echo "OPENAI_MODEL=gpt-4o-mini" >> .env

# 4. Start backend
uvicorn backend.main:app --reload

# 5. Open frontend
open frontend/index.html
```

---

## 📁 Project Structure

```
RetrievAI/
├── backend/
│   ├── main.py        # FastAPI router + endpoints
│   ├── auth.py        # Token-based authentication
│   ├── retriever.py   # Dense embedding index + chunk filtering
│   ├── indexer.py     # Chunking + embedding pipeline
│   └── chat.py        # RAG generation + refusal logic
├── Data/
│   └── raw/
│       ├── Public/
│       ├── Internal/
│       └── Private/
└── frontend/
    └── index.html
```

---

## 🧪 Experiment Summary

Three user roles were evaluated across 75 queries (25 per role) spanning academic, financial, administrative, and HR categories.

**Findings:**
- Public users received high-level summaries; sensitive content was withheld automatically
- Internal users received operationally detailed responses unavailable to public users
- Private/Admin users received full content including contact information and procedural data
- Document flagging → re-indexing → updated retrieval worked end-to-end with no server restart
- No role violations were observed across any test condition

---

## 📘 Use Cases

- Enterprise internal knowledge portals
- HR policy and compliance assistants
- University helpdesk systems
- Any environment requiring **safe, auditable LLM responses with access control**

---

## 🔭 Future Work

- Neural re-ranker for improved retrieval robustness under noisy inputs
- Cloud deployment with persistent vector storage (FAISS, Qdrant, Pinecone)
- Multi-agent verification pipeline
- User studies for usability and trust measurement

---

## 👥 Authors

- Alam K Sathya Chowdary LNU — Texas A&M University – Corpus Christi
- Ramya Sree Kanijam — Texas A&M University – Corpus Christi  
- Lakshmi Sahithi Likhya Paruchuri — Texas A&M University – Corpus Christi

---

*🏆 Distinguished Project Award — TAMU-CC, December 2024*
