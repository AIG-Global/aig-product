# AIG Ecosystem Map & Architecture

**Visual representation of how all components interconnect**

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                          AIG PLATFORM                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │           CUSTOMER EXPERIENCE (Layer 1)                 │   │
│  │                                                          │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │   │
│  │  │  Diana Chat  │  │ Diana        │  │ Applications │  │   │
│  │  │  Interface   │  │ Workspace    │  │              │  │   │
│  │  │  (Voice/Text)│  │ (Split-view) │  │ Projects     │  │   │
│  │  │              │  │              │  │ Documents    │  │   │
│  │  └──────────────┘  └──────────────┘  │ Tasks        │  │   │
│  │                                       │ Calendar     │  │   │
│  │                                       │ Notes        │  │   │
│  │                                       └──────────────┘  │   │
│  └─────────────────────────────────────────────────────────┘   │
│                              ↓                                   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │       PLATFORM SERVICES (Layer 2)                        │   │
│  │       [Shared Infrastructure - No Duplication]           │   │
│  │                                                          │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │   │
│  │  │  Identity   │  │ AI Router   │  │  Memory     │    │   │
│  │  │ Service     │  │ Service     │  │  Service    │    │   │
│  │  │             │  │             │  │             │    │   │
│  │  │ • Auth      │  │ • OpenAI    │  │ • Learn     │    │   │
│  │  │ • Users     │  │ • Anthropic │  │ • Recall    │    │   │
│  │  │ • Orgs      │  │ • Ollama    │  │ • Extract   │    │   │
│  │  │ • Roles     │  │ • Cost opt  │  │ • Store     │    │   │
│  │  │ • Teams     │  │ • Failover  │  │             │    │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘    │   │
│  │                                                          │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │   │
│  │  │  Search     │  │  Storage    │  │ Notification │   │   │
│  │  │  Service    │  │  Service    │  │  Service     │   │   │
│  │  │             │  │             │  │             │    │   │
│  │  │ • Full-text │  │ • Files     │  │ • Email     │    │   │
│  │  │ • Semantic  │  │ • Version   │  │ • Push      │    │   │
│  │  │ • Index     │  │ • Sharing   │  │ • SMS       │    │   │
│  │  │ • Rank      │  │ • Encryption│  │ • In-app    │    │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘    │   │
│  │                                                          │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │   │
│  │  │  Payment    │  │  Analytics  │  │  Audit      │    │   │
│  │  │  Service    │  │  Service    │  │  Service    │    │   │
│  │  │             │  │             │  │             │    │   │
│  │  │ • Stripe    │  │ • Usage     │  │ • Logs      │    │   │
│  │  │ • Billing   │  │ • Metrics   │  │ • Compliance│    │   │
│  │  │ • Revenue   │  │ • Cohorts   │  │ • Forensics │    │   │
│  │  │ • Payouts   │  │ • Alerts    │  │             │    │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘    │   │
│  │                                                          │   │
│  │  ┌─────────────┐  ┌──────────────────────────────┐    │   │
│  │  │  Security   │  │      Event Service            │    │   │
│  │  │  Service    │  │    (Pub/Sub + Real-time)     │    │   │
│  │  │             │  │                              │    │   │
│  │  │ • Encrypt   │  │ • Publish events             │    │   │
│  │  │ • Keys      │  │ • Subscribe to changes       │    │   │
│  │  │ • TLS       │  │ • Real-time sync             │    │   │
│  │  │ • Threats   │  │ • Trigger workflows          │    │   │
│  │  └─────────────┘  └──────────────────────────────┘    │   │
│  │                                                          │   │
│  └─────────────────────────────────────────────────────────┘   │
│                              ↓                                   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │         APPLICATIONS (Layer 3)                           │   │
│  │     [Built on Platform Services]                        │   │
│  │                                                          │   │
│  │  WORKSPACE              BUSINESS CLOUD                 │   │
│  │  ├── Projects           ├── CRM                        │   │
│  │  ├── Documents          ├── ERP                        │   │
│  │  ├── Tasks              ├── HR                         │   │
│  │  ├── Calendar           ├── Finance                    │   │
│  │  ├── Notes              └── Analytics                  │   │
│  │  └── Whiteboards                                        │   │
│  │                                                          │   │
│  │  LEARNING               MARKETPLACE                    │   │
│  │  ├── Academy            ├── Skills                     │   │
│  │  ├── Courses            ├── Plugins                    │   │
│  │  └── Certifications     ├── Templates                  │   │
│  │                         └── Integrations               │   │
│  │                                                          │   │
│  └─────────────────────────────────────────────────────────┘   │
│                              ↓                                   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │    AIOS & NORTH STAR ONE (Layer 4)                      │   │
│  │                                                          │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │   │
│  │  │  AIOS        │  │ North Star   │  │ Beam Me Up   │  │   │
│  │  │              │  │ ONE Device   │  │ (Sync)       │  │   │
│  │  │ • Local AI   │  │              │  │              │  │   │
│  │  │ • Offline    │  │ • Hardware   │  │ • Cross-device
│  │  │ • Privacy    │  │ • AIOS       │  │ • Conflict   │  │   │
│  │  │ • Drivers    │  │ • Diana      │  │ • Offline    │  │   │
│  │  │              │  │ • Premium    │  │   support    │  │   │
│  │  └──────────────┘  └──────────────┘  └──────────────┘  │   │
│  │                                                          │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                   │
├─────────────────────────────────────────────────────────────────┤
│                     DATA LAYER (PostgreSQL + Redis)              │
│         [Primary Storage + Cache + Real-time State]             │
├─────────────────────────────────────────────────────────────────┤
│               EXTERNAL SERVICES & INTEGRATIONS                  │
│  OpenAI | Anthropic | Ollama | SendGrid | Stripe | Cloudflare  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Diana's Request Flow

