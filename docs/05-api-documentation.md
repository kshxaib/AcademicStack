# 05. Complete API Documentation & Contracts

## 📡 API Overview & Standards

The AcademicStack API follows standard RESTful principles over HTTP/1.1:
- **Base URL:** `/api`
- **Content Types:** `application/json`, `multipart/form-data` (file uploads), `application/pdf` (binary streaming)
- **Authentication:** `Authorization: Bearer <jwt_access_token>`
- **Total Verified Endpoints:** **34** active routes

---

## 📋 Master Endpoint Catalog

| Group | Method | Endpoint Path | Auth Req. | Summary |
| :--- | :---: | :--- | :---: | :--- |
| **Auth** | `POST` | `/api/auth/register` | None | Register new student account |
| **Auth** | `POST` | `/api/auth/login` | None | Authenticate student and issue JWT |
| **Auth** | `GET` | `/api/auth/me` | Bearer | Get active student profile |
| **Auth** | `PUT` | `/api/auth/profile/openai-key` | Bearer | Save encrypted personal OpenAI key |
| **Auth** | `DELETE` | `/api/auth/profile/openai-key` | Bearer | Remove personal OpenAI key |
| **Users** | `POST` | `/api/users` | None | Create user record |
| **Users** | `GET` | `/api/users/{user_id}` | None | Get public user profile |
| **Resources** | `POST` | `/api/resources` | Form ID | Upload study notes / slides PDF |
| **Resources** | `GET` | `/api/resources` | Query ID | List user study resources |
| **Resources** | `GET` | `/api/resources/{resource_id}` | None | Get single resource details |
| **Resources** | `DELETE` | `/api/resources/{resource_id}` | None | Delete resource and Cloudinary asset |
| **Resources** | `GET` | `/api/resources/{resource_id}/download` | None | Download original resource PDF |
| **Indexing** | `POST` | `/api/resources/{resource_id}/index` | None | Chunk and vector-index resource into Qdrant |
| **Question Banks** | `POST` | `/api/question-banks` | Form ID | Upload exam paper PDF(s) and create Question Bank |
| **Question Banks** | `GET` | `/api/question-banks` | Query ID | List question banks |
| **Question Banks** | `GET` | `/api/question-banks/{question_bank_id}` | None | Get question bank details |
| **Question Banks** | `POST` | `/api/question-banks/{question_bank_id}/extract` | None | Trigger AI question extraction |
| **Question Banks** | `GET` | `/api/question-banks/{question_bank_id}/questions` | None | List extracted questions for question bank |
| **Question Banks** | `POST` | `/api/question-banks/{question_bank_id}/questions` | None | Manually add a new question to bank |
| **Question Banks** | `GET` | `/api/question-banks/{question_bank_id}/download` | None | Download source exam PDF or generated questions PDF |
| **Question Banks** | `GET` | `/api/question-banks/{question_bank_id}/questions-pdf` | None | Download clean questions-only PDF |
| **Question Banks** | `PATCH` | `/api/question-banks/{question_bank_id}/visibility` | None | Toggle QB visibility (`private` / `community`) |
| **Questions** | `PUT` | `/api/questions/{question_id}` | None | Update question text, marks, or number |
| **Questions** | `DELETE` | `/api/questions/{question_id}` | None | Delete question from question bank |
| **Answers** | `POST` | `/api/answer-sets/generate` | JSON ID | Trigger Two-Stage RAG solution generation |
| **Answers** | `GET` | `/api/answer-sets/{answer_set_id}` | None | Get complete answer set and solutions |
| **Answers** | `GET` | `/api/answer-sets/{answer_set_id}/pdf` | None | Download publication-grade Solved Book PDF |
| **Answers** | `GET` | `/api/answer-sets/{answer_set_id}/cheatsheet-pdf` | None | Download 2-column compact Cheatsheet PDF |
| **Answers** | `GET` | `/api/answer-sets/{answer_set_id}/progress` | None | Poll generation progress percentage |
| **Answers** | `POST` | `/api/answers/{answer_id}/retry` | None | Re-solve single question with custom instruction |
| **Answers** | `GET` | `/api/question-banks/{question_bank_id}/answer-sets` | None | List answer sets for a question bank |
| **Answers** | `PATCH` | `/api/answer-sets/{answer_set_id}/visibility` | None | Toggle answer set visibility |
| **Predictor** | `POST` | `/api/predictor/generate` | Form ID | Multi-paper blueprint audit & paper synthesis |
| **Predictor** | `POST` | `/api/predictor/save-as-qb` | JSON | Clone predicted paper into private Question Bank |
| **Predictor** | `POST` | `/api/predictor/pdf` | None | Download authentic University Exam Paper PDF |
| **Predictor** | `POST` | `/api/predictor/share` | None | Create public share link token |
| **Predictor** | `GET` | `/api/predictor/shared/{token}` | None | Retrieve shared predicted paper by token |
| **Community** | `GET` | `/api/community/resources` | None | Discover public study resources |
| **Community** | `POST` | `/api/community/resources/{resource_id}/share` | None | Toggle resource sharing |
| **Community** | `GET` | `/api/community/answer-sets` | None | Discover public solved answer sets |
| **Community** | `POST` | `/api/community/answer-sets/{answer_set_id}/share` | None | Toggle answer set sharing |
| **Community** | `POST` | `/api/community/answer-sets/{answer_set_id}/share-update` | None | Retire old versions and publish latest solution set |
| **Community** | `GET` | `/api/community/answer-sets/{answer_set_id}/answers` | None | Public read-only solution viewer |
| **Community** | `GET` | `/api/community/predicted-papers` | None | Discover public predicted exam papers |
| **Community** | `GET` | `/api/community/predicted-papers/{paper_id}` | None | Read public predicted exam paper details |
| **Community** | `POST` | `/api/community/predicted-papers/{paper_id}/toggle-share` | None | Toggle predicted paper visibility |
| **Community** | `GET` | `/api/community/question-banks` | None | Discover public question banks |
| **Community** | `POST` | `/api/community/question-banks/{qb_id}/share` | Bearer | Toggle QB sharing (creator check enforced) |
| **Community** | `GET` | `/api/community/question-banks/{qb_id}/questions` | None | Public question list viewer |
| **Community** | `POST` | `/api/community/question-banks/{qb_id}/clone` | Bearer | 1-Click clone question bank into private workspace |
| **Community** | `POST` | `/api/community/answer-sets/{answer_set_id}/clone` | Bearer | 1-Click clone solved answer set into workspace |
| **Community** | `POST` | `/api/community/predicted-papers/{paper_id}/clone` | Bearer | 1-Click clone predicted paper into workspace |
| **Health** | `GET` | `/api/health` | None | Health status of backend, DB, and Qdrant |
| **Health** | `GET` | `/api/health/db` | None | Database connection verification |
| **Health** | `GET` | `/api/health/qdrant` | None | Qdrant vector store connection verification |

