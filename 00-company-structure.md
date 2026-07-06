# AIGINVEST Company Structure: 10 Programs

**Version:** 1.0  
**Updated:** 2026-07-06  
**Epoch:** We stop adding architecture. We build capabilities.

---

## Executive Summary

AIGINVEST is organized into **10 interdependent programs**. Each program delivers:

1. **One user-facing capability** (what users experience)
2. **One reusable platform service** (what future products build on)

This architecture enables disciplined execution while maintaining clean, extensible infrastructure.

---

## Program 1: Identity

**Status:** 🟢 Complete  
**Timeline:** Delivered in Alpha 0.1

### Purpose
Foundation for all AIGINVEST capabilities. Everything starts here.

### Capabilities
- **Authentication** — Email/SSO, JWT tokens, secure session management
- **Organizations** — Company accounts, billing entities, usage tracking
- **Teams** — Group management, collaboration boundaries, permissions
- **Roles** — Fine-grained RBAC (admin, manager, member, viewer, custom)
- **Billing Identity** — Subscription tiers, payment methods, cost allocation

### Platform Services Delivered
- `IdentityService` — User/org/team CRUD, role assignment
- `AuthService` — JWT issuance, token validation, session management
- `RBAC Engine` — Permission checking for all operations

### Database Models
```
User → Organizations
Organization → Teams
Team → Members (with Role)
```

### Success Criteria
✅ Users can register with email  
✅ Organizations can add members and assign roles  
✅ API enforces role-based access control  
✅ Billing system identifies cost centers  

---

## Program 2: Diana

**Status:** 🟡 Sprint 2-5  
**Timeline:** Foundation in Alpha 0.2, Full capabilities in Beta

### Purpose
Diana is the intelligent interface to AIGINVEST. She becomes the orchestration layer for every service.

### Core Capabilities
- **Memory** — Long-term learning, preference persistence, context retention
- **Reasoning** — Multi-step problem solving, planning, decision making
- **Planning** — Break tasks into steps, estimate effort, adapt to changes
- **Tool Calling** — Execute actions via API, create projects, documents, tasks
- **Automation** — Scheduled actions, triggered workflows, background jobs
- **Voice** — Speech-to-text input, text-to-speech output, audio streaming
- **Vision** — Image analysis, document scanning, design understanding
- **Personalization** — Adapt responses to user preferences, communication style

### Platform Services Delivered
- `ContextEngine` — Build LLM prompts with system guidance + history + memories
- `MemoryExtraction` — Identify and store facts about users, preferences, patterns
- `ToolRouter` — Detect user intent and dispatch to appropriate service
- `StreamingEngine` — SSE delivery of generated content word-by-word
- `PersonalizationEngine` — Load user preferences for tone, domain, audience

### Architecture
```
Diana Interface (Chat)
    ↓
Tool Router (Detect Intent)
    ↓
Context Engine (Build Prompt)
    ↓
AI Router (Select Provider)
    ↓
LLM (OpenAI / Anthropic / Ollama)
    ↓
Streaming Engine (SSE)
    ↓
Diana Interface (Output)
    ↓
Memory Extraction → Memory Store
```

### Success Criteria (Sprint 2)
✅ Real AI provider connected (OpenAI/Anthropic)  
✅ SSE streaming working (word-by-word delivery)  
✅ Memory extraction and storage operational  
✅ Cancel and retry working  
✅ 17/17 E2E tests pass  

### Success Criteria (Sprint 3-5)
✅ Tool calling working for all Workspace features  
✅ Multi-turn conversations with memory  
✅ Voice input/output integrated  
✅ Diana suggests next actions based on context  
✅ Automation workflows configured via chat  

---

## Program 3: Workspace

**Status:** 🔵 Sprint 3  
**Timeline:** Foundation in Alpha 0.2, Full capabilities in Beta

### Purpose
Replace traditional app-switching with Diana-driven productivity.

Users don't open apps. They ask Diana.

### Capabilities
- **Documents** — AI-assisted writing, markdown support, rich formatting, collaboration
- **Projects** — Organize work, track status, manage timelines, team assignment
- **Tasks** — Individual actions, priorities, dependencies, reminders
- **Calendar** — Schedule management, meeting integration, Diana availability
- **Notes** — Quick capture, linking to projects/tasks, full-text search
- **Whiteboards** — Visual collaboration, diagram creation, flow design
- **AI Writer** — Assist writing, suggest edits, style guidance, tone adjustment

### Platform Services Delivered
- `DocumentService` — CRUD for markdown docs, revision tracking, sharing
- `ProjectService` — Project lifecycle, team management, progress tracking
- `TaskService` — Task management, dependency resolution, status workflows
- `CalendarService` — Event management, availability, meeting automation
- `SearchService` — Full-text search across all workspace content
- `CollaborationService` — Real-time sync, conflict resolution, sharing

