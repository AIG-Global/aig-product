# Diana: An Operating System for Work

**The Strategic Pivot**

Most AI assistants are **reactive**: User asks → AI answers.

Diana is **orchestrative**: User expresses intent → Diana creates an entire working environment.

---

## The Shift

### Before (Reactive Chatbot)
```
User: "I want to start a company"
Diana: "Great! Here are 10 tips for starting a company..."
User: Opens Projects app
User: Creates a project
User: Opens Documents app
User: Creates a business plan document
...
```

### After (Operating System)
```
User: "I want to start a company"
Diana:
  ✓ Creates Workspace
  ✓ Creates Project "My Company"
  ✓ Creates Business Plan document (template)
  ✓ Creates Task board (startup phases)
  ✓ Creates Knowledge folder
  ✓ Initializes memory
  ✓ Starts guided conversation
```

One phrase. Entire environment ready.

---

## Diana's Internal Architecture

When a user sends a message, Diana follows this decision tree:

```
1. UNDERSTAND
   ├─ Parse user intent (tool calling)
   ├─ Classify action type (question, request, command)
   └─ Extract parameters (what, where, who, when)

2. RETRIEVE CONTEXT
   ├─ Load conversation history
   ├─ Load user memories
   ├─ Load relevant projects/documents
   └─ Load available tools

3. DECIDE ACTION
   ├─ Does this require workspace changes?
   ├─ Do I need to create resources?
   ├─ Do I need to fetch data?
   └─ What tool(s) should I invoke?

4. EXECUTE TOOLS
   ├─ Call Workspace Orchestrator (if needed)
   ├─ Call Projects API (if needed)
   ├─ Call Documents API (if needed)
   ├─ Call Tasks API (if needed)
   └─ Publish events (real-time sync)

5. GENERATE RESPONSE
   ├─ Summarize what was created
   ├─ Suggest next steps
   ├─ Provide guidance
   └─ Stream to user

6. EXTRACT & SAVE MEMORY
   ├─ Save facts about user
   ├─ Save project context
   ├─ Save decisions made
   └─ Update confidence scores
```

**Key difference:** Diana doesn't just talk. Diana orchestrates.

---

## The Workspace Orchestrator

This is the new core service that makes Diana an OS.

```
┌─────────────────────────────────────────┐
│    Diana (Chat + Orchestration Engine)   │
└────────────────┬────────────────────────┘
                 │
         ┌───────┴────────┐
         │                │
    Understand         Orchestrate
         │                │
    Tool Calling      Workspace
      Engine          Orchestrator
         │                │
         └───────┬────────┘
                 │
    ┌────────────┼────────────┐
    │            │            │
Projects      Documents     Tasks
    │            │            │
 Database    Database      Database
```

**Workspace Orchestrator responsibilities:**
- Create workspace (folders, permissions, defaults)
- Initialize workspace (memory, knowledge base, settings)
- Connect services (give Diana access to all tools)
- Sync state (real-time updates across clients)
- Manage lifecycle (archive, export, delete)

### API

```typescript
// Create complete workspace from intention
createWorkspace(userId: string, request: {
  name: string,
  description: string,
  template?: string // "startup", "team", "project", "personal"
}) → {
  workspaceId: uuid,
  projects: Project[],
  documents: Document[],
  tasks: Task[],
  memory: DianaMemory[],
  status: "ready" | "initializing"
}

// Example response for "I want to start a company"
{
  workspaceId: "ws-123",
  projects: [
    { id: "p-1", name: "My Company", status: "active" }
  ],
  documents: [
    { id: "d-1", name: "Business Plan", type: "template", owner: "Diana" },
    { id: "d-2", name: "Financial Projections", type: "template", owner: "Diana" }
  ],
  tasks: [
    { id: "t-1", title: "Validate business idea", status: "todo" },
    { id: "t-2", title: "Create financial model", status: "todo" },
    { id: "t-3", title: "Build MVP", status: "todo" }
  ],
  memory: [
    { key: "user_intent", value: "start_company", confidence: 0.95 },
    { key: "company_stage", value: "ideation", confidence: 0.9 }
  ],
  status: "ready"
}
```

---

## Core 10 Applications

Not dozens of features. **Ten core applications** that every user understands immediately.

```
1. Diana              AI operating system + chat
2. Workspace          Your working environment
3. Projects           Organize work into projects
4. Documents          Create and manage content
5. Tasks              Track what needs doing
6. Calendar           Schedule and timeline
7. Knowledge          Build your knowledge base
8. Marketplace        Install skills and integrations
9. Settings           Configure workspace
10. Profile           Your account and preferences
```

Everything else is a **marketplace app** or **future capability**, not core.

---

## The Knowledge Graph

