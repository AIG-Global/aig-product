# Core Services Layer

**Version:** 1.0  
**Purpose:** Shared infrastructure for all 10 programs  
**Principle:** Every program builds on these services, not implementing duplicates  

---

## Architecture

```
Application Layer (10 Programs)
    ↓
Core Services Layer (Shared Infrastructure)
    ├── Identity Service
    ├── AI Router
    ├── Memory Service
    ├── Notification Service
    ├── Payment Service
    ├── Storage Service
    ├── Search Service
    ├── Analytics Service
    ├── Event Service
    ├── Audit Service
    └── Security Service
    ↓
Data Layer (PostgreSQL + Redis)
    ↓
External Services (LLMs, Payments, Mail)
```

Every program uses these services instead of building its own.

---

## 1. Identity Service

**Purpose:** Unified authentication, authorization, and user management

### API
```
POST   /identity/users/register
POST   /identity/users/login
GET    /identity/users/:id
PUT    /identity/users/:id
POST   /identity/orgs
GET    /identity/orgs/:id
PUT    /identity/orgs/:id
POST   /identity/teams
GET    /identity/teams/:id
PUT    /identity/members/:id/role

// Token management
POST   /identity/tokens/refresh
POST   /identity/tokens/revoke
GET    /identity/tokens/validate
```

### Responsibilities
- User registration and login
- JWT token issuance and validation
- Organization creation and management
- Team membership and role assignment
- Session management
- Password reset and MFA
- OAuth provider integration (Google, GitHub, Microsoft)

### Used By
- **All Programs** (foundation for every operation)

### Data Models
```
User {
  id: uuid
  email: string (unique)
  passwordHash: string
  name: string
  avatar?: string
  preferences: json
  mfaEnabled: boolean
  lastLogin: timestamp
}

Organization {
  id: uuid
  name: string
  slug: string (unique)
  logo?: string
  billingPlan: "free" | "pro" | "enterprise"
  owner: UserId
}

Team {
  id: uuid
  organizationId: uuid
  name: string
  description?: string
}

Member {
  id: uuid
  teamId: uuid
  userId: uuid
  role: "admin" | "manager" | "member" | "viewer" | custom
}

Role {
  id: uuid
  organizationId: uuid
  name: string (custom or built-in)
  permissions: string[] // "create:document", "read:task", etc.
}
```

---

## 2. AI Router

**Purpose:** Unified interface to multiple LLM providers

### API
```
POST /ai/chat/stream
  {
    messages: Message[],
    provider?: "openai" | "anthropic" | "ollama" | "auto",
    model?: "gpt-4" | "claude-3" | "llama-2",
    temperature?: 0.7,
    maxTokens?: 2000,
    onChunk?: (chunk: string) => void
  }

POST /ai/embed
  { text: string, dimension?: 1536 }

POST /ai/moderate
  { text: string }
```

### Responsibilities
- Route requests to appropriate provider based on criteria
- Handle provider failures with automatic fallback
- Track provider health and latency
- Cost tracking and optimization
- Token counting before request
- Rate limiting per provider
- Response formatting and standardization

### Used By
- **Diana** (every response)
- **Marketplace** (for skill generation)
- **Business Cloud** (report generation, suggestions)
- **Enterprise** (content moderation, policy checks)

### Configuration
```yaml
providers:
  openai:
    apiKey: ${OPENAI_API_KEY}
    models: [gpt-4, gpt-3.5-turbo]
    costPer1kTokens: 0.03
    
  anthropic:
    apiKey: ${ANTHROPIC_API_KEY}
    models: [claude-3-opus, claude-3-sonnet]
    costPer1kTokens: 0.015
    
  ollama:
    endpoint: http://ollama:11434
    models: [llama2, mistral]
    costPer1kTokens: 0  # Local

routing:
  default: auto
  privateDataOnly: ollama
  highSpeed: openai
  lowCost: ollama
  fallback: [openai, anthropic, ollama]
```

---

## 3. Memory Service

**Purpose:** Long-term learning and context retention

### API
```
POST   /memory/facts
GET    /memory/facts/:userId
GET    /memory/facts/:userId?type=preference
PUT    /memory/facts/:id
DELETE /memory/facts/:id

// Recall memories relevant to current context
POST   /memory/recall
  { userId: uuid, context: string, limit?: 10 }

// Extract new memories from conversation
POST   /memory/extract
  { userId: uuid, text: string }
```

### Responsibilities
- Store user facts, preferences, and learning
- Extract memories from conversations
- Recall relevant memories for context
- Update memories based on user feedback
- Privacy-aware memory retention
- Memory versioning and audit trail