---

## 🔒 1. Auth & Users Endpoints

### `POST /api/auth/register`
Creates a new student account and returns an access token.
- **Request Body (`application/json`):**
  ```json
  {
    "username": "student_scholar",
    "password": "strongPassword123",
    "name": "Jane Scholar"
  }
  ```
- **Response (`200 OK`):**
  ```json
  {
    "access_token": "eyJhbGciOiJIUzI1NiIsIn...",
    "token_type": "bearer",
    "user": {
      "id": 1,
      "username": "student_scholar",
      "name": "Jane Scholar",
      "has_openai_key": false,
      "created_at": "2026-09-29T03:00:00"
    }
  }
  ```

### `POST /api/auth/login`
Authenticates existing credentials and issues a JWT token.
- **Request Body (`application/json`):**
  ```json
  {
    "username": "student_scholar",
    "password": "strongPassword123"
  }
  ```
- **Response (`200 OK`):** Same schema as `/api/auth/register`.
- **Errors:** `401 Unauthorized` on invalid username or password.

### `GET /api/auth/me`
Retrieves the profile of the authenticated student.
- **Headers:** `Authorization: Bearer <token>`
- **Response (`200 OK`):**
  ```json
  {
    "id": 1,
    "username": "student_scholar",
    "name": "Jane Scholar",
    "has_openai_key": true,
    "created_at": "2026-09-29T03:00:00"
  }
  ```

### `PUT /api/auth/profile/openai-key`
Stores a user's OpenAI API key encrypted with Fernet symmetric encryption.
- **Headers:** `Authorization: Bearer <token>`
- **Request Body (`application/json`):**
  ```json
  {
    "openai_api_key": "sk-proj-..."
  }
  ```
- **Response (`200 OK`):** Returns updated `UserProfileResponse` with `has_openai_key: true`.

### `DELETE /api/auth/profile/openai-key`
Purges the stored encrypted OpenAI key for the active user.
- **Headers:** `Authorization: Bearer <token>`
- **Response (`200 OK`):** Returns updated `UserProfileResponse` with `has_openai_key: false`.

---

## 📚 2. Resources & Vector Indexing Endpoints

