# North Star Architecture

**The Official Platform Structure: AIGINVEST as an AI Operating System**

**Date:** 2026-07-06  
**Status:** Locked Architecture  
**Scope:** Web (2026) → AIOS (2027) → North Star ONE (2028+)  

---

## The Architecture Diagram

```
┌─────────────────────────────────────────────────────┐
│                    AIGINVEST                        │
│              (AI-First Company)                     │
│         Everything Serves Diana's Purpose           │
└──────────────────────┬──────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
    ┌───────────┐  ┌───────────┐  ┌──────────────┐
    │   DIANA   │  │MARKETPLACE│  │BUSINESS CLOUD│
    │ (Core AI) │  │(Extensions)  │(Enterprise)  │
    └───────────┘  └───────────┘  └──────────────┘
        │              │              │
        └──────────────┼──────────────┘
                       │
    ┌──────────────────┴──────────────────┐
    │      CORE PLATFORM SERVICES         │
    ├────────────────────────────────────┤
    │ • Identity & Auth                   │
    │ • Diana Memory & Knowledge Graph    │
    │ • AI Router (Intent Detection)      │
    │ • Workspace Orchestrator            │
    │ • Event Bus & Real-time Sync        │
    │ • Payments & Billing                │
    │ • Search & Indexing                 │
    │ • Storage & Backups                 │
    │ • Security & Encryption             │
    │ • Analytics & Audit                 │
    │ • Notifications                     │
    └────────────────────────────────────┘
                       │
         ┌─────────────┴─────────────┐
         │                           │
    ┌─────────────┐           ┌──────────────┐
    │   AIOS      │           │  Marketplace │
    │(Local AI    │           │ Apps & APIs  │
    │on Device)   │           │  (Defer)     │
    └─────────────┘           └──────────────┘
         │
         │
    ┌─────────────────────────────────────┐
    │     NORTH STAR ONE (Flagship)       │
    │  • Device + OS + Diana Embedded     │
    │  • AIOS Runtime                     │
    │  • Cloud Sync (Optional)            │
    └─────────────────────────────────────┘
```

---

## Layer 1: DIANA (The Center)

**Purpose:** Orchestrate all user work and goals

**Responsibilities:**
- 5 Permanent Roles (Advisor, Builder, Coordinator, Guardian, Learner)
- Mission orchestration
- Context management
- Decision-making
- User interaction

**Not a feature of AIGINVEST.**  
**AIGINVEST is built to support Diana.**

**Capabilities:**
- Understand intent from natural language
- Create workspaces from missions
- Access Knowledge Graph
- Coordinate across apps
- Learn and adapt
- Maintain context
- Explain decisions
- Guard privacy

**Constraints:**
- No autonomous action without permission
- No invention of facts
- Honest about uncertainty
- Respects user agency
- Transparent about reasoning

---

## Layer 2: MARKETPLACE + BUSINESS CLOUD (Extensions)

### MARKETPLACE
**Purpose:** Community extensions, third-party apps, integrations

**Model:**
- Developers build apps that Diana can call
- Diana decides when to invoke each app
- Revenue share model (70/30 or similar)
- Quality gates (no low-quality apps)
- Privacy-first (apps never see user data unless needed)

**Scope (V1.0):**
- Not included in initial launch
- Deferred to Sprint 5+

**Scope (Future):**
- Video conferencing integrations
- CRM integrations
- Accounting software
- Communication tools
- Custom domain-specific apps

### BUSINESS CLOUD
**Purpose:** Enterprise features

**Includes:**
- Advanced team collaboration
- SAML/LDAP authentication
- Role-based access control (RBAC)
- Audit logs
- Data residency options
- SLAs and support

**Scope (V1.0):**
- Not included in initial launch
- Core features support individual users only

**Scope (Future):**
- Teams (3-5 developers)
- Then companies (10-50 people)
- Then enterprises (500+ people)

---

## Layer 3: CORE PLATFORM SERVICES

**Purpose:** Reliable, well-defined infrastructure for Diana to work

**Services:**

### Identity & Authentication
- User registration/login
- OAuth integration
- Multi-factor authentication
- Session management
- API keys & webhooks

### Diana Memory & Knowledge Graph
- Long-term memory storage
- User preferences
- Company knowledge
- Document relationships
- Decision history
- Learning patterns