Diana's intelligence multiplies when she can connect context.

```
USER
├── Workspaces[]
│   ├── Projects[]
│   │   ├── Documents[]
│   │   ├── Tasks[]
│   │   └── Conversations[]
│   ├── Contacts[]
│   ├── Calendar[]
│   └── Knowledge[]
│       └── Topics[]
│           └── Relations[]
```

This allows Diana to answer questions like:

- **"What project did we discuss last Tuesday?"** → Query Conversations + Projects linked to date
- **"Show every document related to North Star ONE"** → Query Knowledge Graph for "North Star ONE" nodes
- **"Which tasks are blocking the marketplace launch?"** → Query Tasks linked to "marketplace" project with status "blocked"
- **"Who did I talk to about fundraising?"** → Query Contacts + Conversations linked to "fundraising" topic

Diana becomes a **search engine for your work**.

---

## Example: "Launch my startup"

User message:
```
"Help me launch my startup. I want to build a B2B SaaS for enterprise resource planning."
```

Diana's internal processing:

```
1. UNDERSTAND
   Intent: Create workspace for startup launch
   Parameters: 
     - company_type: B2B SaaS
     - target_market: Enterprise
     - product_category: ERP

2. RETRIEVE CONTEXT
   User memories: First-time user, technical founder
   Available templates: startup, saas, b2b
   Tools available: Workspace Orchestrator, Projects, Documents, Tasks

3. DECIDE ACTION
   ✓ Create workspace (via Orchestrator)
   ✓ Create "Launch Startup" project
   ✓ Generate B2B SaaS business plan template
   ✓ Create startup phase tasks (validate, build, launch)
   ✓ Initialize memory with startup context

4. EXECUTE TOOLS
   workspace = createWorkspace(user, {
     name: "My SaaS Startup",
     description: "B2B SaaS for Enterprise ERP",
     template: "saas_enterprise"
   })

5. GENERATE RESPONSE
   "✓ Workspace created!
   
   I've set up everything for your startup:
   
   📁 Project: Launch Startup
   📄 Documents: Business Plan, Financial Model, GTM Strategy
   ✓ Task Board: Pre-populated with startup phases
   
   Next steps:
   1. Validate your business idea (task ready)
   2. Research competitor landscape (I can help)
   3. Define your MVP (let's discuss)
   
   What would you like to tackle first?"

6. SAVE MEMORY
   - user_intent: launch_startup
   - company_type: b2b_saas
   - target_market: enterprise
   - founder_type: technical
   - project_created: "Launch Startup"
```

User now has:
- ✅ Organized workspace
- ✅ Project structure
- ✅ Documents ready to edit
- ✅ Task list
- ✅ Next steps guided by Diana
- ✅ Everything saved in knowledge graph

No clicking around. No "which app do I open first?" No manual setup.

---

## Why This Matters

### For Users
- **Speed:** Environment ready instantly
- **Clarity:** Guided path forward
- **Coherence:** Everything connected
- **Continuity:** Diana remembers context

### For AIGINVEST
- **Defensibility:** Not just a chatbot (every AI company has that)
- **Stickiness:** Becomes the center of their work
- **Moat:** Knowledge graph + orchestration unique to us
- **Scaling:** Each new app auto-integrates via orchestrator

### For AIOS & North Star ONE
- **Foundation:** Diana OS pattern scales to device OS
- **Continuity:** Same orchestration logic works everywhere
- **Sync:** User's work state follows them across devices
- **Experience:** Consistent "intent → environment" everywhere

---

## Implementation Strategy

### Phase 1 (Sprint 2-3): Foundation
- ✅ Real AI provider (AIG-101)
- ✅ Streaming (AIG-102)
- ✅ Memory (AIG-103)
- 🆕 **Workspace Orchestrator service**
- 🆕 **Project creation via orchestrator**
- 🆕 **Simple knowledge graph (MVP)**

### Phase 2 (Sprint 4-5): Capabilities
- Document orchestration
- Task auto-generation
- Calendar integration
- Marketplace foundation

### Phase 3 (Sprint 6-7): Polish
- Advanced memory (semantic search)
- Proactive guidance (AIG-105)
- Knowledge graph depth
- Enterprise features

---

## The Fundamental Difference

```
Chatbot:                  OS:
User: "Help me"           User: "Help me"
AI: "Here's advice"       AI: Creates entire environment
User: Manual setup        User: Starts working immediately
```

**AIGINVEST is the latter.**

Diana doesn't just assist.

Diana orchestrates.

Diana makes your work environment.

---

## Next Step

Build the **Workspace Orchestrator** service.

This becomes the heart of everything.

Every future feature attaches to it.

Every capability flows through it.

This is what makes Diana an operating system instead of a chatbot.

