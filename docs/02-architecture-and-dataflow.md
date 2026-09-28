# 02. Architecture & Dataflow

## 🏛️ System Topology & Architectural Layers

AcademicStack is structured across four primary layers:
1. **Presentation Layer (SPA Client):** Single Page Application built with React 19, Vite, and Zustand.
2. **API & Orchestration Layer (REST Gateway):** Asynchronous FastAPI application managing security, parsing, and pipelines.
3. **Core Intelligence & Rendering Layer:** LangChain RAG pipeline, OpenAI GPT-4o synthesis, and ReportLab PDF layout engines.
4. **Data & Persistence Layer:** PostgreSQL / SQLite relational database, Qdrant vector store, and Cloudinary CDN storage.

```mermaid
flowchart TB
    subgraph ClientLayer [Presentation Tier — React 19 + Vite]
        AppUI[Workspace & Landing Components]
        Stores[Zustand Stores: Auth, QuestionBank, Practice, Theme]
        RenderEngines[KaTeX Math & Mermaid.js Flowchart Engine]
        AxiosHTTP[Axios Client with Bearer Interceptors]
    end

    subgraph APILayer [API Gateway Tier — FastAPI]
        SecurityFilter[PyJWT Bearer Auth & Bcrypt]
        Routers[FastAPI Modular APIRouters]
        KeyService[Fernet AES Key Encryption / Decryption]
    end

    subgraph CoreEngines [Core Intelligence & Execution Tier]
        PyMuPDF[PyMuPDF Document Loader & Parser]
        VisionOCR[OpenAI Vision OCR Fallback]
        OpenAIRouter[LLM Router: gpt-4o-mini / gpt-4o]
        RAGPipeline[Two-Stage RAG Generation & Review Engine]
        PDFGenerators[ReportLab Multi-Format PDF Engines]
    end

    subgraph DataLayer [Storage & Persistence Tier]
        RDBMS[(Relational DB: PostgreSQL / SQLite)]
        VectorDB[(Qdrant Vector Database)]
        CloudStorage[(Cloudinary Document Storage)]
    end

    AppUI --> Stores
    Stores --> AxiosHTTP
    RenderEngines --> AppUI
    AxiosHTTP -- HTTPS/REST --> SecurityFilter
    SecurityFilter --> Routers
    Routers --> KeyService
    Routers --> RDBMS
    Routers --> PyMuPDF
    PyMuPDF --> VectorDB
    PyMuPDF -. Scanned PDF .-> VisionOCR
    VisionOCR --> OpenAIRouter
    Routers --> CloudStorage
    Routers --> OpenAIRouter
    Routers --> RAGPipeline
    RAGPipeline --> VectorDB
    RAGPipeline --> OpenAIRouter
    Routers --> PDFGenerators
```

---

## 🔄 End-to-End User Lifecycles & Lifecycles

### 1. Study Resource Upload & Vector Indexing Lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Student as Student (Client)
    participant Client as Frontend SPA
    participant API as FastAPI Backend
    participant CDN as Cloudinary Storage
    participant DB as Relational DB
    participant Embed as OpenAI Embeddings
    participant Qdrant as Qdrant Vector DB

    Student->>Client: Upload Lecture Notes / Slides PDF
    Client->>API: POST /api/resources (Multipart Form)
    API->>CDN: Upload Raw PDF
    CDN-->>API: Return secure_url & public_id
    API->>DB: INSERT Resource (status="uploaded")
    API-->>Client: Resource created (200 OK)

    Student->>Client: Click "Index for AI Search"
    Client->>API: POST /api/resources/{id}/index
    API->>DB: UPDATE Resource (status="indexing")
    API->>CDN: Download PDF bytes
    API->>API: Extract text via PyMuPDF
    API->>API: Split into chunks (1000 chars, 200 overlap)
    API->>API: Enrich metadata (resource_id, subject, chapters)
    API->>Qdrant: Query existing indexed chunk indices
    API->>Embed: Generate 1536-dim embeddings (batch size 16)
    Embed-->>API: Vector arrays
    API->>Qdrant: Upsert points with payload
    API->>DB: UPDATE Resource (status="indexed")
    API-->>Client: Return indexed chunk count
```

---

### 2. Question Bank Upload & Multi-Paper Extraction Lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Student as Student (Client)
    participant Client as Frontend SPA
    participant API as FastAPI Backend
    participant CDN as Cloudinary Storage
    participant Parser as Question Parser
    participant Vision as OpenAI Vision
    participant GPT as GPT-4o LLM
    participant DB as Relational DB

    Student->>Client: Upload Past Exam Paper(s)
    Client->>API: POST /api/question-banks (Multipart Form)
    API->>CDN: Upload exam PDF(s)
    API->>DB: INSERT QuestionBank (status="uploaded")
    API-->>Client: QuestionBank created

    Student->>Client: Click "Extract Questions"
    Client->>API: POST /api/question-banks/{id}/extract
    API->>DB: UPDATE QuestionBank (status="extracting")
    API->>API: Extract text via PyMuPDF
    alt Text extraction produces unmapped glyphs or <30 alnum chars
        API->>Vision: Transcribe PDF pages via OpenAI Vision
        Vision-->>API: OCR Transcribed text
    end
    API->>Parser: Format extraction prompt with group header rules
    Parser->>GPT: Execute extraction prompt (temperature=0.1)
    GPT-->>Parser: JSON Array of structured questions
    Parser->>Parser: Post-process explicit vs AI-estimated marks
    Parser->>DB: INSERT Question records (with marks, repeat_count)
    Parser->>DB: UPDATE QuestionBank (status="extracted")
    Parser-->>Client: Return question count & extracted items
```

