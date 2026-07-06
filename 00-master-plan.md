# AIGINVEST v1.0: Company Master Plan

**Version:** 1.0  
**Date:** 2026-07-06  
**Epoch:** A platform company. One capability per week. 90 days to v1.0.

---

## Mission Statement

> **Deliver the first publicly demonstrable AIGINVEST platform where Diana is the intelligent interface to work, knowledge, and productivity—and where every architectural decision prepares the path for AIOS and the North Star ONE device.**

---

## Organizational Structure

### Three Repositories

**1. aig-platform** (Source Code)
- Apps: API (NestJS), Web (Next.js)
- Services: Identity, AI, Chat, Projects, Documents, Tasks
- Libraries: Shared utilities, types, authentication
- Tests: E2E, unit, integration

**2. aig-product** (Product Design & Strategy)
- Chapter 01-13: Complete product vision
- Program definitions (10 programs)
- Core services layer
- Sprint planning (90 days)
- Marketing materials (future)

**3. ai-docs** (Engineering Playbook)
- Architecture decisions
- API documentation
- Deployment guides
- Best practices
- Troubleshooting

---

## The 10 Programs

| # | Program | Status | Timeline | Lead |
|---|---------|--------|----------|------|
| 1 | Identity | ✅ Complete | Alpha 0.1 | - |
| 2 | Diana | 🟡 Sprint 2-5 | Alpha 0.2 → Beta | - |
| 3 | Workspace | 🔵 Sprint 3 | Alpha 0.3 → Beta | - |
| 4 | Marketplace | 🔵 Sprint 4 | Alpha 0.3 → Beta | - |
| 5 | Business Cloud | ⏳ 2027 | Beta → v1.0 | - |
| 6 | AIOS | ⏳ 2027 | Beta → v1.0 | - |
| 7 | North Star ONE | ⏳ 2028 | Prototype → Launch | - |
| 8 | Developer Platform | 🟡 Sprint 2-5 | Alpha 0.2 → Beta | - |
| 9 | Enterprise | 🔵 Sprint 5 | Alpha 0.3 → Beta | - |
| 10 | Intelligence Platform | ✅ Complete | Alpha 0.2 | - |

---

## Timeline: 2026-2028

```
2026

Q2:
  Week 1-2:   Sprint 2 → Real AI integrated
  Week 3-4:   Sprint 3 → Workspace foundation
  Week 5-6:   Sprint 4 → Marketplace + Payments
  Week 7-8:   Sprint 5 → Enterprise foundation
  Week 9-10:  Sprint 6 → Advanced Diana
  Week 11-12: Sprint 7 → Polish + Performance
  Week 13:    Beta Release → v0.3.0-beta

Q3:
  Production stability
  Partner integration
  North Star prototype

Q4:
  v1.0 Release
  First paying customers
  Marketplace first creators

---

2027

Q1:
  International expansion (EU, APAC)
  Mobile app launch
  Marketplace growth

Q2:
  AIOS developer preview
  North Star developer edition (100 units)
  Enterprise launch

Q3:
  Scale operations
  Marketplace $100K+ revenue
  AIOS beta program

Q4:
  Prepare for 2028 launch
  Refine North Star
  Enterprise partnerships

---

2028

Q1:
  North Star ONE launch
  Marketplace launch (public)
  International growth

Q2-Q4:
  Scale production
  Build AIOS ecosystem
  Achieve profitability
```

---

## Core Architecture

### Data Flow

```
User (Browser)
    ↓
Diana Interface (Next.js)
    ↓
Chat Controller (NestJS)
    ↓
Tool Router (Intent detection)
    ├─→ Create Project/Document/Task
    ├─→ Update Project/Document/Task
    └─→ Query/Search
    ↓
Context Engine (Build LLM prompt)
    ├─ System prompt
    ├─ Conversation history
    └─ User memories
    ↓
AI Router (Select provider)
    ├─ OpenAI (GPT-4)
    ├─ Anthropic (Claude)
    ├─ Ollama (Local)
    └─ Custom models
    ↓
LLM (Generate response)
    ↓
Tool Executor (If tool called)
    ├─ ProjectService
    ├─ DocumentService
    ├─ TaskService
    └─ Other services
    ↓
Memory Extraction
    └─→ Save to DianaMemory
    ↓
Event Publishing
    └─→ Notify subscribers
    ↓
Streaming Response (SSE)
    └─→ Browser (word-by-word)
    ↓
User sees response
```

