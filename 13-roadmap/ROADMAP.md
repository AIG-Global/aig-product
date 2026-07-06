# Chapter 13: Roadmap

## AIGINVEST Release Roadmap

**Version 1.0** | 2026

---

## Versioning Principle

Every release must answer yes to one question:

> **Can a user do something today that they couldn't do yesterday?**

---

## Current Status

| Version | Name | Status | Date |
|---------|------|--------|------|
| **v0.1** | Meet Diana | ✅ Released | 2026-07-06 |
| **v0.2** | Real Diana | ✅ Released | 2026-07-06 |
| **v0.3** | Diana Works | 🔵 Next | — |
| **v0.4** | Connectivity | ⏳ Planned | — |
| **v0.5** | AIOS Prep | ⏳ Planned | — |
| **v1.0** | Public Launch | ⏳ Planned | — |

---

## v0.1 — Meet Diana ✅ COMPLETE

**Goal:** A new user can meet Diana.

- ✅ Email authentication (register/login)
- ✅ Diana welcome screen
- ✅ Real-time chat
- ✅ Conversation persistence (PostgreSQL)
- ✅ Conversation history sidebar
- ✅ Project creation via chat
- ✅ Document generation via chat
- ✅ Auto-titling of conversations

---

## v0.2 — Real Diana ✅ COMPLETE

**Goal:** Diana becomes an intelligent assistant that reasons, remembers, and acts.

- ✅ LLM Provider Router (OpenAI / Anthropic / Ollama / Mock)
- ✅ SSE streaming responses (word by word)
- ✅ Cancel generation (AbortController)
- ✅ Retry generation (one click)
- ✅ Long-term memory (DianaMemory table active)
- ✅ Context engine (system prompt + history + memories)
- ✅ Tool framework (create_project, create_document)
- ✅ Backend auto-titling
- ✅ E2E tests: 17/17 passing
- ✅ Redis infrastructure added

---

## v0.3 — Diana Works 🔵 NEXT

**Goal:** Diana helps users organize and execute real work.

### Sprint backlog

#### Tasks
- Create tasks from conversation: "Add a task to X project"
- Task status: todo / in_progress / done
- Task priority: low / medium / high
- List tasks by project

#### Notes
- Capture structured notes from conversation
- Auto-organize by project
- Search across notes

#### Kanban
- Visual project board driven by Diana
- Drag-drop (optional for MVP)
- Status columns configurable

#### Search
- Full-text search across conversations
- Search projects and documents
- Semantic search (via embeddings — future)

#### Mobile Optimization
- Responsive layout for all screens
- Touch-friendly input
- Proper viewport handling

#### Error States
- Graceful handling of API failures
- User-visible error messages
- Offline indicator

**Definition of Done:** A user can manage a complete project lifecycle (create → tasks → documents → notes) entirely through conversation with Diana.

---

## v0.4 — Connectivity ⏳ PLANNED

**Goal:** Diana connects teams.

- Team workspaces
- Shared projects and documents
- Role-based access (owner, editor, viewer)
- Real-time collaboration (WebSockets)
- Activity feed
- Notifications

---

## v0.5 — AIOS Prep ⏳ PLANNED

**Goal:** Prepare every decision for device deployment.

- `NEXT_PUBLIC_API_URL` fully configurable (done ✅)
- Local model support via Ollama (done ✅)
- Offline queue (messages sent when reconnected)
- Beam Me Up sync protocol design
- Sailfish OS integration research
- Device API abstraction layer

---

## v1.0 — Public Launch ⏳ PLANNED

**Goal:** First public release of AIGINVEST.

- All Alpha features polished
- Performance benchmarks met
- Security audit passed
- GDPR compliance verified
- Marketplace foundation
- Payments integration
- Public documentation
- Support system
- 99.9% uptime SLA

---

## Engineering Standards (Before Any Release)

Every release must pass:

- [ ] Build: all TypeScript compiles without errors
- [ ] Lint: no lint errors
- [ ] Tests: all E2E tests pass
- [ ] Manual: Product Acceptance Test completed
- [ ] Performance: response time < 200ms (non-streaming)
- [ ] Security: no new OWASP Top 10 issues introduced

---

## Product Acceptance Test (Run Before Every Release)

1. Can a new user register?
2. Does Diana greet them naturally?
3. Can they start a project?
4. Is the generated content useful?
5. Can they leave and continue later?
6. Would we confidently show this to a customer?

If any answer is "no" — fix before release.