### Database Models
```
Project
  ├── Document[]
  ├── Task[]
  └── Members[]

Document
  ├── Revisions[]
  └── SharedWith[]

Task
  ├── Subtasks[]
  ├── Dependencies[]
  └── Assignees[]
```

### Diana Integration
Users say:
- "Create a project for Q3 planning" → ProjectService
- "Write a product brief for Diana" → DocumentService + AI Writer
- "Add a task to review the brief" → TaskService
- "What's due this week?" → CalendarService + TaskService
- "Show me open tasks" → TaskService with full-text search

### Success Criteria (Sprint 3)
✅ Projects CRUD working via chat and API  
✅ Documents CRUD working via chat and API  
✅ Tasks CRUD working via chat and API  
✅ Frontend shows all workspace items  
✅ Diana can create all three types  

---

## Program 4: Marketplace

**Status:** 🔵 Sprint 4  
**Timeline:** Foundation in Alpha 0.3, Launch in 2027

### Purpose
Enable external developers and creators to extend AIGINVEST.

Developers earn revenue. Users get specialized capabilities.

### Capabilities
- **Skills** — Small, focused abilities Diana can invoke
- **Agents** — Autonomous mini-apps that orchestrate multiple services
- **Templates** — Pre-built projects, documents, workflows
- **Integrations** — Webhooks, API bridges to external services
- **Plugins** — Browser extensions, desktop integrations

### Platform Services Delivered
- `MarketplaceRegistry` — Publish, discover, version skills/agents/templates
- `BillingEngine` — Track usage, split revenue, handle payouts
- `ManifestValidator` — Enforce security, permission, capability constraints
- `RuntimeSandbox` — Execute untrusted code safely
- `NotificationService` — Alert users about new extensions, usage alerts

### Revenue Model
- AIGINVEST takes 30% of skill/agent sales
- Creators set their own pricing
- Template library free (users still subscribe to premium templates)
- Integration bandwidth billed at cost

### Example Skills
- "Summarize this PDF" → Reads PDF, generates summary
- "Create a competitor analysis" → Researches competitors, generates report
- "Schedule a meeting" → Integrates with calendar, finds times, books

### Diana Integration
- "Show me available skills for document analysis" → Browse marketplace
- "Install the PDF analyzer" → Download and activate
- "Analyze this marketing plan" → Diana invokes the skill
- "What skills did I use this month?" → Usage analytics

### Success Criteria (Sprint 4)
✅ Marketplace structure defined and built  
✅ Skill upload/approval workflow working  
✅ First revenue split calculated and tracked  
✅ Creators can list, update, and earn from skills  

---

## Program 5: Business Cloud

**Status:** ⏳ 2027  
**Timeline:** Launch in 2027, focus after Marketplace

### Purpose
Enable business customers to run core operations on AIGINVEST.

From productivity to operational platform.

### Modules
- **CRM** — Customer management, pipeline, forecasting
- **ERP** — Inventory, fulfillment, supply chain
- **HR** — Hiring, onboarding, performance, payroll
- **Finance** — Accounting, reporting, tax compliance
- **Analytics** — Business metrics, dashboards, alerting
- **Knowledge** — Internal wiki, documentation, SOPs

### Powered By Diana
- "Generate monthly sales report" → Analytics + CRM
- "Onboard new team member" → HR workflows
- "Process purchase order" → ERP
- "Forecast Q3 revenue" → Finance + Analytics
- "Document our SEO process" → Knowledge + AI Writer

### Success Criteria
✅ CRM module launched with basic pipeline management  
✅ First business customer live and profitable  
✅ Diana can execute core workflows  
✅ Analytics dashboard showing business metrics  

---

## Program 6: AIOS

**Status:** ⏳ 2027  
**Timeline:** Developer preview in 2027, launch in 2028

### Purpose
Operating system for AI-first devices. Local-first, privacy-preserving.

### Core Capabilities
- **Local AI** — Run models on-device, offline capability
- **Offline Mode** — Full functionality without internet connection
- **Device Sync** — Seamless data sync when online
- **Secure Enclave** — Hardware-backed encryption, private computation
- **Edge Inference** — Deploy models to edge, update silently
- **IoT Hub** — Manage connected devices, firmware updates

### Architecture
```
AIOS (Linux-based)
├── Diana Runtime (local LLM)
├── Secure Enclave (encryption keys, biometrics)
├── Device Drivers (hardware integration)
├── Network Stack (sync, updates)
└── App Runtime (manifest-based execution)
```