```
User Input (Text/Voice/Vision)
        ↓
┌───────────────────────────────────────────────────┐
│           DIANA ORCHESTRATION ENGINE              │
│  [Located in Chat Service + Context Engine]       │
└───────────────────────────────────────────────────┘
        ↓
┌─ Intent Recognition ─┐
│ "Create project"     │
│ "Summarize email"    │
│ "Update task status" │
└─ Tool Router ────────┘
        ↓
    ┌───────┴────────────────────────────────┐
    ↓                                         ↓
[Tool Calling]                      [Retrieval Only]
│                                   │
├─ Extract parameters               ├─ Search Service
├─ Call Projects/Docs/Tasks API     ├─ Memory Service
├─ Create/update resource           ├─ Load conversation
└─ Emit event                       └─ Retrieve context
    ↓                                   ↓
[Publish to Event Bus]
    ↓
[Context Engine builds prompt]
├─ System prompt (Diana's personality)
├─ Conversation history
├─ Relevant memories (from Memory Service)
├─ Available tools
├─ Current context
└─ User preferences

[AI Router selects model]
├─ OpenAI (default, best quality)
├─ Anthropic (if reasoning preferred)
├─ Ollama (if privacy required)
└─ Cost optimization

[LLM generates response]
├─ Stream word-by-word
├─ Decide if tool calling needed
└─ Handle errors

[Memory Extraction]
├─ Extract facts about user
├─ Save to Memory Service
└─ Update confidence scores

[Emit events]
├─ Item created (if tool called)
├─ Task updated (if status changed)
└─ Notify subscribers

[Output]
├─ Stream to browser (SSE)
├─ Display in chat
├─ Execute actions
└─ Update sidebar
```

---

## Real-Time Sync Flow

```
Any User Action
    ↓
Service updates data
    ↓
Service publishes event to Event Bus
    ├─ project.created
    ├─ task.updated
    ├─ document.shared
    ├─ member.added
    └─ etc.
    ↓
Event Bus (Redis Pub/Sub + WebSocket)
    ├─ Subscribers notified in real-time
    ├─ Update payload sent
    └─ "Other user modified this"
    ↓
Subscriber (Other user's browser)
    ├─ Receive event via WebSocket
    ├─ Update local state
    └─ Refresh UI
    ↓
User sees instant update (no refresh needed)
```