### Used By
- **Diana** (every response includes recalled memories)
- **Context Engine** (builds prompts with memories)
- **Personalization Engine** (user preferences)

### Memory Types
```
Preferences: Communication style, tone, language
Facts: Name, company, role, interests
Patterns: Meeting times, common projects, usual collaborators
History: Past decisions, project outcomes, learnings
Relationships: Key contacts, collaboration history
Domain Knowledge: Technical skills, expertise areas
```

### Data Model
```
Memory {
  id: uuid
  userId: uuid
  type: "preference" | "fact" | "pattern" | "relationship" | "knowledge"
  content: string
  confidence: 0.0-1.0 (how certain we are)
  source: "extracted" | "explicit" | "learned"
  createdAt: timestamp
  updatedAt: timestamp
  referenceCount: int
}
```

---

## 4. Notification Service

**Purpose:** Deliver alerts, reminders, and messages to users

### API
```
POST   /notifications/send
  {
    userId: uuid,
    type: "alert" | "reminder" | "mention" | "update",
    title: string,
    body: string,
    action?: { url: string, label: string },
    channels: ["email", "push", "sms", "in-app"]
  }

GET    /notifications/user/:userId
POST   /notifications/:id/read
POST   /notifications/:id/dismiss

// Batch notifications
POST   /notifications/batch
  { userIds: uuid[], ... }

// Schedule notifications
POST   /notifications/schedule
  { userId: uuid, delayMinutes: 60, ... }
```

### Responsibilities
- Queue notifications across multiple channels
- Batch notifications to avoid notification fatigue
- Smart scheduling (respect user quiet hours)
- Delivery tracking and retry logic
- Preference-based filtering
- Channel selection per user

### Used By
- **Workspace** (task reminders, project updates)
- **Diana** (action completed alerts)
- **Marketplace** (new skill alerts)
- **Business Cloud** (sales alerts, inventory warnings)

### Channels
- **Email** — Longer-form, trackable
- **Push** — Mobile/desktop notifications
- **SMS** — Time-critical alerts
- **In-App** — Notification center, badges
- **Slack** — Team notifications
- **Webhook** — External system integration

### Configuration
```yaml
preferences:
  quietHours: "20:00-08:00"
  maxEmailsPerDay: 10
  batchDigest: true
  channels:
    email: enabled
    push: enabled
    sms: disabled
```

---

## 5. Payment Service

**Purpose:** Billing, invoicing, and revenue distribution

### API
```
POST   /payments/charge
  { customerId: uuid, amount: number, currency: "USD", description: string }

POST   /payments/subscribe
  { customerId: uuid, planId: string }

GET    /payments/invoices/:customerId
POST   /payments/payout
  { creatorId: uuid, amount: number, method: "stripe" | "paypal" }

GET    /payments/usage/:organizationId
```

### Responsibilities
- Process payments via Stripe
- Manage subscriptions (pro, enterprise, custom)
- Generate invoices
- Track usage-based billing
- Distribute revenue to creators (Marketplace)
- Invoice delivery and collection
- Tax compliance and reporting

### Used By
- **Identity** (billing identity)
- **Marketplace** (creator payouts, revenue tracking)
- **Business Cloud** (subscription billing)
- **Enterprise** (per-seat or usage-based)

### Pricing Models
```
// Per-seat
pro_plan: $29/user/month

// Usage-based (Diana)
$0.001 per message
$0.003 per document
$0.002 per task

// Marketplace
Skill download: $5 (70% to creator)
Subscription skill: 30% platform, 70% creator
Template: Free (drives skill adoption)
```

### Data Model
```
Subscription {
  id: uuid
  organizationId: uuid
  planId: string
  status: "active" | "trialing" | "past_due" | "canceled"
  currentPeriodStart: timestamp
  currentPeriodEnd: timestamp
  cancelAtPeriodEnd: boolean
}

Invoice {
  id: uuid
  organizationId: uuid
  amount: number
  status: "draft" | "sent" | "paid" | "failed"
  invoiceDate: timestamp
  dueDate: timestamp
}

UsageRecord {
  id: uuid
  organizationId: uuid
  eventType: string
  count: int
  date: timestamp
}
```

---

## 6. Storage Service

**Purpose:** File storage, versioning, and access control

### API
```
POST   /storage/upload
  { organizationId: uuid, file: File, path: string }

GET    /storage/:fileId
DELETE /storage/:fileId

// Versioning
GET    /storage/:fileId/versions
GET    /storage/:fileId/versions/:version

// Sharing
POST   /storage/:fileId/share
  { users: uuid[], role: "view" | "edit" }

GET    /storage/search
  { query: string, mimeType?: string }
```