### Platform Services
- `DeviceRegistry` — Identify and manage AIOS devices
- `ModelDeployment` — Push LLMs to devices, version management
- `PrivacyEngine` — Ensure data never leaves device without consent
- `SyncService` — Reconcile device data with cloud

### Success Criteria
✅ AIOS running on development hardware  
✅ Local Diana model operational  
✅ Device sync reliable for files, settings, memories  
✅ Secure enclave protecting user data  

---

## Program 7: North Star ONE

**Status:** ⏳ 2028  
**Timeline:** Prototype in 2026, developer edition in 2027, launch in 2028

### Purpose
The flagship device. Diana everywhere, in your hand.

### Features
- **Hardware** — Custom AI accelerator, 7" display, premium materials, 7-day battery
- **AIOS** — Full operating system, local Diana, offline-first
- **Diana Integration** — Voice, vision, contextual awareness
- **Beam Me Up** — Seamless sync to cloud, other devices, team members
- **Secure Identity** — Biometric + cryptographic identity
- **Industrial Design** — Premium unboxing, luxury positioning

### Use Cases
- "What's on my calendar today?" → Local computation
- "Summarize my emails" → Diana + cloud sync
- "Take a photo of this receipt" → OCR + smart capture
- "Schedule a meeting with Sarah" → Integrates with cloud calendar
- "Show me documents from the project" → Searches cloud, displays locally

### Go-to-Market
- Developer edition: 2027 (limited, $2,000)
- Consumer launch: 2028 ($1,500 base)
- Enterprise version: 2028 ($3,000 with BYOK)

### Success Criteria
✅ Prototype device built and working  
✅ 100 developer edition units in hands of beta testers  
✅ North Star review scores 4.5+ on tech media  
✅ Pre-orders exceed 10,000 units  

---

## Program 8: Developer Platform

**Status:** 🟡 Sprint 2-5 (Foundation)  
**Timeline:** Foundation in 2026, full launch in 2027

### Purpose
Enable external developers to build for AIGINVEST ecosystem.

### Deliverables
- **SDK** — Python, TypeScript, Go SDKs for building services
- **APIs** — Comprehensive REST + GraphQL API surface
- **Webhooks** — Event-driven integrations
- **CLI** — Command-line tool for deployment, testing, debugging
- **Documentation** — Tutorials, references, best practices, examples
- **Testing Sandbox** — Isolated environment for development

### Example API Endpoints
```
POST /api/v1/skills          → Create a skill
GET  /api/v1/skills/:id      → Get skill details
POST /api/v1/agents          → Deploy an agent
GET  /api/v1/events          → Subscribe to events
POST /api/v1/documents       → Create document from external system
GET  /api/v1/tasks?project   → Query tasks in project
```

### Webhook Events
```
user.created
user.updated
project.created
project.updated
task.created
task.completed
document.shared
skill.installed
workspace.invited
```

### SDK Example (Python)
```python
from aiginvest import Diana, Project, Task

diana = Diana(api_key="...")
project = diana.projects.get("Q3-Planning")
tasks = project.tasks.list(status="open")

for task in tasks:
    task.status = "in_progress"
    task.save()

# Diana will notice the change and notify assignees
```

### Success Criteria
✅ Full API documented with examples  
✅ TypeScript SDK published on npm  
✅ Python SDK published on PyPI  
✅ 50+ external developers active  
✅ 10+ marketplace extensions published  

---

## Program 9: Enterprise

**Status:** 🔵 Sprint 5 (Foundation)  
**Timeline:** Foundation in 2026, full launch in 2027

### Purpose
Enable large organizations to adopt AIGINVEST with security, compliance, auditability.

### Capabilities
- **SAML SSO** — Directory-based authentication (Okta, Azure AD, OneLogin)
- **RBAC** — Role-based access control, custom roles, permission inheritance
- **Audit Logs** — Complete action history, compliance reporting
- **DLP** — Data loss prevention, content policies, export restrictions
- **BYOK** — Bring-your-own-key for encryption at rest
- **Compliance** — SOC 2 attestation, GDPR compliance, data residency
- **Admin Console** — Manage users, teams, policies, usage
- **Domain Routing** — Route to customer's private cloud or data center

### Architecture
```
Enterprise AIGINVEST
├── SAML IdP Integration
├── Permission Engine (Advanced RBAC)
├── Audit Log Storage (Immutable)
├── Encryption Service (BYOK support)
├── DLP Engine (Content scanning)
├── Admin UI (Full management)
└── Compliance Reporter
```

### Admin Console Features
- User management (invite, remove, suspend)
- Team creation and management
- Policy enforcement (password rules, IP restrictions, session limits)
- Usage analytics (user activity, storage, API calls)
- Audit log export (compliance, forensics)
- SSO configuration
- Custom branding