### `POST /api/resources`
Uploads a lecture slides or study notes PDF to Cloudinary.
- **Content-Type:** `multipart/form-data`
- **Form Parameters:**
  - `user_id` (integer, required)
  - `name` (string, required): e.g. "Lecture 1: Fuzzy Systems"
  - `subject` (string, required): e.g. "Soft Computing"
  - `chapters` (string, optional): e.g. "Module 1 & 2"
  - `description` (string, optional)
  - `visibility` (string, default "private"): `"private"` | `"community"`
  - `file` (binary PDF file, max 10MB)
- **Response (`200 OK`):**
  ```json
  {
    "id": 12,
    "user_id": 1,
    "name": "Lecture 1: Fuzzy Systems",
    "subject": "Soft Computing",
    "chapters": "Module 1 & 2",
    "description": null,
    "cloudinary_url": "https://res.cloudinary.com/.../raw/upload/...",
    "cloudinary_public_id": "academicstack/resources/...",
    "visibility": "private",
    "status": "uploaded",
    "created_at": "2026-09-29T03:05:00",
    "updated_at": "2026-09-29T03:05:00"
  }
  ```

### `POST /api/resources/{resource_id}/index`
Downloads the PDF from Cloudinary, chunks text into 1000-character segments, computes embeddings via `text-embedding-3-small`, and inserts points into Qdrant.
- **Response (`200 OK`):**
  ```json
  {
    "message": "Resource indexed successfully.",
    "resource_id": 12,
    "status": "indexed",
    "chunks_indexed": 48
  }
  ```
- **Errors:** `429 Too Many Requests` (OpenAI quota exhaustion), `500 Internal Server Error` on failure.

### `GET /api/resources/{resource_id}/download`
Streams the original PDF document with a clean attachment filename header.
- **Response (`200 OK`):** Binary `application/pdf`.

---

## 📝 3. Question Banks & Extraction Endpoints

### `POST /api/question-banks`
Creates a question bank record and uploads one or multiple past exam PDFs to Cloudinary.
- **Content-Type:** `multipart/form-data`
- **Form Parameters:**
  - `user_id` (integer, required)
  - `name` (string, required): e.g. "May 2025 Semester Exam"
  - `subject` (string, required): e.g. "Soft Computing"
  - `resource_ids` (string, comma-separated IDs): e.g. "12,13"
  - `files` (array of binary PDF files, required)
- **Response (`200 OK`):**
  ```json
  {
    "id": 5,
    "user_id": 1,
    "name": "May 2025 Semester Exam",
    "subject": "Soft Computing",
    "cloudinary_url": "https://res.cloudinary.com/...",
    "cloudinary_public_id": "academicstack/question_banks/...",
    "files_meta": "[{\"public_id\":\"...\",\"url\":\"...\",\"filename\":\"Exam.pdf\"}]",
    "resource_ids": "12,13",
    "status": "uploaded",
    "visibility": "private",
    "created_at": "2026-09-29T03:10:00",
    "updated_at": "2026-09-29T03:10:00"
  }
  ```

### `POST /api/question-banks/{question_bank_id}/extract`
Extracts all individual questions from the uploaded question paper using GPT-4o with automatic Vision OCR fallback.
- **Response (`200 OK`):**
  ```json
  {
    "message": "Questions extracted successfully.",
    "question_bank_id": 5,
    "status": "extracted",
    "questions_extracted": 14
  }
  ```

### `GET /api/question-banks/{question_bank_id}/questions`
Lists all structured questions belonging to the question bank.
- **Response (`200 OK`):**
  ```json
  {
    "questions": [
      {
        "id": 101,
        "question_bank_id": 5,
        "question_number": 1,
        "question_text": "State the difference between Fuzzy Logic and Crisp Logic.",
        "marks": 2,
        "marks_source": "explicit",
        "repeat_count": 3,
        "years_appeared": "2023, 2024, 2025",
        "created_at": "2026-09-29T03:12:00"
      }
    ]
  }
  ```

### `PUT /api/questions/{question_id}`
Updates the text, marks, or order of an extracted question.
- **Request Body (`application/json`):**
  ```json
  {
    "question_text": "Differentiate between Fuzzy Logic and Crisp Sets with truth tables.",
    "marks": 5,
    "question_number": 1
  }
  ```
- **Response (`200 OK`):** Updated `QuestionResponse`.

---

## 🧠 4. Answers & RAG Solution Endpoints

### `POST /api/answer-sets/generate`
Initiates two-stage RAG solution generation across all questions in the bank.
- **Request Body (`application/json`):**
  ```json
  {
    "question_bank_id": 5,
    "user_id": 1
  }
  ```