---

## Data Flow Example: Creating a Project

```
User: "Create a project called Q3 Planning"
    ↓
Diana's Intent Detection → create_project
    ↓
Parameter Extraction → { name: "Q3 Planning" }
    ↓
Call Projects API
    ├─ POST /api/v1/projects
    ├─ { name: "Q3 Planning", userId, organizationId }
    └─ Return { projectId }
    ↓
Projects Service
    ├─ Validate input (Identity Service)
    ├─ Store to PostgreSQL
    ├─ Cache in Redis
    ├─ Publish event: project.created
    └─ Return response
    ↓
Event Bus receives event
    ├─ All subscribed clients notified
    ├─ Other users' sidebars update
    └─ Analytics logged
    ↓
Diana responds
    ├─ "✓ Created Q3 Planning project"
    ├─ "Would you like to add team members?"
    └─ Stream to user's browser
    ↓
Diana extracts memory
    ├─ "User is planning Q3"
    ├─ Save to Memory Service
    └─ Confidence: 0.95
    ↓
User sees:
    ├─ Chat message: "✓ Created Q3 Planning"
    ├─ Sidebar updates with new project
    └─ Sidebar shows "3 projects" (updated count)
```

---

## Service Dependencies

```
Diana Service
    ├─ Context Engine
    │   ├─ Memory Service
    │   ├─ Search Service
    │   └─ Conversation History
    ├─ Tool Router
    │   ├─ Projects Service
    │   ├─ Documents Service
    │   ├─ Tasks Service
    │   └─ Custom Skills
    ├─ AI Router
    │   ├─ OpenAI API
    │   ├─ Anthropic API
    │   ├─ Ollama Local
    │   └─ Cost Optimizer
    ├─ Memory Extraction
    │   └─ Memory Service
    └─ Event Publishing
        └─ Event Service

Projects Service
    ├─ Identity Service (auth)
    ├─ Storage Service (files)
    ├─ Search Service (indexing)
    ├─ Event Service (publishing)
    ├─ Analytics Service (tracking)
    ├─ Audit Service (logging)
    └─ PostgreSQL (storage)

Workspace (UI)
    ├─ All Services via APIs
    ├─ Event Service (WebSocket)
    └─ Storage Service (downloads)

Memory Service
    ├─ Storage (PostgreSQL)
    ├─ Search Service (indexing)
    ├─ Security Service (encryption)
    └─ Event Service (memory changed)

Search Service
    ├─ Elasticsearch (indexing)
    ├─ Pinecone (embeddings)
    ├─ Storage Service (source)
    └─ Analytics Service (queries)

Payments Service
    ├─ Stripe API
    ├─ Identity Service (billing identity)
    ├─ Analytics Service (usage tracking)
    ├─ Notification Service (receipts)
    └─ Audit Service (transactions)

Marketplace Service
    ├─ Payments Service (revenue split)
    ├─ Storage Service (skill packages)
    ├─ Identity Service (creator auth)
    ├─ Event Service (skill installed)
    └─ Analytics Service (adoption)
```

---

## Deployment Architecture

