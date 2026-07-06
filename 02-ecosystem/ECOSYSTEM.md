# Chapter 2: The Complete AIG Ecosystem

## Everything AIGINVEST Builds

**Version 1.0** | 2026

---

## Ecosystem Overview

```
┌──────────────────────────────────────────────────────────────────┐
│                         AIGINVEST                                │
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │   Diana UI  │  │  AIOS       │  │   North Star ONE        │  │
│  │  (Web/App)  │  │  (OS Layer) │  │   (Hardware Device)     │  │
│  └──────┬──────┘  └──────┬──────┘  └─────────────┬───────────┘  │
│         │                │                       │              │
│  ┌──────▼────────────────▼───────────────────────▼──────────┐  │
│  │                    Ask Diana Service                       │  │
│  │         (Context Engine + Memory + Tool Runner)           │  │
│  └──────────────────────────┬───────────────────────────────┘  │
│                             │                                   │
│  ┌──────────────────────────▼───────────────────────────────┐  │
│  │                    Platform APIs                           │  │
│  │  Identity │ Projects │ Documents │ Tasks │ Marketplace    │  │
│  └──────────────────────────┬───────────────────────────────┘  │
│                             │                                   │
│  ┌──────────────────────────▼───────────────────────────────┐  │
│  │                    Infrastructure                          │  │
│  │       PostgreSQL │ Redis │ Object Storage │ CDN            │  │
│  └──────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Core Products

### Diana (AI Companion)
The primary interface for all AIGINVEST products.
- Conversational
- Persistent memory
- Tool execution (projects, documents, tasks)
- Available on every surface

### AIGINVEST Platform (Web)
The primary web application.
- Account management
- Projects and workspaces
- Documents and notes
- Team collaboration
- Marketplace

### AIOS (Operating System Layer)
The software layer that makes North Star ONE possible.
- Device integration
- Offline AI capabilities
- Beam Me Up synchronization
- Hardware optimization

### North Star ONE (Device)
The flagship phone powered by AIOS.
- Diana built-in, always available
- Privacy-first hardware
- Sailfish OS foundation
- Custom AI accelerator (future)

---

## The Five Workstreams

| # | Name | Mission |
|---|------|---------|
| 🟢 1 | Customer Experience | Make people fall in love with Diana |
| 🔵 2 | Platform | Keep the platform stable and debt-free |
| 🟣 3 | Diana Intelligence | Every sprint Diana becomes smarter |
| 🟡 4 | AIOS | Prepare for the future device |
| 🔴 5 | North Star ONE Hardware | Prepare the flagship phone |

---

## Platform Modules

### Identity
- User registration and authentication
- Organizations and roles
- SSO and enterprise identity
- Privacy controls

### Ask Diana
- Conversational AI interface
- Context engine
- Memory system
- Tool runner
- Provider router (OpenAI / Anthropic / Ollama)

### Projects
- Project creation from conversation
- Task management
- Milestone tracking
- Team workspaces

### Documents
- Document generation from conversation
- Rich text editor
- Version history
- Export (PDF, Markdown)

### AI Memory
- Long-term user memory
- Project context memory
- Preference learning
- Cross-session continuity

### Marketplace (Future)
- Skill extensions for Diana
- Third-party integrations
- Developer SDK
- Revenue sharing

### Payments (Future)
- Subscription management
- Enterprise billing
- Usage-based pricing

### Beam Me Up (AIOS Feature)
- Seamless sync between web and device
- Offline capability
- Conflict resolution
- Encrypted transport

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 14, React 18, TypeScript |
| Backend | NestJS 10, TypeScript, ESM |
| Database | PostgreSQL 16 (primary), Redis 7 (cache/sessions) |
| ORM | Prisma 5 |
| AI Providers | OpenAI GPT, Anthropic Claude, Ollama (local) |
| Container | Docker, Docker Compose |
| Cloud | Hetzner (primary), edge CDN |
| Monitoring | (TBD — Prometheus / Grafana) |

---

## Data Principles

1. **User data belongs to the user.** Export at any time.
2. **Encryption at rest and in transit.** Always.
3. **Memory is transparent.** Users can view and delete what Diana remembers.
4. **No training on user data** without explicit opt-in.
5. **GDPR compliant by design.** Not as an afterthought.

---

## Semantic Versioning

| Version | Milestone |
|---------|-----------|
| v0.1.x | Foundation (Meet Diana) |
| v0.2.x | Real Diana (LLM, Streaming, Memory) |
| v0.3.x | Productivity (Tasks, Notes, Kanban) |
| v0.4.x | AIOS Integration |
| v0.5.x | North Star ONE Device Support |
| v1.0.0 | Public Launch |