### AI Router (Intent Detection)
- Natural language understanding
- Mission classification
- Tool selection
- Routing to correct action
- Confidence scoring

### Workspace Orchestrator
- Create workspaces from missions
- Initialize apps
- Setup documents
- Configure permissions
- Manage lifecycle

### Event Bus & Real-time Sync
- Subscribe to changes
- Broadcast updates
- WebSocket connections
- Real-time collaboration
- Maintain consistency

### Payments & Billing
- Subscription management
- Usage-based pricing (future)
- Invoice generation
- Tax compliance
- Churn prevention

### Search & Indexing
- Full-text search
- Semantic search
- Cross-workspace search
- Relevance ranking
- Privacy-respecting

### Storage & Backups
- Document storage
- File management
- Backup/restore
- Version history
- Disaster recovery

### Security & Encryption
- Encryption at rest
- Encryption in transit
- Access control
- Audit logging
- Compliance (GDPR, SOC2, etc.)

### Analytics & Audit
- Usage tracking (privacy-first)
- Performance monitoring
- Error tracking
- Business analytics
- Decision audit trail

### Notifications
- In-app notifications
- Email digest
- Mobile push (future)
- Notification preferences
- Do-not-disturb modes

---

## Layer 4: AIOS (Offline-First AI on Device)

**Purpose:** Make Diana work on device, independent of cloud

**Timeline:** Sprint 8-12 (2027)

**Architecture:**
- Same Diana orchestration engine
- Local-first data architecture
- Optional cloud sync
- Works offline
- Same 5 roles

**Scope:**
- Everything from web version
- Offline document editing
- Local knowledge base
- Device-specific features

**Not:**
- Separate product
- Different UI
- Different capabilities
- Replacement for cloud version

**Together:**
- Web Diana + Device Diana = Seamless experience
- Cloud for collaboration, Device for privacy
- Both connected via North Star ONE

---

## Layer 5: NORTH STAR ONE (Flagship Device & OS)

**Purpose:** Hardware + OS + Software that embodies AIGINVEST vision

**Timeline:** 2028+ (long-term)

**Form Factor:**
- Tablet-like device
- Powerful enough for local AI
- Beautiful industrial design
- Designed for work (not consumption)
- Connectivity optional

**Software:**
- AIOS running Diana
- Optimized for touch + voice
- Cloud sync if desired
- Always user-controlled
- No telemetry without permission

**Not an iPhone competitor.**

**It's a work device that puts Diana in your hands.**

---

## The Inversion: How Layers Connect

### Traditional (Software Company)
```
App Layer (UI)
App Logic Layer
Database Layer
Infrastructure

User → App → Features
```

### AIGINVEST (AI Company)
```
Diana (AI Orchestrator)
     ↓
Decides what to do
     ↓
Calls Core Platform
     ↓
Apps & Services respond
     ↓
Updates realtime
     ↓
Diana learns

User → Diana → Everything
```

---

## Architecture Principles

### 1. Diana-First
Every component exists to make Diana more capable.

If it doesn't serve Diana, it's not core.

### 2. Mission-Driven
Users think in missions, not apps.

Diana orchestrates apps to accomplish missions.

### 3. Privacy-Conscious
User data never leaves workspace unless user approves.

Encryption by default.

No data sales.

### 4. Transparent
Diana explains decisions.

Diana shows reasoning.

Diana acknowledges uncertainty.

### 5. User-Agency
Diana suggests, doesn't demand.

Diana explains, doesn't assume.

Diana learns, doesn't judge.

### 6. Extensible
Marketplace builds on Diana.

Apps don't replace Diana.

Diana orchestrates all extensions.

### 7. Scalable
From individual (web) → team (Business Cloud) → enterprise (future)

Same architecture, more features.

---

## Data Flow: Example

User says: **"I want to build an AI startup"**