### Responsibilities
- Upload and download files
- Virus scanning
- Malware detection
- File versioning with rollback
- Encryption at rest (BYOK support)
- Access control and sharing
- Quotas and storage limits
- Archive and deletion workflows

### Used By
- **Workspace** (document files, attachments)
- **Diana** (image uploads for vision)
- **Marketplace** (skill packages, templates)

### Storage Options
- **S3-compatible** — AWS, Minio, Digital Ocean Spaces
- **GCS** — Google Cloud Storage
- **Azure** — Microsoft Azure Blob
- **Local** — On-premise option

---

## 7. Search Service

**Purpose:** Full-text and semantic search across all content

### API
```
GET /search
  {
    q: string,
    type?: ["document", "task", "project", "note"],
    limit?: 20,
    semantic?: boolean
  }

POST /search/index
  { id: uuid, type: string, content: string, metadata: json }

DELETE /search/index/:id

// Saved searches
POST  /search/saved
GET   /search/saved/:id
```

### Responsibilities
- Full-text search via Elasticsearch
- Semantic search via vector embeddings
- Real-time indexing
- Search result ranking
- Autocomplete and suggestions
- Search history
- Analytics on search patterns

### Used By
- **Workspace** (find documents, tasks, notes)
- **Marketplace** (find skills)
- **Business Cloud** (search CRM, documents)
- **Diana** (context retrieval for responses)

### Indexing Strategy
```
Document → Text extracted → Embedded via AI Router
                         ↓
              Vector index (Pinecone / Milvus)
                    ↓
          Available for semantic search
          
Document → Full-text indexed via Elasticsearch
            ↓
      Available for keyword search
```

---

## 8. Analytics Service

**Purpose:** Usage tracking, metrics, and business intelligence

### API
```
POST /analytics/event
  {
    userId: uuid,
    event: "message_sent" | "document_created" | "task_completed",
    metadata: json,
    timestamp?: timestamp
  }

GET /analytics/dashboard/:organizationId
GET /analytics/metrics
  { metric: "dau" | "messages" | "documents", period: "day" | "week" | "month" }

GET /analytics/cohort
  { createdAfter: timestamp, conversionGoal: string }
```

### Responsibilities
- Event collection and storage
- Real-time dashboards
- Cohort analysis
- Retention metrics
- Funnel analysis
- Anomaly detection
- Data export for BI tools

### Used By
- **All Programs** (track feature usage)
- **Enterprise** (admin dashboards)
- **Business Cloud** (business metrics)

### Key Metrics
```
DAU: Daily active users
MAU: Monthly active users
Messages sent: Diana interactions
Documents created: Workspace usage
Tasks completed: Workspace engagement
Marketplace installs: Skill adoption
Revenue: Billing metrics
Churn: Subscription cancellations
```

---

## 9. Event Service (Event Bus)

**Purpose:** Pub/sub for real-time system events

### API
```
// Subscribe to events
bus.subscribe("document.created", (event) => {
  // Handle event
})

// Publish events
bus.publish("document.created", {
  documentId: uuid,
  userId: uuid,
  projectId?: uuid,
  timestamp: timestamp
})

// Listen to multiple events
bus.subscribe(["task.*"], (event) => {
  // Handle any task event
})
```

### Events Emitted
```
user.created
user.updated
org.created
team.created
member.added

document.created
document.updated
document.deleted
document.shared

project.created
project.updated
project.completed

task.created
task.updated
task.completed

skill.installed
skill.uninstalled

message.sent
conversation.started

subscription.activated
subscription.canceled
```

### Used By
- **All Programs** (communicate state changes)
- **Real-time sync** (WebSockets)
- **Notifications** (trigger alerts)
- **Analytics** (event collection)
- **Workflow automation** (triggered actions)

### Implementation
```
Architecture: Redis Pub/Sub + Event Stream
├── Real-time subscribers (WebSockets)
├── Event persistence (for replay)
└── Event archival (for audit)
```

---

## 10. Audit Service

**Purpose:** Immutable record of all system actions for compliance

### API
```
GET /audit/logs
  {
    organizationId: uuid,
    userId?: uuid,
    action?: string,
    startDate?: timestamp,
    endDate?: timestamp,
    limit?: 1000
  }

GET /audit/export
  { organizationId: uuid, format: "csv" | "json" }

// Verify log integrity
GET /audit/verify
  { logId: uuid }
```