### Success Criteria
✅ SAML SSO working with major IdPs  
✅ Advanced RBAC implemented and tested  
✅ Audit logs immutable and exportable  
✅ SOC 2 audit scheduled  
✅ First enterprise customer signed  

---

## Program 10: Intelligence Platform

**Status:** 🟢 Complete  
**Timeline:** Multi-provider routing in Alpha 0.2, custom models in 2027

### Purpose
Orchestrate multiple AI models, optimizing for cost, speed, privacy, quality, availability.

No vendor lock-in. Best-of-breed LLMs.

### Providers
- **OpenAI** — GPT-4, best-in-class reasoning
- **Anthropic** — Claude, strong reasoning and safety
- **Ollama** — Local models, on-premise, privacy-first
- **Together AI** — Open-source models, low cost
- **Hugging Face** — Custom fine-tuned models
- **Custom** — In-house trained models

### Routing Criteria
```
Request → Route Decision Tree
├── User preference? (Diana customization)
├── Privacy level? (Use Ollama for private data)
├── Speed requirement? (Use fast model)
├── Cost budget? (Use cheapest that meets quality)
├── Model availability? (Fallback to backup)
└── Task type? (Vision → suitable provider)
```

### Configuration
```yaml
DefaultProvider: openai
PrivateDataProvider: ollama
HighSpeedProvider: together
LowCostProvider: huggingface

Fallback: [openai, anthropic, ollama]
```

### Platform Services
- `LLMService` — Unified interface to all providers
- `ProviderHealthMonitor` — Track availability, latency, error rates
- `TokenCounter` — Calculate costs before routing
- `RateLimiter` — Fair usage across providers
- `ProviderRouter` — Decision engine for model selection

### Success Criteria ✅
✅ OpenAI integration working  
✅ Anthropic integration working  
✅ Ollama local fallback working  
✅ Cost tracking implemented  
✅ Fallback routing working  
✅ User can specify preferred provider  

---

## Interdependencies

```
Identity (Foundation)
    ↓
AI Router, Memory, Auth
    ↓
Diana (Orchestration)
    ↓
┌───────────────────────────────────────┐
├ Workspace    ├ Marketplace  ├ Business Cloud
├ Enterprise   ├ Dev Platform └─ More Programs
└───────────────────────────────────────┘
    ↓
AIOS (Device OS)
    ↓
North Star ONE (Flagship)
```

---

## KPIs by Program

### Identity
- User registration rate
- Org activation rate
- Team member invitations
- Permission assignment accuracy

### Diana
- Message latency (< 100ms first chunk)
- Tool invocation success rate
- Memory recall accuracy
- User satisfaction with responses

### Workspace
- Document creation rate
- Project completion rate
- Task completion rate
- Collaboration metrics (comments, shares)

### Marketplace
- Skill downloads
- Revenue per creator
- Average rating
- Developer satisfaction

### Business Cloud
- Business customer adoption
- Module utilization
- Customer CSAT
- Revenue per customer

### AIOS
- Device uptake
- Battery efficiency
- Sync reliability
- Privacy violation reports (should be 0)

### North Star ONE
- Device sales
- Customer satisfaction
- Market share vs. competitors
- Brand perception

### Developer Platform
- External developer count
- API usage volume
- SDK GitHub stars
- Skill marketplace revenue

### Enterprise
- Enterprise customer count
- Compliance audit pass rate
- Admin dashboard usage
- Compliance incident reports (should be 0)

### Intelligence Platform
- Model routing accuracy
- Cost per request
- Provider failure rate
- User satisfaction with model choice

---

## Next Steps

**This Week (Sprint 2):**
- Real AI provider connected
- Memory extraction working
- Full streaming pipeline tested

**Next Week (Sprint 3):**
- Workspace foundation complete (Projects, Docs, Tasks)
- Diana can create all three
- Full E2E tests passing

**Week After (Sprint 4):**
- Marketplace foundation built
- Payments integrated
- First skills uploaded

**Then (Sprint 5):**
- Enterprise auth foundation
- Advanced RBAC
- AIOS integration layer started

---

## Conclusion

These 10 programs represent a **platform company**, not an app.

Each week produces:
1. **One user capability** (visible value)
2. **One platform service** (future foundation)

This balance ensures both delivery and architectural quality.

The mission: **Deliver the first publicly demonstrable AIGINVEST where Diana is the intelligent interface to work, knowledge, and productivity—and where every architectural decision prepares the path for AIOS and the North Star ONE device.**

**90 days. Disciplined execution. Relentless focus on user experience.**

That's the game.
