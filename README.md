```md
# 🗂️ Media Service

Welcome 👋  
This repository contains a **media lifecycle microservice** with a **Vue 3 admin UI**, designed to safely manage files stored on **S3 or disk** — without ever accidentally deleting user data.

Think of it as:

> 🧹 **The janitor for your media files**  
> Careful, patient, and never deletes anything without permission.

---

## ✨ What This Service Does

- Stores media metadata in Postgres
- Tracks orphaned (unlinked) media
- Applies grace periods before deletion
- Deletes files from **S3 or disk** safely
- Provides a **Vue 3 admin dashboard**
- Scales to **millions of media records**
- Never blocks your main application

Your main app creates & links media  
This service cleans up responsibly 🧠

---

## 🧠 Design Philosophy

- ❌ No cascade deletes
- ❌ No cross-service foreign keys
- ❌ No file deletion during user requests
- ✅ Explicit lifecycle tracking
- ✅ Background cleanup jobs
- ✅ Grace periods before deletion
- ✅ Microservice-safe boundaries

If something breaks, **data stays safe**.

---

## 🧱 Tech Stack

### Backend

- **FastAPI** – async, fast, clean
- **Prisma (Python)** – shared schema, type-safe DB access
- **Postgres** – media metadata
- **Redis** – background jobs & retries
- **uv** – fast, deterministic Python package manager

### Frontend

- **Vue 3 + Vite** (development)
- **Vue 3 + Nginx** (production)
- **TypeScript**
- Internal admin UI (not user-facing)

### Infra

- **Docker & Docker Compose**
- Docker Compose **profiles** (`dev`, `prod`)
- `docker compose up --watch` for hot reload
- No Python or Node required on host

---

## 📁 Repository Structure

media-service/
├── backend/
│ ├── app/ # FastAPI app (MVC-style)
│ ├── prisma/ # Prisma schema & migrations
│ ├── Dockerfile
│ ├── pyproject.toml
│ └── uv.lock
│
├── frontend/
│ ├── src/ # Vue 3 app
│ ├── public/
│ ├── Dockerfile.dev # Vite dev server
│ ├── Dockerfile.prod # Vue build + Nginx
│ ├── nginx.conf
│ ├── package.json
│ └── vite.config.ts
│
├── docker-compose.yml
└── README.md

````

Backend and frontend are **siblings**, not tangled.

---

## 🚀 Getting Started (Docker-only)

### 1️⃣ Copy env file
```bash
cp backend/.env.example backend/.env
````

Fill in:

- Database URL
- S3 credentials (or disk config)

---

## 🔧 Development Mode (Recommended)

Uses:

- **FastAPI**
- **Vue + Vite (hot reload)**

```bash
docker compose --profile dev up --watch
```

Then open:

- 🔧 Backend API → [http://localhost:8000](http://localhost:8000)
- 📘 API Docs → [http://localhost:8000/docs](http://localhost:8000/docs)
- 🌐 Admin UI (Vue Dev) → [http://localhost:5173](http://localhost:5173)

Edit code → instant reload 🔥
No rebuilds. No restarts.

---

## 🚀 Production Preview Mode

Uses:

- **FastAPI**
- **Vue built & served by Nginx**

```bash
docker compose --profile prod up --build
```

Then open:

- 🌐 Admin UI (Nginx) → [http://localhost:8080](http://localhost:8080)
- 🔧 Backend API → proxied automatically via `/api`

This mode closely matches real production behavior.

---

## 🧹 Orphan Cleanup (How it works)

1. Main app unlinks media (sets media ID to `null`)
2. Main app notifies media service
3. Media is marked as:
   - `isOrphaned = true`
   - `orphanedAt = now`

4. Background job checks for expired orphans
5. Files are deleted from storage
6. Media row is removed from DB

All deletions are:

- Asynchronous
- Retriable
- Safe

---

## 🔗 How Services Link Media (Important)

This service **does NOT** use database foreign keys.

- Other services store `mediaId` as a plain number
- When an entity is deleted, it tells this service to unlink
- This service owns media lifecycle decisions

> Microservices don’t enforce relationships — they honor contracts.

---

## 🔐 Is this user-facing?

Nope 🙅
This is an **internal admin service**.

- No public uploads
- No user accounts
- No cross-service joins
- Easy to lock down later with auth

---

## 📈 Scalability Notes

This design works at scale because:

- Indexed orphan queries
- Batched deletions
- Async I/O
- Background workers
- No DB joins
- Stateless containers

Yes — **1M+ users is fine**.

---

## 🛠️ Things You Can Add Later

- Auth (JWT / internal token)
- Bulk delete actions
- Reference counting
- Metrics & alerts
- S3 lifecycle rules
- Retry dashboards
- Audit logs

The architecture already supports all of this.

---

## 🧠 Final Thought

This service exists so your main app can move fast
**without ever worrying about deleting the wrong file.**

Sleep better. Ship faster. 💤🚀
