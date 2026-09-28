# 01. Project Overview & Vision

## 🎯 Executive Summary & Mission

**AcademicStack** is an academic revision, exam prediction, and study resource collaboration platform purpose-built for university students and academic cohorts.

Traditional exam preparation forces students to navigate fragmented slide decks, disjointed lecture PDFs, and decades of past examination papers without verified answers. Generic public LLMs exacerbate this issue by introducing **hallucinations**, referencing out-of-syllabus methods, and producing answers poorly calibrated to university grading criteria.

AcademicStack eliminates this friction by coupling **strictly grounded dense vector retrieval (RAG)** with **dynamic blueprint pattern analysis** and **automated university-grade PDF rendering**. Students upload their syllabus notes and past exam papers, extract verified examination questions, synthesize structured solutions strictly tied to course materials, forecast high-probability upcoming exam blueprints, and collaborate across their cohort through 1-click workspace cloning.

---

## 🛑 Real-World Problems Solved

```
┌────────────────────────────────────────────────────────────────────────┐
│                        THE TRADITIONAL EXAM STRUGGLE                   │
│                                                                        │
│  1. Unindexed Lecture Slideware                                        │
│     Students possess hundreds of lecture slides and scanned textbook   │
│     chapters with no unified, semantic search capability.              │
│                                                                        │
│  2. Unsolved Historical Exam Papers                                    │
│     Past question papers lack official, step-by-step marking-scheme    │
│     answer keys.                                                       │
│                                                                        │
│  3. Public AI Hallucinations                                           │
│     Standard LLMs answer questions using extraneous internet facts or  │
│     incorrect mathematical notations not taught in the course syllabus.│
│                                                                        │
│  4. Time-Consuming Revision Material Prep                              │
│     Drafting concise 2-column revision cheatsheets before exams takes   │
│     dozens of manual typing hours.                                     │
│                                                                        │
│  5. Siloed Student Efforts                                             │
│     Every semester, batches of students duplicate the exact same effort│
│     transcribing and solving identical past papers.                    │
└────────────────────────────────────────────────────────────────────────┘
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        THE ACADEMICSTACK SOLUTION                      │
│                                                                        │
│  1. Fast Semantic Vector Indexing (Qdrant + OpenAI text-embedding-3)   │
│  2. Automated Layout & Multi-Paper Question Extraction (GPT-4o + OCR)  │
│  3. Two-Stage Grounded RAG with 2-Min Quick Recall & Mark Scaling      │
│  4. Zero-Assumption Historical Blueprint Predictor                     │
│  5. ReportLab PDF Solved Books & 2-Column Examination Cheatsheets      │
│  6. The Commons: Community Sharing with 1-Click Workspace Cloning      │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 🏛️ Core Architectural Pillars

AcademicStack is engineered on five architectural pillars:

### 1. Strict Syllabus Grounding (Zero Hallucinations)
Answers are never synthesized from unconstrained model memory. The backend executes dense vector similarity search across user-uploaded course slides and notes indexed in [Qdrant](file:///d:/Shoaib/AcademicStack/as-backend/app/vector_store/qdrant.py). Context chunks are injected into a two-stage synthesis and academic review pipeline, preventing out-of-scope concepts.

### 2. Marks-Proportional Scaling & Quick Recall
Exam answers must match grading criteria. AcademicStack implements strict length and depth scaling based on marks:
- **2 Marks:** Crisp definitions (~60–100 words), exactly 2 bullet points, no subheadings or unsolicited diagrams.
- **5–7 Marks:** Moderate depth (~200–300 words), core explanations, 4–6 bullet points, focused diagrams.
- **10+ Marks:** Comprehensive depth (~450–600 words), structured sub-sections, mandatory Mermaid diagram, and pros/cons analysis.
- **Every Answer:** Begins with a **⚡ 2-Min Quick Recall (Exam-Hall TL;DR)** block designed for high-yield 30-second review outside the exam hall.

### 3. Zero-Assumption Dynamic Blueprint Prediction
Rather than assuming a rigid, hardcoded question paper structure (such as 5 questions of 20 marks), the predictor engine audits uploaded past papers dynamically. It computes question frequencies, identifies choice pools (e.g. "Attempt any 4 of 6"), maps module weights, and synthesizes high-probability model exam papers matching the exact university format.

### 4. Bring-Your-Own-Key (BYOK) Security Model
To maintain operational sustainability and data isolation, students can provide their own OpenAI API key via their profile settings. Keys are encrypted server-side using Fernet symmetric encryption ([app/utils/encryption.py](file:///d:/Shoaib/AcademicStack/as-backend/app/utils/encryption.py)) with an environment encryption key, ensuring credentials are never exposed in plaintext.

### 5. High-Fidelity Multi-Format PDF Publishing
The platform incorporates custom ReportLab PDF engines ([app/pdf/](file:///d:/Shoaib/AcademicStack/as-backend/app/pdf/)) using bundled TrueType DejaVu typography to generate:
- Full-length, publication-grade **Solved Question Books** with KaTeX math and vector diagrams.
- Compact **2-Column Examination Revision Cheatsheets**.
- Authentic **University Model Examination Papers** with formal university headers and candidate instructions.

---

## ✅ Verified System Capabilities (Truth-Only Inventory)

The following capabilities are verified to physically exist and execute within the codebase:

| Capability | Module Path | Status | Verification Detail |
| :--- | :--- | :--- | :--- |
| **User Authentication** | [as-backend/app/users/](file:///d:/Shoaib/AcademicStack/as-backend/app/users/) | Active | Bcrypt password hashing, JWT Bearer tokens, profile management |
| **Encrypted BYOK Key Storage** | [as-backend/app/utils/encryption.py](file:///d:/Shoaib/AcademicStack/as-backend/app/utils/encryption.py) | Active | Fernet authenticated AES-128-CBC encryption for OpenAI keys |
| **Cloud PDF Storage** | [as-backend/app/storage/cloudinary.py](file:///d:/Shoaib/AcademicStack/as-backend/app/storage/cloudinary.py) | Active | Raw document upload, signed streaming download, public ID indexing |
| **PDF Text & Chunk Indexing** | [as-backend/app/indexing/](file:///d:/Shoaib/AcademicStack/as-backend/app/indexing/) | Active | PyMuPDF text loader, RecursiveCharacterTextSplitter (1000/200), Qdrant batching with 429 backoff |
| **Vector Embeddings** | [as-backend/app/rag/embeddings.py](file:///d:/Shoaib/AcademicStack/as-backend/app/rag/embeddings.py) | Active | OpenAI `text-embedding-3-small` (1536 dimensions) |
| **Question Extraction** | [as-backend/app/parsing/question_parser.py](file:///d:/Shoaib/AcademicStack/as-backend/app/parsing/question_parser.py) | Active | GPT-4o multi-pass parser with group header inheritance and explicit/estimated mark resolution |
| **Vision OCR Fallback** | [as-backend/app/llm/router.py](file:///d:/Shoaib/AcademicStack/as-backend/app/llm/router.py) | Active | OpenAI Vision transcription for scanned or unmapped font glyph PDFs |
| **Two-Stage RAG Generation** | [as-backend/app/rag/service.py](file:///d:/Shoaib/AcademicStack/as-backend/app/rag/service.py) | Active | Stage 1 draft generation + Stage 2 academic grading review and sanitization |
| **Single Answer Retry** | [as-backend/app/answers/service.py](file:///d:/Shoaib/AcademicStack/as-backend/app/answers/service.py) | Active | In-place answer re-synthesis with custom student instructions |
| **Dynamic Exam Predictor** | [as-backend/app/predictor/](file:///d:/Shoaib/AcademicStack/as-backend/app/predictor/) | Active | Multi-paper pattern discovery, blueprint synthesis, and clone-to-QB |
| **ReportLab PDF Generators** | [as-backend/app/pdf/](file:///d:/Shoaib/AcademicStack/as-backend/app/pdf/) | Active | Solved Book PDF, 2-Column Cheatsheet PDF, Questions PDF, Predicted Paper PDF |
| **The Commons (Community Hub)** | [as-backend/app/community/](file:///d:/Shoaib/AcademicStack/as-backend/app/community/) | Active | Public discovery of resources, QBs, answers, predicted papers, and 1-click cloning |
| **Practice & Flashcard Test Mode** | [as-frontend/src/store/usePracticeStore.js](file:///d:/Shoaib/AcademicStack/as-frontend/src/store/usePracticeStore.js) | Active | Blur/reveal self-testing, mastery tracking (`NEEDS_REVIEW`, `MASTERED`) |
| **KaTeX Math & Mermaid Diagrams** | [as-frontend/src/components/](file:///d:/Shoaib/AcademicStack/as-frontend/src/components/) | Active | In-browser LaTeX math rendering and Mermaid.js vector diagram rendering |

---

## 🚫 Explicit Scope & Non-Goals

To maintain truthfulness and prevent architectural drift, the following features are explicitly **out of scope** and **not implemented** in the current system:
- ❌ **No WebSockets or Real-Time Push**: Progress is polled via REST endpoints (`/api/answer-sets/{id}/progress`).
- ❌ **No GraphQL Interface**: All client-server communication uses standard HTTP/1.1 REST endpoints with JSON or Multipart Form data.
- ❌ **No Distributed Background Workers (Celery/RQ/Redis)**: Long-running synthesis tasks execute in-process via synchronous or async FastAPI request cycles.
- ❌ **No Client-Side In-Browser Cryptography**: BYOK keys are encrypted server-side via Python `Fernet` before writing to the database.
- ❌ **No Multi-LLM Provider Switching**: The active router strictly utilizes OpenAI models (`gpt-4o-mini`, `gpt-4o`, `text-embedding-3-small`).
