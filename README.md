# 🎓 AcademicStack

> **Intelligent RAG-Grounded Exam Preparation, Blueprint Prediction & Peer Collaboration Platform for University Students**

[![FastAPI](https://img.shields.io/badge/Backend-FastAPI%200.141-009688.svg?style=flat&logo=fastapi)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/Frontend-React%2019%20%2B%20Vite%208-61DAFB.svg?style=flat&logo=react)](https://react.dev/)
[![TailwindCSS](https://img.shields.io/badge/Styling-TailwindCSS%20v4-38B2AC.svg?style=flat&logo=tailwind-css)](https://tailwindcss.com/)
[![Qdrant](https://img.shields.io/badge/Vector%20DB-Qdrant-DC2626.svg?style=flat&logo=qdrant)](https://qdrant.tech/)
[![OpenAI](https://img.shields.io/badge/LLM-OpenAI%20GPT--4o%20%2F%20text--embedding--3--small-412991.svg?style=flat&logo=openai)](https://openai.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

### 🔗 Quick Links & Repositories

| Resource | Link | Description |
| :--- | :--- | :--- |
| 🌐 **Live Application** | [academicstack.kshoeb.in](https://academicstack.kshoeb.in) | Production deployment of the full web application |
| 🎨 **Frontend Repository** | [github.com/kshxaib/as-frontend](https://github.com/kshxaib/as-frontend) | React 19, Vite 8, TailwindCSS v4, KaTeX & Zustand SPA |
| ⚙️ **Backend Repository** | [github.com/kshxaib/as-backend](https://github.com/kshxaib/as-backend) | FastAPI, Qdrant Vector Store, Two-Stage RAG & PDF Engines |
| 📚 **Modular Technical Docs** | [docs/README.md](file:///d:/Shoaib/AcademicStack/docs/README.md) | Exhaustive 9-part enterprise technical documentation suite |
| 📖 **Master Blueprint** | [PROJECT_DOCUMENTATION.md](file:///d:/Shoaib/AcademicStack/PROJECT_DOCUMENTATION.md) | Comprehensive project specification & source of truth |

---

## 🌟 Overview

**AcademicStack** is an end-to-end AI study revision and exam preparation platform designed for university students, study groups, and academic cohorts.

Instead of manually spending weeks searching for answers across hundreds of lecture slides and solving historical exam papers with generic, hallucination-prone AI prompts, AcademicStack provides:

1. **Strictly Grounded RAG Solutions**: Answers generated strictly from uploaded syllabus notes and lecture slides using dense vector search (`text-embedding-3-small` + Qdrant), eliminating AI hallucinations.
2. **Two-Stage Synthesis & Review Engine**: Drafts student-friendly explanations with **2-Minute Quick Recall** blocks, **KaTeX math formulas**, **Mermaid flowcharts**, and **marks-proportional length scaling**.
3. **Zero-Assumption Exam Paper Predictor**: Audits up to 10 years of historical question papers to extract exact examination blueprints, calculate module weights, and synthesize authentic high-probability model examination papers.
4. **1-Click PDF Export**: Generates full-length solved books, formal university exam question sheets, and ultra-compact **2-column examination revision cheatsheets** powered by ReportLab.
5. **The Commons (Community Hub)**: Discover peer-shared question banks, solved answer keys, and predicted papers with **1-click workspace cloning**.

---

## 🏗️ Architecture & Tech Stack

```mermaid
flowchart TB
    subgraph Client [Frontend SPA — React 19 + Vite 8]
        UI[Workspace & Landing UI]
        State[Zustand Persistent Stores]
        Render[KaTeX LaTeX + Mermaid.js Engine]
        Axios[Axios HTTP Client + Interceptors]
    end

    subgraph Server [Backend REST API — FastAPI]
        Auth[JWT Bearer & Bcrypt Auth]
        BYOK[Fernet Symmetric AES Key Decryption]
        Controllers[API Route Controllers]
    end

    subgraph AI_Engine [AI & Vector Pipelines]
        Embeddings[OpenAI text-embedding-3-small]
        LLM[OpenAI GPT-4o / GPT-4o-mini]
        Vision[OpenAI Vision OCR for Scanned PDFs]
        RAG[Two-Stage RAG Context Retriever]
    end

    subgraph Storage [Persistence & Storage]
        DB[(PostgreSQL / SQLite DB)]
        Qdrant[(Qdrant Vector Database)]
        Cloudinary[(Cloudinary Document CDN)]
    end

    subgraph PDF_Engines [ReportLab Custom Layout Engines]
        CheatPDF[2-Column Revision Cheatsheet]
        SolvedPDF[Full Solved Book PDF]
        ExamPDF[Predicted Model Exam PDF]
    end

    UI --> State --> Axios
    Axios -- JWT Bearer HTTP --> Auth --> Controllers
    Controllers --> BYOK --> AI_Engine
    Controllers --> DB
    Controllers --> Cloudinary
    AI_Engine --> Qdrant
    Controllers --> PDF_Engines
    Render --> UI
```

### Verified Technology Highlights

| Component | Technology | Description |
| :--- | :--- | :--- |
| **Frontend SPA** | React 19, Vite 8, JavaScript | Fast, interactive single-page application with responsive workspace shell |
| **Styling** | TailwindCSS v4, CSS Custom Properties | Curated warm-paper academic design tokens with editorial typography |
| **State Management**| Zustand v5 (Persisted) | Atomic, multi-store state synchronization with `localStorage` cache |
| **Math & Diagrams** | KaTeX, Mermaid.js v11, `react-markdown` | High-fidelity LaTeX math typography and vector flowcharts |
| **Backend API** | FastAPI 0.141, Python 3.12, Uvicorn | High-performance asynchronous RESTful backend |
| **Database & ORM** | PostgreSQL / SQLite, SQLAlchemy 2.0 | Relational schema with cascade rules and foreign-key integrity |
| **Vector Database** | Qdrant Cloud / Local Collection | Dense vector indexing (1536 dim) for chunked study materials |
| **LLM & Embeddings**| OpenAI GPT-4o / GPT-4o-mini / Vision | Grounded RAG synthesis, OCR transcription, and exam blueprint forecasting |
| **PDF Generation** | ReportLab 5.0, PyMuPDF | Custom canvas geometry, DejaVu typography, and multi-column pagination |
| **Cloud Storage** | Cloudinary CDN | Secure raw PDF document storage and signed streaming delivery |
| **Security** | Fernet AES-128-CBC, Bcrypt, PyJWT | Server-side encrypted Bring-Your-Own-Key (BYOK) architecture |

---

## 📂 Project Directory Structure

```text
AcademicStack/
├── as-frontend/                            # Frontend Single Page Application
│   ├── public/                             # Static assets & favicon
│   ├── src/
│   │   ├── api/
│   │   │   └── client.js                   # Axios HTTP client with Bearer auth interceptors
│   │   ├── components/
│   │   │   ├── ui/                         # Reusable UI atoms (Logo, Badges, EmptyState, ThemeToggle)
│   │   │   ├── LandingPage.jsx             # Public marketing page with 3D perspective preview
│   │   │   ├── WorkspaceLayout.jsx         # Authenticated app shell with sidebar navigation
│   │   │   ├── ResourceManager.jsx         # Study notes/slides upload & vector indexing tab
│   │   │   ├── QuestionBankManager.jsx     # Past exam paper upload & AI extraction tab
│   │   │   ├── QuestionReview.jsx          # Parsed question review, mark editing & RAG trigger
│   │   │   ├── SolutionViewer.jsx          # Grounded solution viewer, KaTeX, Mermaid & PDF export
│   │   │   ├── PredictedPaperGenerator.jsx # AI Exam blueprint & model paper predictor
│   │   │   ├── CommunityHub.jsx            # The Commons discovery feed & 1-click cloning
│   │   │   ├── ProfileSettings.jsx         # Encrypted OpenAI BYOK configuration
│   │   │   └── ...                         # Dedicated viewers and modals
│   │   ├── store/
│   │   │   ├── useAuthStore.js             # User session, JWT tokens, profile state
│   │   │   ├── useQuestionBankStore.js     # Primary data store for resources, QBs, questions, answers
│   │   │   ├── usePracticeStore.js         # Practice mode and active answer visibility state
│   │   │   └── useThemeStore.js            # Theme state & DOM synchronization
│   │   ├── App.jsx                         # Main app orchestrator & tab coordinator
│   │   └── index.css                       # Design tokens, typography & KaTeX overrides
│   ├── package.json                        # Frontend NPM dependencies
│   ├── vercel.json                         # Vercel SPA client rewrite rules
│   └── vite.config.js                      # Vite build configuration
│
├── as-backend/                             # FastAPI Python REST Backend
│   ├── app/
│   │   ├── answers/                        # RAG solution synthesis, retry & cheatsheet endpoints
│   │   ├── community/                      # Public discovery, sharing & 1-click cloning endpoints
│   │   ├── core/                           # Security, JWT encoding/decoding, configuration
│   │   ├── db/                             # SQLAlchemy engine, session, and relational models
│   │   ├── indexing/                       # PDF text chunking, embedding generation & Qdrant upsert
│   │   ├── llm/                            # Dynamic BYOK router & OpenAI completion wrappers
│   │   ├── parsing/                        # Multi-pass GPT-4o question paper parser
│   │   ├── pdf/                            # ReportLab PDF layout engines (Solved, Cheatsheet, Exam)
│   │   ├── predictor/                      # Statistical pattern analysis & exam blueprint synthesizer
│   │   ├── question_banks/                 # Question bank CRUD, file ingestion & PDF export
│   │   ├── questions/                      # Question CRUD & mark modification endpoints
│   │   ├── rag/                            # Vector embeddings, retriever & 2-stage generation
│   │   ├── resources/                      # Study resource upload, listing & Cloudinary storage
│   │   ├── storage/                        # Cloudinary document storage connector
│   │   ├── users/                          # User authentication, registration & AES key encryption
│   │   ├── utils/                          # Fernet AES encryption & error formatting
│   │   ├── vector_store/                   # Qdrant client & collection initialization
│   │   └── main.py                         # FastAPI application entrypoint & CORS middleware
│   ├── Dockerfile                          # Backend containerization Dockerfile (Python 3.12-slim)
│   ├── docker-compose.yml                  # Local development Postgres + Qdrant services
│   └── requirements.txt                    # Backend Python dependencies
│
├── docs/                                   # Enterprise Modular Documentation Suite
│   ├── README.md                           # Master sitemap & architecture overview
│   ├── 01-project-overview.md              # Vision, problems solved & core pillars
│   ├── 02-architecture-and-dataflow.md     # System topology, lifecycles & data pipelines
│   ├── 03-backend-architecture.md          # Framework, directory breakdown & services
│   ├── 04-frontend-architecture.md         # UI tech stack, stores, components & rendering
│   ├── 05-api-documentation.md             # Complete 34 REST endpoints & schemas
│   ├── 06-database-and-storage.md          # ER diagram, ORM models, Qdrant & Cloudinary
│   ├── 07-core-features-and-logic.md       # Two-Stage RAG, blueprint predictor & PDF engines
│   └── 08-setup-and-deployment.md          # Local runbook, Docker, Cloud & env variables
│
├── PROJECT_DOCUMENTATION.md                # Comprehensive Project Specification & Source of Truth
└── README.md                               # Project Landing & Overview
```

---

## ⚡ Quick Start Guide

### Prerequisites
- **Node.js**: v18.0.0 or higher & `npm`
- **Python**: v3.11 or higher (3.12 recommended)
- **OpenAI API Key** (or input your personal key via the app UI in Profile Settings)
- **Qdrant**: Cloud cluster URL/Key or local instance (`localhost:6333`)
- **Cloudinary Account**: Cloud Name, API Key, API Secret

---

### 1. Backend Setup

```bash
# 1. Navigate to backend directory
cd as-backend

# 2. Create and activate a Python virtual environment
python -m venv venv

# On Windows (PowerShell):
.\venv\Scripts\Activate.ps1
# On macOS / Linux:
source venv/bin/activate

# 3. Install Python dependencies
pip install -r requirements.txt

# 4. Generate Fernet encryption key for BYOK OpenAI keys:
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"

# 5. Configure environment variables
cp .env.example .env
# Edit .env with your credentials (DATABASE_URL, JWT_SECRET, ENCRYPTION_KEY, CLOUDINARY_*, QDRANT_*)

# 6. Start the FastAPI development server
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```
Backend API interactive documentation is available at `http://127.0.0.1:8000/docs`.

---

### 2. Frontend Setup

```bash
# 1. In a separate terminal, navigate to frontend directory
cd as-frontend

# 2. Install Node dependencies
npm install

# 3. Start the Vite development server
npm run dev
```

The frontend application will boot at **`http://localhost:5173`**.

---

### 3. Local Docker Services Setup (Alternative)

To quickly run PostgreSQL and Qdrant without local system installations:

```bash
# Inside as-backend/
docker compose up -d
```

---

## 🔑 Environment Variables Reference

### Backend (`as-backend/.env`)

```env
APP_NAME=AcademicStack
DEBUG=True

# Database (PostgreSQL recommended; SQLite supported)
DATABASE_URL=postgresql://academicstack:academicstack@localhost:5432/academicstack

# JWT Authentication
JWT_SECRET=your_secure_random_jwt_secret_key_here
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=43200

# Fernet Symmetric Encryption Key for User OpenAI BYOK Keys
ENCRYPTION_KEY=your_generated_fernet_key_base64_here

# Cloudinary Storage
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Qdrant Vector Store
QDRANT_HOST=localhost
QDRANT_PORT=6333
# For Qdrant Cloud:
# QDRANT_URL=https://your-qdrant-cluster.qdrant.tech
# QDRANT_API_KEY=your_qdrant_api_key

# CORS Whitelist (Comma-separated)
FRONTEND_URL=http://localhost:5173,http://127.0.0.1:5173
```

### Frontend (`as-frontend/.env`)

```env
VITE_API_URL=http://127.0.0.1:8000/api
```

---

## 📡 Core API Endpoints

| Category | Method | Endpoint | Description |
| :--- | :---: | :--- | :--- |
| **Auth** | `POST` | `/api/auth/register` | Register new student account |
| **Auth** | `POST` | `/api/auth/login` | Authenticate student & receive Bearer JWT |
| **Auth** | `GET` | `/api/auth/me` | Fetch active profile & key status |
| **Profile** | `PUT` | `/api/auth/profile/openai-key` | Save encrypted personal OpenAI key |
| **Resources**| `POST` | `/api/resources` | Upload study notes/slides PDF to Cloudinary |
| **Resources**| `POST` | `/api/resources/{id}/index` | Chunk & embed study material into Qdrant |
| **Question Banks** | `POST` | `/api/question-banks` | Upload past exam paper PDF |
| **Question Banks** | `POST` | `/api/question-banks/{id}/extract` | AI question extraction via GPT-4o & Vision OCR |
| **Answers** | `POST` | `/api/answer-sets/generate` | Generate grounded solutions via Two-Stage RAG |
| **Answers** | `GET` | `/api/answer-sets/{id}/pdf` | Download full Solved Book PDF |
| **Answers** | `GET` | `/api/answer-sets/{id}/cheatsheet-pdf` | Download 2-column compact Cheatsheet PDF |
| **Answers** | `POST` | `/api/answers/{id}/retry` | Re-solve single question with custom feedback |
| **Predictor** | `POST` | `/api/predictor/generate` | Synthesize predicted exam paper blueprint |
| **Predictor** | `POST` | `/api/predictor/pdf` | Export authentic university examination sheet PDF |
| **Predictor** | `POST` | `/api/predictor/save-as-qb` | Clone predicted paper into editable Question Bank |
| **Community** | `GET` | `/api/community/resources` | Discover shared study materials in The Commons |
| **Community** | `GET` | `/api/community/answer-sets` | Discover shared solved answer sets |
| **Community** | `POST` | `/api/community/.../clone` | 1-Click clone shared assets into private workspace |

For the complete endpoint specifications, see [docs/05-api-documentation.md](file:///d:/Shoaib/AcademicStack/docs/05-api-documentation.md).

---

## 📚 Modular Technical Documentation

For in-depth architectural guides, ER diagrams, prompt engineering specifications, and token design standards, consult the `/docs` suite:

- 🗺️ **[Master Sitemap & Overview](file:///d:/Shoaib/AcademicStack/docs/README.md)**
- 🎯 **[01. Project Overview & Vision](file:///d:/Shoaib/AcademicStack/docs/01-project-overview.md)**
- 🏛️ **[02. Architecture & Dataflow](file:///d:/Shoaib/AcademicStack/docs/02-architecture-and-dataflow.md)**
- ⚙️ **[03. Backend Architecture](file:///d:/Shoaib/AcademicStack/docs/03-backend-architecture.md)**
- 🎨 **[04. Frontend Architecture](file:///d:/Shoaib/AcademicStack/docs/04-frontend-architecture.md)**
- 📡 **[05. Complete API Documentation](file:///d:/Shoaib/AcademicStack/docs/05-api-documentation.md)**
- 🗄️ **[06. Database & Storage Architecture](file:///d:/Shoaib/AcademicStack/docs/06-database-and-storage.md)**
- 🧠 **[07. Core Features & Algorithmic Logic](file:///d:/Shoaib/AcademicStack/docs/07-core-features-and-logic.md)**
- 🚀 **[08. Setup, Configuration & Deployment](file:///d:/Shoaib/AcademicStack/docs/08-setup-and-deployment.md)**

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
