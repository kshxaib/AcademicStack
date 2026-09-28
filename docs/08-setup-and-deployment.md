# 08. Setup, Configuration & Deployment Guide

## 📋 Prerequisites & Tooling Requirements

Before installing AcademicStack, ensure your environment meets these requirements:

| Tool | Minimum Version | Recommended | Notes |
| :--- | :--- | :--- | :--- |
| **Python** | 3.11+ | 3.12 | Required for `as-backend` |
| **Node.js** | 18.0.0+ | 20 LTS or 22 LTS | Required for `as-frontend` |
| **npm** | 9.0.0+ | 10+ | Node package manager |
| **Docker & Compose** | 24.0+ | Latest | Optional, for local PostgreSQL and Qdrant containers |
| **PostgreSQL** | 14+ | 16 | Relational database (SQLite supported in local dev) |
| **Qdrant** | 1.8+ | 1.19+ | Local instance or Qdrant Cloud cluster |
| **Cloudinary** | Free tier | Standard | For raw PDF document storage |
| **OpenAI API Key** | — | — | Can be set in `.env` or input via app UI in Profile Settings |

---

## 💻 Local Step-by-Step Installation Runbook

### 1. Clone & Inspect Repository
```bash
git clone https://github.com/kshxaib/AcademicStack.git
cd AcademicStack
```

---

### 2. Backend Setup (`as-backend`)

#### Step A: Create and Activate Virtual Environment
```bash
cd as-backend

# Windows (PowerShell)
python -m venv venv
.\venv\Scripts\Activate.ps1

# macOS / Linux
python3 -m venv venv
source venv/bin/activate
```

#### Step B: Install Python Dependencies
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

#### Step C: Generate Fernet Encryption Key & Configure `.env`
AcademicStack encrypts student OpenAI keys at rest using Python's `Fernet` symmetric cipher. Generate a valid key using this one-liner:

```bash
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```

Copy `.env.example` to `.env` and fill in the values:
```bash
cp .env.example .env
```

Edit `as-backend/.env`:
```env
# Application Configuration
APP_NAME=AcademicStack
DEBUG=True

# Database Configuration (PostgreSQL recommended; SQLite fallback)
DATABASE_URL=postgresql://academicstack:academicstack@localhost:5432/academicstack
# Or for local SQLite:
# DATABASE_URL=sqlite:///./academicstack.db

# JWT Authentication
JWT_SECRET=your_super_secret_jwt_key_here_change_in_production
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=43200

# Fernet AES Key Encryption for BYOK OpenAI Keys (Paste generated key here)
ENCRYPTION_KEY=your_generated_fernet_key_base64_here

# Qdrant Vector Store (Local or Qdrant Cloud)
QDRANT_HOST=localhost
QDRANT_PORT=6333
# For Qdrant Cloud, use URL and API Key:
# QDRANT_URL=https://your-cluster-url.qdrant.tech
# QDRANT_API_KEY=your_qdrant_api_key

# Cloudinary Storage
CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_API_SECRET=your_cloudinary_api_secret

# CORS Allowed Origins (Comma-separated)
FRONTEND_URL=http://localhost:5173,http://127.0.0.1:5173
```

#### Step D: Run Development Server
```bash
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```
- API root: `http://127.0.0.1:8000`
- Interactive Swagger docs: `http://127.0.0.1:8000/docs`
- Health check: `http://127.0.0.1:8000/api/health`

---

### 3. Frontend Setup (`as-frontend`)

#### Step A: Install Node Dependencies
```bash
# In a separate terminal window:
cd as-frontend
npm install
```

#### Step B: Configure Frontend `.env`
Create `as-frontend/.env`:
```env
VITE_API_URL=http://127.0.0.1:8000/api
```

#### Step C: Start Vite Development Server
```bash
npm run dev
```
The application will boot at **`http://localhost:5173`**.

---

## 🐳 Docker Support Services Setup

To quickly boot PostgreSQL 16 and Qdrant without installing them directly on your host machine, use the provided [as-backend/docker-compose.yml](file:///d:/Shoaib/AcademicStack/as-backend/docker-compose.yml):

```bash
cd as-backend
docker compose up -d
```

This starts:
- **PostgreSQL 16:** Port `5432:5432` with user `academicstack`, password `academicstack`, database `academicstack`.
- **Qdrant Vector DB:** Ports `6333:6333` (HTTP) and `6334:6334` (gRPC).

To stop the containers:
```bash
docker compose down
```

---

## 🔑 Complete Environment Variables Specification

### Backend Variables (`as-backend/.env`)

| Variable Name | Required | Default | Description |
| :--- | :---: | :--- | :--- |
| `APP_NAME` | No | `AcademicStack` | Service display name returned in health check |
| `DEBUG` | No | `True` | Enables debug logging and FastAPI debug mode |
| `DATABASE_URL` | **Yes** | — | SQLAlchemy connection URI (e.g. `postgresql://user:pass@host:5432/db` or `sqlite:///./academicstack.db`) |
| `JWT_SECRET` | **Yes** | Built-in fallback | Secret key for signing and verifying JWT tokens |
| `JWT_ALGORITHM` | No | `HS256` | JWT signing algorithm |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | No | `43200` | Token lifetime (default 30 days) |
| `ENCRYPTION_KEY` | **Yes** | — | 32-byte url-safe base64 string for Fernet AES encryption of OpenAI keys |
| `QDRANT_HOST` | No | `localhost` | Hostname for local Qdrant server |
| `QDRANT_PORT` | No | `6333` | HTTP port for local Qdrant server |
| `QDRANT_URL` | Optional | — | Cloud cluster endpoint (e.g. `https://xxx.qdrant.tech`) |
| `QDRANT_API_KEY` | Optional | — | API key for Qdrant Cloud cluster |
| `CLOUDINARY_CLOUD_NAME` | **Yes** | — | Cloudinary cloud identifier |
| `CLOUDINARY_API_KEY` | **Yes** | — | Cloudinary API access key |
| `CLOUDINARY_API_SECRET` | **Yes** | — | Cloudinary API access secret |
| `FRONTEND_URL` | No | Empty | Comma-separated CORS allowed origins |

### Frontend Variables (`as-frontend/.env`)

| Variable Name | Required | Default | Description |
| :--- | :---: | :--- | :--- |
| `VITE_API_URL` | **Yes** | `http://localhost:8000/api` | Full base URL of the FastAPI backend API |

---

## ☁️ Production Deployment Guide

### Backend Cloud Deployment (Render / Railway / Cloud Run)
The backend includes a production-ready [Dockerfile](file:///d:/Shoaib/AcademicStack/as-backend/Dockerfile) based on `python:3.12-slim`:
- Installs `build-essential`, `libpq-dev`, and system font packages (`fonts-dejavu-core`, `fonts-liberation`).
- Installs Python dependencies via `pip install --no-cache-dir -r requirements.txt`.
- Exposes port `10000` (Render default).
- Start command:
  ```bash
  uvicorn app.main:app --host 0.0.0.0 --port ${PORT:-10000}
  ```

### Frontend Cloud Deployment (Vercel)
The frontend includes [as-frontend/vercel.json](file:///d:/Shoaib/AcademicStack/as-frontend/vercel.json) configured for client-side SPA routing:
```json
{
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/"
    }
  ]
}
```
1. Connect the `as-frontend` directory to Vercel.
2. Set Build Command to `npm run build` and Output Directory to `dist`.
3. Configure `VITE_API_URL` to point to your live backend domain (e.g. `https://api.academicstack.kshoeb.in/api`).