---

### 3. Two-Stage Grounded RAG Solution Synthesis Lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Student as Student (Client)
    participant Client as Frontend SPA
    participant API as FastAPI Backend
    participant Qdrant as Qdrant Vector DB
    participant GPT as OpenAI LLM Router
    participant DB as Relational DB

    Student->>Client: Click "Generate Solutions"
    Client->>API: POST /api/answer-sets/generate
    API->>DB: INSERT AnswerSet (status="generating")
    loop For each Question in Question Bank
        API->>Qdrant: Similarity search (k=5, filter resource_ids)
        Qdrant-->>API: Top 5 relevant text chunks
        Note over API,GPT: Stage 1: Initial Solution Draft
        API->>GPT: Call call_generation() with DRAFT_SYSTEM_INSTRUCTION
        GPT-->>API: Draft answer (Quick Recall + Content + Math/Mermaid)
        Note over API,GPT: Stage 2: Senior Academic Review
        API->>GPT: Call call_review() with REVIEWER_SYSTEM_INSTRUCTION
        GPT-->>API: Polished, mark-scaled, sanitized answer
        API->>API: clean_answer_text() regex sanitization
        API->>DB: INSERT Answer record (status="completed")
        API->>DB: UPDATE AnswerSet (completed_questions += 1)
    end
    API->>DB: UPDATE AnswerSet (status="completed")
    API-->>Client: Complete AnswerSet with all answers
```

---

### 4. Zero-Assumption Exam Blueprint Prediction Lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Student as Student (Client)
    participant Client as Frontend SPA
    participant API as FastAPI Backend
    participant Predictor as Predictor Service
    participant GPT as GPT-4o LLM
    participant DB as Relational DB

    Student->>Client: Select up to 10 Past Papers or Question Banks
    Client->>API: POST /api/predictor/generate (Multipart)
    API->>Predictor: Ingest all paper texts (PDF upload or existing QBs)
    Predictor->>GPT: Execute SYSTEM_PROMPT (Phase 1 Blueprint Audit + Phase 2 Synthesis)
    GPT-->>Predictor: Structured JSON (exam_meta, pattern_insights, sections)
    Predictor-->>Client: Return predicted paper JSON
    opt Save to Question Banks
        Student->>Client: Click "Save as Question Bank"
        Client->>API: POST /api/predictor/save-as-qb
        API->>DB: INSERT QuestionBank & Question records
        API-->>Client: Question Bank created
    end
    opt Export University Exam PDF
        Student->>Client: Click "Download Exam Paper PDF"
        Client->>API: POST /api/predictor/pdf
        API->>API: Generate university paper layout via ReportLab
        API-->>Client: Streamed PDF binary download
    end
```

---

## 📊 Data Transformation Pipelines

### 1. Document to Vector Representation Pipeline
```
[Raw PDF File]
       │
       ▼ PyMuPDFLoader
[Document(page_content, page_number)]
       │
       ▼ RecursiveCharacterTextSplitter (chunk_size=1000, overlap=200)
[Text Chunks]
       │
       ▼ enrich_documents()
[Document with metadata: resource_id, subject, chapter, chunk_index]
       │
       ▼ OpenAI text-embedding-3-small
[1536-Dimensional Floating Point Vector]
       │
       ▼ Qdrant client.upsert() with COSINE distance
[Qdrant Collection: academicstack_resources_openai]
```

### 2. Multi-Paper Exam Paper to Question Record Pipeline
```
[Past Paper PDF / Scanned PDF]
       │
       ├─► PyMuPDF fitz.get_text() (if readable)
       └─► PyMuPDF pixmap render ──► OpenAI Vision (OCR Fallback)
       │
       ▼ Combined Clean Exam Text
       │
       ▼ GPT-4o Extraction Prompt (temperature=0.1)
[Raw JSON Array: question_number, question_text, marks, marks_source]
       │
       ▼ _clean_and_estimate_marks()
       ├─► Explicit Marks: Group header inheritance (e.g. Q.1 for 2 Marks)
       └─► AI Estimated Marks: Standard tiers (2, 5, 10) based on complexity
       │
       ▼ SQLAlchemy ORM
[Question Table Records: question_bank_id, marks, marks_source, repeat_count]
```

### 3. Grounded Answer to Multi-Format Export Pipeline
```
[Question + Top 5 Study Chunks]
       │
       ▼ Stage 1: Draft Generation (call_generation)
[Draft Answer with Quick Recall & Rough LaTeX/Mermaid]
       │
       ▼ Stage 2: Senior Reviewer (call_review)
[Refined, mark-scaled explanation strictly matching marks]
       │
       ▼ clean_answer_text() regex sanitization
[Stored in answers.content]
       │
       ├─► Frontend Client: KaTeX math + Mermaid.js vector rendering
       ├─► Solved Book PDF: ReportLab DejaVu Serif + mathrender PNGs
       └─► 2-Column Cheatsheet: ReportLab compact multi-column canvas
```