```
┌────────────────────────────────────────────────────────┐
│                    INTERNET                            │
└────────────────────────────────────────────────────────┘
        ↓
┌────────────────────────────────────────────────────────┐
│            CLOUDFLARE (DDoS + CDN + WAF)               │
└────────────────────────────────────────────────────────┘
        ↓
┌────────────────────────────────────────────────────────┐
│          LOAD BALANCER (DigitalOcean / Hetzner)        │
└────────────────────────────────────────────────────────┘
        ↓
┌────────────────────────────────────────────────────────┐
│            KUBERNETES CLUSTER (Multi-AZ)               │
│                                                         │
│  ┌──────────────────────────────────────────────────┐ │
│  │  INGRESS (Kong API Gateway)                      │ │
│  └──────────────────────────────────────────────────┘ │
│           ↓           ↓           ↓           ↓        │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐      │
│  │   Chat Pod  │ │ Identity    │ │  Projects   │      │
│  │  (5 replicas)│ │  Pod (3)    │ │  Pod (3)    │      │
│  └─────────────┘ └─────────────┘ └─────────────┘      │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐      │
│  │  Search Pod │ │  Payment    │ │  Analytics  │      │
│  │  (3 replicas)│ │  Pod (1)    │ │  Pod (2)    │      │
│  └─────────────┘ └─────────────┘ └─────────────┘      │
│  ┌──────────────────────────────────────────────────┐ │
│  │ Next.js Frontend Pods (3 replicas)               │ │
│  └──────────────────────────────────────────────────┘ │
│                                                         │
└────────────────────────────────────────────────────────┘
        ↓
┌────────────────────────────────────────────────────────┐
│           PERSISTENT STORAGE (Volumes)                 │
│                                                         │
│  PostgreSQL (Primary + 2 Replicas)                    │
│  Redis (Cluster mode)                                 │
│  Elasticsearch (3 nodes)                              │
│  MinIO S3 (Object storage)                            │
│                                                         │
└────────────────────────────────────────────────────────┘
        ↓
┌────────────────────────────────────────────────────────┐
│        EXTERNAL SERVICES & MONITORING                  │
│                                                         │
│  OpenAI API | Anthropic API | SendGrid | Stripe       │
│  Datadog | Sentry | CloudWatch                        │
│                                                         │
└────────────────────────────────────────────────────────┘
```

---

## Development Workflow (Local)

```
Developer Machine

┌─────────────────────────────────────┐
│     Docker Compose (docker-compose.yml)
│                                     │
│  ┌──────────────────────────────┐  │
│  │ PostgreSQL Container         │  │
│  │ :5432                        │  │
│  └──────────────────────────────┘  │
│                                     │
│  ┌──────────────────────────────┐  │
│  │ Redis Container              │  │
│  │ :6379                        │  │
│  └──────────────────────────────┘  │
│                                     │
│  ┌──────────────────────────────┐  │
│  │ Elasticsearch Container      │  │
│  │ :9200                        │  │
│  └──────────────────────────────┘  │
│                                     │
└─────────────────────────────────────┘
        ↓
┌─────────────────────────────────────┐
│     Terminal 1: NestJS API          │
│     npm run dev                     │
│     :3333                           │
└─────────────────────────────────────┘
        ↓
┌─────────────────────────────────────┐
│     Terminal 2: Next.js Frontend    │
│     npm run dev                     │
│     :3001                           │
└─────────────────────────────────────┘
        ↓
┌─────────────────────────────────────┐
│     Terminal 3: Test Suite          │
│     npm run test:watch              │
└─────────────────────────────────────┘
        ↓
┌─────────────────────────────────────┐
│     Browser: localhost:3001         │
│     Hot reload on code changes      │
└─────────────────────────────────────┘
```

---

## Feature Delivery Pipeline

```
Feature Idea
    ↓
Add to Backlog (in this Master Design)
    ↓
Design in Master Design (Purpose, Requirements, API, Security)
    ↓
Create Issue in GitHub
    ↓
Developer picks up
    ↓
Branch: feature/feature-name
    ↓
Implement code exactly per spec
    ↓
Write tests (unit + integration + E2E)
    ↓
Local testing (docker-compose environment)
    ↓
Push to GitHub
    ↓
GitHub Actions CI
    ├─ Lint
    ├─ Test
    ├─ Build
    └─ Security scan
    ↓
Pass? Deploy to Staging
    ↓
Manual QA testing
    ↓
Code review
    ↓
Approved? Merge to main
    ↓
Auto-deploy to Production (Friday deployments)
    ↓
Monitor metrics
    ├─ Error rate
    ├─ API latency
    ├─ User adoption
    └─ Alerting
    ↓
Feature live ✓
```

---

This ecosystem map is the visual companion to the AIG Master Product Design.

It shows how every component connects and flows together.

Use this when explaining the architecture to new team members, partners, or investors.
