# Chapter 12: Technical Architecture

## AIGINVEST Platform Architecture

**Version 1.0** | 2026

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                          Client Layer                                │
│                                                                      │
│  Browser (Next.js 14)              North Star ONE (Future)          │
│  ├── /login                        ├── AIOS Native App              │
│  ├── /chat (Diana UI)              └── Beam Me Up Sync              │
│  └── /projects, /documents                                          │
└────────────────────────┬────────────────────────────────────────────┘
                         │ HTTPS / SSE
┌────────────────────────▼────────────────────────────────────────────┐
│                          API Layer                                   │
│                                                                      │
│  NestJS 10 (Port 3333)                                              │
│  ├── /api/auth          AuthController + AuthModule                 │
│  ├── /api/chat          ChatController + ChatModule                  │
│  │   └── POST /stream   SSE streaming (text/event-stream)           │
│  ├── /api/projects      ProjectController + ProjectModule           │
│  ├── /api/documents     DocumentController                          │
│  └── /api/health        AppController                               │
│                                                                      │
│  AI Layer (apps/api/src/ai/)                                        │
│  ├── LLMService         Provider router (OpenAI/Anthropic/Ollama)   │
│  └── ContextEngine      Message builder + memory loader             │
└────────────────────────┬────────────────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────────────────┐
│                        Data Layer                                    │
│                                                                      │
│  PostgreSQL 16 (Port 5432)                                          │
│  ├── users              Identity + preferences                      │
│  ├── conversations      Chat threads                                │
│  ├── messages           Individual messages (role + content)        │
│  ├── projects           User projects                               │
│  ├── project_tasks      Tasks within projects                       │
│  ├── documents          Generated documents                         │
│  └── diana_memories     Long-term memory (category + key + value)   │
│                                                                      │
│  Redis 7 (Port 6379)                                                │
│  ├── Session cache      (future)                                    │
│  ├── Rate limiting      (future)                                    │
│  └── Real-time events   (future WebSocket broker)                   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Repository Structure

```
aig-platform/
├── apps/
│   ├── api/                    NestJS backend
│   │   └── src/
│   │       ├── ai/             LLM service + context engine
│   │       ├── auth/           Authentication controller
│   │       ├── chat/           Chat, Diana, documents
│   │       └── projects/       Project service + controller
│   └── web/                    Next.js frontend
│       └── app/
│           ├── chat/           Main chat interface
│           └── login/          Authentication page
├── packages/
│   ├── identity/               Auth package (JWT, bcrypt)
│   └── (future packages)
├── prisma/
│   ├── schema.prisma           Database schema (7 models)
│   └── migrations/             All applied migrations
├── infra/
│   └── docker/                 Dockerfiles
├── docker-compose.yml          Local dev (postgres + redis)
└── .env                        Environment configuration
```

---

## Key Architectural Decisions

### ADR-001: Email-only authentication (MVP)
**Decision:** No password required for Alpha. Email = identity.
**Reason:** Zero friction onboarding. Password can be added in v0.4.
**Status:** Active

### ADR-002: In-process LLM routing (no gateway)
**Decision:** Provider selection via env vars in LLMService, not a separate service.
**Reason:** Avoid premature infrastructure. Single service is simpler to deploy.
**Status:** Active

### ADR-003: SSE over WebSockets for streaming
**Decision:** Use Server-Sent Events for chat streaming.
**Reason:** Simpler protocol, works through proxies, one-directional (sufficient for streaming).
**Status:** Active

### ADR-004: PostgreSQL as primary store, Redis as cache
**Decision:** All persistent data in PostgreSQL. Redis for ephemeral data.
**Reason:** PostgreSQL is the source of truth. Avoids data sync complexity.
**Status:** Active

### ADR-005: Monorepo (pnpm workspaces + Turbo)
**Decision:** All code in one repository.
**Reason:** Single source of truth. Shared packages. Atomic commits.
**Status:** Active

### ADR-006: API URL configurable via env var
**Decision:** `NEXT_PUBLIC_API_URL` controls all API calls.
**Reason:** Same frontend code works in dev, staging, prod, and AIOS.
**Status:** Active

---

## Environment Variables

| Variable | Default | Purpose |
|----------|---------|---------|
| `DATABASE_URL` | postgres://aig:aig@localhost:5432/aig | PostgreSQL connection |
| `REDIS_URL` | redis://localhost:6379 | Redis connection |
| `PORT` | 3333 | API port |
| `LLM_PROVIDER` | mock | AI provider (openai/anthropic/ollama/mock) |
| `OPENAI_API_KEY` | — | OpenAI API key |
| `ANTHROPIC_API_KEY` | — | Anthropic API key |
| `OLLAMA_URL` | http://localhost:11434 | Ollama endpoint |
| `NEXT_PUBLIC_API_URL` | http://localhost:3333 | Frontend → API URL |

---

## Starting the Development Environment

```bash
# 1. Start infrastructure
docker-compose up -d postgres redis

# 2. Apply database migrations
npx prisma migrate dev

# 3. Start API
cd apps/api && node dist/main.js    # or: npm run dev

# 4. Start frontend
cd apps/web && npx next dev --port 3001

# 5. Run tests
cd apps/api && npx jest
```