### Services Layer

**Core Services (Shared Infrastructure)**
1. Identity Service — Auth, users, orgs, teams, roles
2. AI Router — Multi-provider LLM orchestration
3. Memory Service — Long-term learning
4. Notification Service — Alerts, reminders, messages
5. Payment Service — Billing, revenue distribution
6. Storage Service — File storage, versioning
7. Search Service — Full-text + semantic search
8. Analytics Service — Usage tracking, metrics
9. Event Service — Pub/sub for real-time events
10. Audit Service — Compliance logging
11. Security Service — Encryption, key management

**Application Services (Specific to Programs)**
- Diana Service — AI orchestration, tool calling
- Workspace Service — Projects, documents, tasks
- Marketplace Service — Skills, templates, integrations
- Business Cloud Service — CRM, ERP, HR, Finance
- Enterprise Service — Advanced RBAC, SSO, compliance

---

## Technology Stack

### Frontend
- **Framework:** Next.js 14 (React 18)
- **Styling:** Tailwind CSS
- **State:** React Context + Hooks
- **Components:** Headless UI, Radix UI
- **Rich Text:** Tiptap (markdown + visual)
- **Icons:** Heroicons
- **Charts:** Recharts

### Backend
- **Runtime:** Node.js 20
- **Framework:** NestJS 10
- **ORM:** Prisma 5
- **Database:** PostgreSQL 16
- **Cache:** Redis 7
- **Search:** Elasticsearch (future)
- **Messaging:** Redis Pub/Sub → RabbitMQ (future)

### AI/ML
- **LLM APIs:** OpenAI (GPT-4), Anthropic (Claude), Together.ai
- **Embeddings:** OpenAI embeddings
- **Vector DB:** Pinecone (future)
- **Local Models:** Ollama

### DevOps
- **Containerization:** Docker
- **Orchestration:** Docker Compose (dev), Kubernetes (prod)
- **CI/CD:** GitHub Actions
- **Hosting:** Hetzner (EU), DigitalOcean (US)
- **Monitoring:** Datadog
- **Logs:** ELK Stack
- **Error Tracking:** Sentry

### APIs & Integrations
- **Payments:** Stripe
- **Email:** SendGrid
- **SMS:** Twilio
- **Auth:** Auth0 (future)
- **SSO:** Okta, Azure AD, OneLogin (future)
- **File Storage:** S3-compatible, GCS, Azure Blob

---

## Current Status (Sprint 2 Start)

### Completed (Alpha 0.1-0.2)
✅ Email authentication  
✅ Organization & team management  
✅ Conversation persistence  
✅ Project creation via chat  
✅ Document generation via chat  
✅ Task creation (scaffolded)  
✅ LLM provider routing (mock → real)  
✅ SSE streaming (word-by-word)  
✅ Cancel and retry functionality  
✅ Memory extraction heuristics  
✅ 17/17 E2E tests passing  
✅ Docker dev environment (Postgres + Redis)  
✅ Core Services Layer architecture defined  

### In Progress (Sprint 2)
🟡 Real AI provider integration (OpenAI/Anthropic)  
🟡 Memory extraction → recall  
🟡 Perfect streaming reliability  
🟡 Cancel/retry polish  

### Next (Sprint 3-5)
⏳ Workspace completion (Projects, Docs, Tasks UI)  
⏳ Real-time collaboration  
⏳ Marketplace foundation  
⏳ Payment processing  
⏳ Enterprise SAML + RBAC  
⏳ AIOS integration layer  

---

## Execution Model

### Deployment Cadence
- **Daily:** Code merged to main after tests pass
- **Weekly:** Production deployment on Friday
- **Bi-weekly:** Sprint reviews and planning