### Audit Trail Records
```
{
  id: uuid,
  organizationId: uuid,
  userId: uuid,
  action: "create" | "read" | "update" | "delete" | "share" | "export",
  resourceType: "document" | "task" | "user" | "settings" | ...,
  resourceId: uuid,
  changes?: {
    before: json,
    after: json
  },
  ipAddress: string,
  userAgent: string,
  timestamp: timestamp,
  status: "success" | "failure",
  reason?: string
}
```

### Responsibilities
- Log all privileged actions
- Immutable storage
- Tamper detection
- Retention policies
- Export for compliance audits
- Real-time alerting for suspicious activity

### Used By
- **Enterprise** (compliance reporting, SOC 2)
- **Security** (threat detection, forensics)

### Compliance Reports
- **SOC 2** — Audit trail for Type II
- **GDPR** — Data access and deletion logs
- **HIPAA** — PHI access tracking (for healthcare variants)

---

## 11. Security Service

**Purpose:** Encryption, key management, threat detection

### API
```
POST /security/encrypt
  { plaintext: string, key?: "default" | "customer" }

POST /security/decrypt
  { ciphertext: string, key?: "default" | "customer" }

GET /security/keys
POST /security/keys (BYOK)

POST /security/scan
  { userId: uuid, ipAddress: string }
```

### Responsibilities
- At-rest encryption
- In-transit encryption (TLS)
- Key management (KMS)
- BYOK (Bring Your Own Key) for enterprise
- Threat detection
- Brute-force prevention
- IP allowlisting
- Session security

### Used By
- **All Programs** (data protection)
- **Enterprise** (BYOK)
- **AIOS** (private device data)

### Encryption Strategy
```
Default: AES-256 with AWS KMS
BYOK: Customer-managed keys (HSM support)
Transit: TLS 1.3
Hashing: Argon2 for passwords
```

---

## Core Services — Dependencies

```
Payment Service
    ↓
Stripe API

Analytics Service
    ↓
Elasticsearch + Timeseries DB

Search Service
    ↓
Elasticsearch + Vector DB (Pinecone)

Storage Service
    ↓
S3 (or compatible)

AI Router
    ↓
OpenAI, Anthropic, Ollama, etc.

Notification Service
    ↓
SendGrid, Twilio, Firebase Cloud Messaging

Audit Service
    ↓
Immutable log store

Security Service
    ↓
AWS KMS or equivalent

Event Service (Event Bus)
    ↓
Redis or RabbitMQ

All Services
    ↓
PostgreSQL (primary data)
Redis (cache/sessions)
```

---

## Implementation Roadmap

### Phase 1 (Alpha 0.2) — ✅ Done
- Identity Service ✅
- AI Router ✅
- Memory Service ✅
- Event Service (basic) ✅

### Phase 2 (Alpha 0.3)
- Notification Service
- Analytics Service
- Storage Service (basic)
- Search Service (basic)

### Phase 3 (Beta)
- Payment Service
- Audit Service
- Security Service (advanced)
- Search Service (semantic)

### Phase 4 (v1.0)
- All services production-ready
- Redundancy and failover
- Multi-region support

---

## Deployment Architecture

```
Load Balancer (CloudFlare)
    ↓
API Gateway (Kong/Nginx)
    ├─→ Identity Service (3 replicas)
    ├─→ AI Router (5 replicas, auto-scaling)
    ├─→ Workspace Service (3 replicas)
    ├─→ Diana Service (2 replicas)
    ├─→ Notification Service (2 replicas)
    ├─→ Analytics Service (1 replica + workers)
    ├─→ Search Service (3 replicas, Elasticsearch)
    ├─→ Payment Service (1 replica, PCI-compliant)
    ├─→ Audit Service (1 replica)
    └─→ Security Service (1 replica)
    ↓
Cache Layer (Redis Cluster)
    ↓
Data Layer (PostgreSQL Primary + Replicas)
    ↓
External Services
    ├─ Stripe (payments)
    ├─ SendGrid (email)
    ├─ OpenAI (LLM)
    ├─ S3 (files)
    ├─ Elasticsearch (search)
    └─ Datadog (monitoring)
```

---

## Core Services — Next Steps

**This Week:**
- Finalize Payment Service API
- Plan Notification Service

**Next Sprint:**
- Launch Payment Service
- Launch Notification Service
- Expand Analytics

**Future:**
- Semantic search (requires embeddings)
- Advanced security (BYOK for enterprise)
- Multi-region deployment

Every program builds on these. No duplicates. Clean, maintainable architecture.