```
1. DIANA receives input
   ├─ "I want to..." (mission indicator)
   └─ "build an AI startup" (domain + category)

2. DIANA (Advisor Role)
   ├─ Recognizes: Mission type
   └─ Decides: Use Builder role

3. AI ROUTER (Core Platform)
   ├─ Classifies: "Start a Company" mission
   ├─ Confidence: 0.98
   └─ Template: startup-v1

4. WORKSPACE ORCHESTRATOR (Core Platform)
   ├─ Creates: Project "My AI Startup"
   ├─ Creates: Business Plan document (AI variant)
   ├─ Creates: Financial model
   ├─ Creates: Roadmap
   ├─ Creates: Task board (8 phases)
   ├─ Initializes: Memory profile
   └─ Triggers: Events (project.created, documents.created, etc.)

5. EVENT BUS broadcasts
   ├─ Frontend receives events
   ├─ Sidebar updates in real-time
   ├─ Chat interface updates
   └─ All tabs stay in sync

6. DIANA (Learner Role)
   ├─ Records: User is building AI startup
   ├─ Stores: in DianaMemory
   ├─ Links: Related documents + tasks
   └─ Prepares: Next phase guidance

7. DIANA responds to user
   "Perfect! I've prepared your AI startup workspace.
   
   ✓ Business Plan (with AI sections)
   ✓ Financial Model
   ✓ Roadmap
   ✓ Task timeline
   ✓ Pitch deck
   
   Phase 1: Ideation & Validation
   I recommend: Interview 10 potential customers
   
   Ready to start?"

8. User clicks "Start"

9. DIANA (Coordinator Role)
   ├─ Creates: Interview guide document
   ├─ Creates: Task: "Schedule first interview"
   ├─ Sets: Due date 3 days from now
   └─ Reminds: "Time to validate your idea"
```

---

## Launch Timeline

### V1.0 (Sprint 2-7)
```
Web Platform
  ├─ Diana + 5 roles (basic)
  ├─ Core Platform services
  ├─ 5 mission templates
  └─ Single user experience

Target: Individual creators, founders, learners
```

### V1.1 (Sprint 8-12)
```
Enhanced Diana
  ├─ Proactive advice
  ├─ Better learning
  ├─ AIOS foundation
  ├─ Marketplace infrastructure
  └─ Better coordination

Target: Still individual but more intelligent
```

### V2.0 (Sprint 13+)
```
Teams & Enterprise
  ├─ Business Cloud
  ├─ Team collaboration
  ├─ RBAC + SAML
  ├─ Advanced analytics
  └─ Marketplace launch

Target: Teams and companies
```

### V3.0 (2027+)
```
AIOS
  ├─ Local-first Diana
  ├─ Device support
  ├─ Offline-first
  ├─ Cloud sync
  └─ Integration with web

Target: Local first, optional cloud
```

### North Star ONE (2028+)
```
Flagship Device
  ├─ Custom hardware
  ├─ AIOS OS
  ├─ Diana embedded
  ├─ Beautiful design
  └─ Premium experience

Target: The complete AIGINVEST vision
```

---

## Why This Architecture Works

### 1. Clear Layering
Each layer has one job.

No confusion about responsibility.

Easy to scale or modify.

### 2. Diana-Centric
Everything serves Diana's growth.

Diana's capabilities drive feature priorities.

Not app proliferation.

### 3. Inevitable Expansion
Starts simple (Diana + missions).

Naturally adds layers (AIOS, Marketplace).

Reaches vision (North Star ONE).

### 4. Defensible Position
Hard to copy the whole stack.

Marketplace creates moat.

AIOS + North Star ONE lock in customers.

### 5. Capital-Efficient
Start with web (low cost).

AIOS later (when needed).

North Star ONE last (when mature).

Not all-in on hardware upfront.

---

## Success Metrics by Layer

### Diana Layer
- Questions answered correctly (%)
- Missions completed end-to-end (%)
- User satisfaction (NPS)
- Trust in Diana (survey)

### Marketplace Layer (Future)
- Apps installed
- Revenue per app
- Developer satisfaction

### Business Cloud Layer (Future)
- Teams using features
- Enterprise customers
- ARR

### AIOS Layer (Future)
- Users on local-first
- Cloud sync activity
- Device adoption

### North Star ONE (Future)
- Devices sold
- Premium positioning
- Brand differentiation

---

## The Vision

When someone uses AIGINVEST:

They don't think about apps, databases, or servers.

They think: **"I'm talking to Diana, and she's helping me accomplish my goal."**

That experience is built by this architecture.

Diana is the center.

Everything else supports her.

That's the North Star.