### Definition of Done (for each capability)
- [ ] Feature implemented and tested
- [ ] Unit + integration tests passing
- [ ] E2E test scenario covered
- [ ] Documentation updated
- [ ] Performance benchmarks met
- [ ] Zero console errors
- [ ] PR reviewed and approved
- [ ] Deployed to staging
- [ ] Ready for production deployment

### Sprint Discipline
- Two-week sprints
- One capability per week
- One service per sprint
- No multitasking
- No technical debt accumulation
- Ship daily
- Measure everything

---

## Success Metrics

### User Metrics
- **DAU:** 100+ by beta release
- **Engagement:** 5+ messages/session average
- **Retention:** 40%+ week-1 retention
- **NPS:** 50+ (positive feedback)

### Technical Metrics
- **Uptime:** 99.5%+
- **API Latency:** p95 < 200ms
- **Stream Latency:** < 100ms first chunk
- **Error Rate:** < 0.1%
- **E2E Test Pass Rate:** 95%+

### Business Metrics
- **Creator Revenue:** $5K+ by sprint 4
- **Enterprise Pilots:** 1-2 customers by sprint 5
- **User Satisfaction:** 4.5+/5.0
- **Monthly Recurring Revenue:** $10K+ by Q4

---

## Resources

### Documents
- [Program Definitions](./00-company-structure.md) — All 10 programs in detail
- [Core Services Layer](./00-core-services-layer.md) — Shared infrastructure
- [Sprint Planning](./00-sprint-planning.md) — 90-day execution plan
- [Executive Vision](./01-executive-vision/README.md) — Product story
- [Ecosystem](./02-ecosystem/README.md) — Tech stack and principles
- [Ask Diana](./05-ask-diana/README.md) — Architecture diagram
- [Technical Architecture](./12-technical-architecture/README.md) — System design
- [Roadmap](./13-roadmap/README.md) — Release milestones

### Code
- [aig-platform](https://github.com/AIG-Global/aig-platform) — Source code (NestJS + Next.js)
- [API Docs](../ai-docs/) — API reference, guides
- [E2E Tests](../aig-platform/apps/api/test/e2e/) — User workflows

---

## Decision: Stop Adding Architecture

### We Are NOT Going To
- Add another service until core is complete
- Design new features that aren't in the 10 programs
- Build for "someday" — only build for Sprint N
- Accept technical debt for speed
- Hire before we prove PMF

### We ARE Going To
- Ship one capability per week
- Build one service per sprint
- Polish ruthlessly
- Test comprehensively
- Measure obsessively
- Listen to users
- Iterate based on feedback
- Move fast with confidence

---

## 90-Day Bet

If we execute this plan perfectly:

**By Week 13 (End of Q2 2026):**
- Diana is the intelligent interface
- Users can work entirely through chat
- Marketplace is operational
- First creators earning revenue
- Enterprise interested in pilots
- 100+ DAU in private beta

**By Q4 2026:**
- v1.0 released to public
- 10,000+ users
- $10K MRR
- North Star prototype working
- Ready to launch 2027 products

**By Q4 2027:**
- North Star developer edition (100 units)
- AIOS developer preview
- Enterprise customers paying
- Marketplace $100K+ revenue

**By Q1 2028:**
- North Star ONE launches
- 50,000+ users
- $100K MRR
- International expansion
- Ready to scale

---

## What's Different Now

1. **Clarity:** 10 clear programs, each with outcomes
2. **Focus:** Stop adding architecture, start shipping
3. **Discipline:** Two-week sprints, ship daily
4. **Foundation:** Core services enable fast product development
5. **Team:** Clear programs mean clear ownership
6. **Speed:** Reduce meetings, maximize coding
7. **Quality:** Comprehensive testing, zero technical debt
8. **Metrics:** Measure everything, optimize relentlessly

---

## Let's Go

This is not theoretical. This is the actual execution plan.

Every line of code should move us closer to one of these 10 programs.

Every sprint should deliver both user value and platform capability.

Every week should ship something real.

**The mission is clear. The plan is detailed. The team is ready.**

Let's build a platform company.

**v1.0 Company in 90 days. Ship it.**