- **Response (`200 OK`):**
  ```json
  {
    "id": 8,
    "question_bank_id": 5,
    "user_id": 1,
    "status": "completed",
    "total_questions": 14,
    "completed_questions": 14,
    "visibility": "private",
    "created_at": "2026-09-29T03:15:00",
    "updated_at": "2026-09-29T03:18:00",
    "answers": [
      {
        "id": 301,
        "question_id": 101,
        "question_number": 1,
        "question_text": "State the difference between Fuzzy Logic and Crisp Logic.",
        "marks": 2,
        "repeat_count": 3,
        "years_appeared": "2023, 2024, 2025",
        "content": "> **⚡ 2-Min Quick Recall (Exam-Hall TL;DR)**\n> - **Fuzzy Logic**: Multi-valued logic...\n\n...",
        "sources": "Lecture 1: Fuzzy Systems (Page 4)",
        "status": "completed",
        "error_message": null
      }
    ]
  }
  ```

### `POST /api/answers/{answer_id}/retry`
Re-solves a single question with custom student guidance.
- **Request Body (`application/json`):**
  ```json
  {
    "user_instruction": "Include a comparison table comparing boundary values and membership functions."
  }
  ```
- **Response (`200 OK`):** Updated `AnswerResponse`.

### `GET /api/answer-sets/{answer_set_id}/pdf`
Downloads the complete publication-grade Solved Book PDF.
- **Response (`200 OK`):** Binary `application/pdf`.

### `GET /api/answer-sets/{answer_set_id}/cheatsheet-pdf`
Downloads the ultra-compact 2-Column Examination Revision Cheatsheet PDF.
- **Response (`200 OK`):** Binary `application/pdf`.

---

## 🔮 5. Exam Paper Predictor Endpoints

### `POST /api/predictor/generate`
Audits multiple past examination papers and synthesizes a high-probability model exam paper.
- **Content-Type:** `multipart/form-data`
- **Form Parameters:**
  - `subject` (string, required): Course title
  - `title` (string, required): e.g. "Model Examination Paper 2026"
  - `user_id` (integer, required)
  - `papers_meta` (JSON string array, optional)
  - `existing_qb_ids` (comma-separated QB IDs, optional)
  - `existing_qbs_meta` (JSON string array, optional)
  - `files` (array of uploaded PDF files, optional)
- **Response (`200 OK`):** Structured JSON containing `exam_meta`, `pattern_insights`, and `sections`.

### `POST /api/predictor/save-as-qb`
Converts a generated predicted paper into a real, editable Question Bank.
- **Request Body (`application/json`):**
  ```json
  {
    "user_id": 1,
    "paper_data": { ... }
  }
  ```
- **Response (`200 OK`):**
  ```json
  {
    "success": true,
    "question_bank_id": 9,
    "name": "BE SEM-VII Examination — Model Exam",
    "subject": "Soft Computing",
    "message": "Predicted paper saved to Question Banks successfully."
  }
  ```

### `POST /api/predictor/pdf`
Renders an authentic university examination sheet PDF from predicted paper data.
- **Response (`200 OK`):** Binary `application/pdf`.

### `POST /api/predictor/share`
Publishes a predicted paper to the public and generates a unique share token (`p_<token>`).
- **Response (`200 OK`):** Returns `share_token`, `creator_name`, and `visibility`.

---

## 🌐 6. The Commons (Community Hub) & 1-Click Cloning

### `GET /api/community/resources`
Lists all peer-shared study slide decks and notes (`visibility == "community"`).

### `GET /api/community/answer-sets`
Lists all peer-shared solved answer sets (`visibility == "community"`).

### `POST /api/community/question-banks/{qb_id}/clone`
Performs a 1-click clone of a shared Question Bank and all its structured questions into the authenticated user's workspace.
- **Headers:** `Authorization: Bearer <token>`
- **Response (`200 OK`):** Cloned `QuestionBank` object with total question count.

### `POST /api/community/answer-sets/{answer_set_id}/clone`
Clones both the underlying Question Bank and the complete set of solved answers into the student's workspace.
- **Headers:** `Authorization: Bearer <token>`
- **Response (`200 OK`):** Cloned `question_bank_id` and `answer_set_id`.

### `POST /api/community/predicted-papers/{paper_id}/clone`
Converts a community predicted paper into an editable private Question Bank.
- **Headers:** `Authorization: Bearer <token>`
- **Response (`200 OK`):** Cloned `QuestionBank` details.

---

## 🩺 7. System Health Endpoints

### `GET /api/health`
Monitors overall health and uptime:
- **Response (`200 OK`):**
  ```json
  {
    "status": "ok",
    "service": "AcademicStack",
    "database": "connected",
    "qdrant": "connected",
    "hit_count": 142,
    "last_hit_at": "2026-09-29 03:20:00 AM",
    "server_start_time": "2026-09-29 03:00:00 AM"
  }
  ```
