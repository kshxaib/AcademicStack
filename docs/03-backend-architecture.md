# 03. Backend Architecture

## ⚙️ Runtime, Framework & Core Technologies

The backend of AcademicStack is built with modern, asynchronous Python technologies:
- **Framework:** [FastAPI](https://fastapi.tiangolo.com/) `0.141.1` running on [Starlette](https://www.starlette.io/) `1.6.0`
- **ASGI Server:** [Uvicorn](https://www.uvicorn.org/) `0.52.4` with hot-reload in development
- **ORM & Database:** [SQLAlchemy](https://www.sqlalchemy.org/) `2.0.52` with connection pooling (`pool_pre_ping=True`) and `psycopg2-binary` `2.9.12`
- **Data Validation & Schemas:** [Pydantic](https://docs.pydantic.dev/) `2.13.4` and `pydantic-settings` `2.15.0`
- **Authentication & Security:** `PyJWT` `2.13.0` (HS256) and `bcrypt` `5.0.0`
- **Symmetric Encryption:** `cryptography` `50.0.0` (Fernet authenticated AES-128-CBC)
- **Vector Search & AI:** [Qdrant Client](https://qdrant.tech/) `1.19.0`, [LangChain](https://www.langchain.com/) `1.3.16`, `langchain-openai` `1.6.0`, `langchain-qdrant` `1.1.0`
- **PDF Extraction & Layout:** [PyMuPDF (fitz)](https://pymupdf.readthedocs.io/) `1.28.2` and [ReportLab](https://www.reportlab.com/) `5.0.1`
- **Cloud Media Storage:** [Cloudinary](https://cloudinary.com/) `1.46.0`

---

## 📂 Verified Physical Directory Structure

Every single file in this tree physically exists in the repository under [as-backend/app/](file:///d:/Shoaib/AcademicStack/as-backend/app/):

```text
as-backend/
├── app/
│   ├── answers/
│   │   ├── routes.py               # Answer set generation, progress, retry & PDF download routes
│   │   ├── schemas.py              # Pydantic models for AnswerSets, Answers, and Retries
│   │   └── service.py              # Business logic for generation, single-answer retry & response formatting
│   ├── community/
│   │   └── routes.py               # The Commons public discovery, sharing toggles & 1-click cloning
│   ├── core/
│   │   ├── config.py               # Pydantic / dotenv environment settings (JWT, Qdrant, DB)
│   │   └── security.py             # Bcrypt hashing, PyJWT creation/decoding, get_current_user dependencies
│   ├── db/
│   │   ├── database.py             # SQLAlchemy engine, SessionLocal factory, get_db dependency
│   │   ├── init_db.py              # Table metadata creation & PostgreSQL idempotent schema migrations
│   │   └── models.py               # SQLAlchemy ORM entity models (7 physical tables)
│   ├── indexing/
│   │   ├── routes.py               # Resource vectorization trigger endpoint
│   │   └── service.py              # PDF chunking, metadata enrichment & Qdrant batch upsert with backoff
│   ├── llm/
│   │   ├── router.py               # OpenAI model failover chain (gpt-4o-mini -> gpt-4o) & Vision OCR
│   │   └── service.py              # Raw OpenAI chat completion utilities with exponential retry
│   ├── parsing/
│   │   └── question_parser.py      # Multi-pass GPT-4o question extractor with group-header inheritance
│   ├── pdf/
│   │   ├── assets/
│   │   │   └── fonts/              # Bundled TrueType DejaVu font suite (Serif, Sans, Mono)
│   │   ├── cheatsheet_generator.py # ReportLab 2-column compact examination cheatsheet engine
│   │   ├── fonts.py                # Thread-safe DejaVu TrueType font registration & fallback
│   │   ├── generator.py            # ReportLab publication-grade Solved Question Book PDF engine
│   │   ├── mathrender.py           # LaTeX math expression rendering for PDF insertion
│   │   ├── predicted_paper_generator.py # University-format examination paper PDF engine
│   │   └── questions_generator.py  # Formatted question list PDF engine
│   ├── predictor/
│   │   ├── routes.py               # Multi-paper blueprint analysis, PDF export, share token endpoints
│   │   └── service.py              # Zero-assumption pattern discovery & exam blueprint synthesis
│   ├── question_banks/
│   │   ├── routes.py               # Question bank upload, listing, extraction trigger & PDF download
│   │   ├── schemas.py              # Pydantic models for QuestionBanks and Question lists
│   │   └── service.py              # File ingestion, question adding, and extraction coordination
│   ├── questions/
│   │   ├── routes.py               # Single question update (marks, text) and deletion endpoints
│   │   ├── schemas.py              # Pydantic schemas for Question creation and update
│   │   └── service.py              # CRUD service for individual questions
│   ├── rag/
│   │   ├── embeddings.py           # LangChain OpenAIEmbeddings factory (text-embedding-3-small)
│   │   ├── retriever.py            # Qdrant similarity retriever with metadata resource filtering
│   │   ├── service.py              # Two-stage RAG generation prompts, review stage, and sanitization
│   │   └── vector_store.py         # LangChain QdrantVectorStore instantiation helper
│   ├── resources/
│   │   ├── routes.py               # Study resource upload, listing, deletion & download endpoints
│   │   ├── schemas.py              # Pydantic schemas for study resource entities
│   │   └── service.py              # Cloudinary raw upload and DB resource lifecycle management
│   ├── storage/
│   │   └── cloudinary.py           # Cloudinary uploader, raw downloader & resource deletion helpers
│   ├── users/
│   │   ├── routes.py               # Auth endpoints (/auth/register, /auth/login, /auth/me, /auth/profile/openai-key)
│   │   ├── schemas.py              # Pydantic schemas for user registration, tokens, and profiles
│   │   └── service.py              # User authentication, registration, and Fernet key encryption
│   ├── utils/
│   │   ├── encryption.py           # Fernet symmetric encryption & decryption for user OpenAI API keys
│   │   └── error_messages.py       # OpenAI / provider quota error detection and user-friendly formatting
│   ├── vector_store/
│   │   └── qdrant.py               # Direct QdrantClient factory, collection management & vector deletion
│   └── main.py                     # FastAPI application factory, CORS middleware & health endpoints
├── Dockerfile                      # Production container definition (Python 3.12-slim, DejaVu fonts)
├── docker-compose.yml              # Local development services (PostgreSQL 16 + Qdrant)
└── requirements.txt                # Pinned production Python dependencies
```

---

## 🧩 Architectural Modules Breakdown

### 1. `app/core/` — Configuration & Security
- [config.py](file:///d:/Shoaib/AcademicStack/as-backend/app/core/config.py): Instantiates a global `settings` object loading `APP_NAME`, `DEBUG`, `DATABASE_URL`, `QDRANT_HOST`, `QDRANT_PORT`, `JWT_SECRET`, `JWT_ALGORITHM`, and `ACCESS_TOKEN_EXPIRE_MINUTES`.
- [security.py](file:///d:/Shoaib/AcademicStack/as-backend/app/core/security.py): Implements `hash_password(password)` and `verify_password(plain, hashed)` using `bcrypt`. Generates access tokens via `create_access_token(user_id, username)` with expiration defaulting to 30 days (`43200` minutes). Implements `get_current_user` and `get_current_user_optional` FastAPI dependencies using `HTTPBearer`.

### 2. `app/db/` — Relational Persistence & Migrations
- [database.py](file:///d:/Shoaib/AcademicStack/as-backend/app/db/database.py): Initializes `create_engine` with `pool_pre_ping=True` and yields database sessions via the `get_db()` dependency.
- [models.py](file:///d:/Shoaib/AcademicStack/as-backend/app/db/models.py): Defines 7 physical SQLAlchemy ORM models: `User`, `Resource`, `QuestionBank`, `Question`, `AnswerSet`, `Answer`, and `SharedPredictedPaper`.
- [init_db.py](file:///d:/Shoaib/AcademicStack/as-backend/app/db/init_db.py): Runs `Base.metadata.create_all(bind=engine)` followed by an idempotent PL/pgSQL migration block checking `information_schema.columns` and adding missing columns (`username`, `password_hash`, `openai_api_key_encrypted`, `visibility`, `pdf_url`, `repeat_count`, `years_appeared`, `files_meta`).

### 3. `app/llm/` & `app/rag/` — Intelligence & Two-Stage RAG
- [llm/router.py](file:///d:/Shoaib/AcademicStack/as-backend/app/llm/router.py): Dynamic OpenAI model execution with failover (`gpt-4o-mini` -> `gpt-4o`). Automatically parses rate-limit delays (`retry in X seconds`) and applies fast-failover when wait times exceed 10 seconds. Also houses `transcribe_image_with_vision()` for OCR fallback.
- [rag/embeddings.py](file:///d:/Shoaib/AcademicStack/as-backend/app/rag/embeddings.py): Provides LangChain `OpenAIEmbeddings` using `text-embedding-3-small` (1536 dimensions). Enforces that the key originates strictly from the authenticated user profile.
- [rag/retriever.py](file:///d:/Shoaib/AcademicStack/as-backend/app/rag/retriever.py): Constructs a LangChain `QdrantVectorStore` retriever filtering points where `metadata.resource_id` matches the list of selected resource IDs (top-k=5).
- [rag/service.py](file:///d:/Shoaib/AcademicStack/as-backend/app/rag/service.py): Implements the two-stage synthesis pipeline:
  - **Stage 1 (Draft):** Prompts the model with `DRAFT_SYSTEM_INSTRUCTION` to synthesize an initial solution with Quick Recall and mark scaling.
  - **Stage 2 (Review):** Re-evaluates the draft with `REVIEWER_SYSTEM_INSTRUCTION` (Senior Academic Reviewer prompt) to polish formatting, ensure Mermaid syntax validity, verify KaTeX formulas, and strip unwanted commentary.
  - `clean_answer_text()`: Regex cleanup stripping accidental rubric blocks or meta-commentary.

### 4. `app/parsing/` — Multi-Pass Question Extraction
- [question_parser.py](file:///d:/Shoaib/AcademicStack/as-backend/app/parsing/question_parser.py): Analyzes raw question paper text to extract individual questions. Contains strict rules for group header inheritance: if a paper declares "Q.1 for 2 Marks (Answer any 4)", all sub-questions inherit `marks=2` and `marks_source="explicit"`. If no marks exist anywhere in the paper, it calculates academic tiers (2, 5, or 10) and flags them as `marks_source="ai_estimated"`.

### 5. `app/predictor/` — Exam Blueprint Pattern Discovery
- [predictor/service.py](file:///d:/Shoaib/AcademicStack/as-backend/app/predictor/service.py): Ingests up to 10 past exam papers (or question banks). Uses `SYSTEM_PROMPT` to perform a Phase 1 blueprint audit (discovering main questions, choice pool sizes, time limits, module distributions) followed by Phase 2 synthesis of a model examination paper matching the university's exact layout.
- [predictor/routes.py](file:///d:/Shoaib/AcademicStack/as-backend/app/predictor/routes.py): Handles generation, PDF export, saving to private Question Banks, and public share token generation (`p_<random_token>`).

### 6. `app/pdf/` — ReportLab Custom Layout Engines
- [fonts.py](file:///d:/Shoaib/AcademicStack/as-backend/app/pdf/fonts.py): Registers bundled DejaVu TrueType fonts (`DejaVuSerif.ttf`, `DejaVuSans.ttf`, `DejaVuSansMono.ttf`) in normal, bold, italic, and bold-italic styles.
- [generator.py](file:///d:/Shoaib/AcademicStack/as-backend/app/pdf/generator.py): Compiles the full **Solved Question Bank PDF** with title page, table of contents, and styled answers.
- [cheatsheet_generator.py](file:///d:/Shoaib/AcademicStack/as-backend/app/pdf/cheatsheet_generator.py): Compiles the ultra-dense **2-Column Exam Cheatsheet PDF** using a dual-column layout frame.
- [predicted_paper_generator.py](file:///d:/Shoaib/AcademicStack/as-backend/app/pdf/predicted_paper_generator.py): Renders the authentic university exam paper format with university headers, candidate roll-number blocks, and choice instructions.
- [questions_generator.py](file:///d:/Shoaib/AcademicStack/as-backend/app/pdf/questions_generator.py): Compiles the official Question Sheet PDF.

---

## 🛡️ Error Handling, Quota Management & Rate Limiting

1. **Global Uncaught Exception Handler ([main.py](file:///d:/Shoaib/AcademicStack/as-backend/app/main.py)):**
   ```python
   @app.exception_handler(Exception)
   async def global_exception_handler(request, exc):
       return JSONResponse(
           status_code=500,
           content={"detail": f"Internal server error: {str(exc)}"},
       )
   ```
2. **Indexing Rate Limiter ([indexing/service.py](file:///d:/Shoaib/AcademicStack/as-backend/app/indexing/service.py)):**
   - Enforces a 60 requests-per-minute interval (`RateLimiter(requests_per_minute=60)`).
   - Catches HTTP 429 quota exhaustion errors during Qdrant batch upserts and applies exponential backoff:
     $$\text{wait\_seconds} = \min(6 \times 2^{\text{attempt}}, 60)$$
     or extracts delay directly from provider error messages.
3. **OpenAI Failover & Quota Detection ([utils/error_messages.py](file:///d:/Shoaib/AcademicStack/as-backend/app/utils/error_messages.py)):**
   - Translates raw OpenAI 429 errors into actionable user notifications explaining that their OpenAI API key quota is exhausted.
