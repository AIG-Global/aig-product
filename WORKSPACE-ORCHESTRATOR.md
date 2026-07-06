# Workspace Orchestrator Service

**Reference:** [SERVICE-SPECIFICATION-TEMPLATE.md](SERVICE-SPECIFICATION-TEMPLATE.md)

Following the standard 14-section service specification format.

---

## 1. Service Identity

```yaml
Service Name: Workspace Orchestrator
Service Code: srv-workspace-orchestrator
Team Ownership: Platform
Status: Design (ready for Sprint 2-3 implementation)
Version: 1.0 (Design)
Last Updated: 2026-07-06
```

---

## 2. Purpose & Principles

**One-line purpose:**
Transform user intent into a fully-initialized, production-ready working environment.

**Responsibilities:**
- Create workspaces from templates
- Initialize all connected services
- Set up default folders and structure
- Configure permissions and access
- Initialize user memory
- Coordinate cross-service setup
- Not responsible for: Individual service operations (Projects, Documents, Tasks handle their own operations)

**Principles:**
- **Orchestration, not duplication:** Never duplicate what other services do; coordinate them
- **Atomic transactions:** Either everything succeeds or nothing persists
- **Template-driven:** Different workspace types for different use cases
- **User-centric:** Every workspace is tailored to the user's intent
- **Event-driven:** Publishes events for Diana and real-time sync

---

## 3. Functional Requirements

**Primary Use Cases:**

1. **User expresses intent** → Orchestrator creates production-ready environment
   - "Help me start my company" → Startup template workspace created
   - "Set up my freelance business" → Freelancer template workspace created
   - "Organize my team project" → Team project template workspace created

2. **Diana makes decisions** → Orchestrator executes them
   - Diana decides to create a project → Calls Orchestrator
   - Diana decides to initialize templates → Calls Orchestrator
   - Diana decides to onboard user → Calls Orchestrator

3. **New user onboarding** → Orchestrator sets up complete environment
   - Sign up → Default workspace created
   - Choose template → Pre-configured with examples
   - Start working → Everything is ready

**Features Provided:**
- Create workspace from template
- Initialize workspace (folders, memory, permissions)
- Connect all services (Projects, Documents, Tasks, Calendar, Knowledge)
- Set up default content (examples, guides, templates)
- Configure workspace settings
- Export workspace state
- Archive/delete workspace

**Constraints:**
- Workspace creation must complete in < 2 seconds
- All sub-operations must succeed atomically (no partial workspaces)
- Support for 5+ concurrent workspace creations per second per instance
- No workspace state inconsistencies (eventual consistency acceptable for events)

---

## 4. Architecture

**High-level Design:**

```
CreateWorkspace Request
        ↓
┌──────────────────────────────┐
│  Workspace Orchestrator      │
│                              │
│ 1. Validate input           │
│ 2. Create workspace record  │
│ 3. Create projects          │
│ 4. Create documents         │
│ 5. Create tasks             │
│ 6. Initialize memory        │
│ 7. Set permissions          │
│ 8. Publish events           │
│ 9. Return workspace state   │
└──────────────────────────────┘
        ↓
Return Complete Environment
```

**Key Components:**
- **Template Engine:** Load and customize workspace templates
- **Service Coordinator:** Call multiple services in correct order
- **Transaction Manager:** Ensure atomic operations (commit or rollback)
- **Event Publisher:** Notify real-time subscribers
- **State Manager:** Track workspace setup progress

**Deployment:**
- Stateless service (can scale horizontally)
- 3-5 instances minimum
- Auto-scale on queue depth (workspace creations)
- Failover to backup instance on timeout

---

## 5. API Specification

### CreateWorkspace (Primary Operation)

```
POST /api/v1/workspaces
Description: Create a complete workspace from intent
Authentication: Required (JWT with user scope)
Rate Limit: 10 per minute per user

Request:
{
  "name": "My Startup",
  "description": "Launching my B2B SaaS",
  "template": "startup",  // or "freelancer", "team", "personal", "research"
  "settings": {
    "defaultVisibility": "private",
    "allowCollaboration": true,
    "theme": "light"
  }
}

Response (201 Accepted):
{
  "workspaceId": "ws-uuid",
  "status": "initializing",
  "createdAt": "2026-07-06T10:00:00Z",
  
  "initialized": {
    "projects": [
      {
        "id": "p-1",
        "name": "Startup Launch",
        "status": "active",
        "created": true
      }
    ],
    "documents": [
      {
        "id": "d-1",
        "name": "Business Plan",
        "type": "template",
        "created": true
      },
      {
        "id": "d-2",
        "name": "Financial Model",
        "type": "template",
        "created": true
      }
    ],
    "tasks": [
      {
        "id": "t-1",
        "title": "Validate business idea",
        "status": "todo",
        "created": true
      }
    ],
    "memory": [
      {
        "key": "workspace_intent",
        "value": "startup",
        "confidence": 0.95
      }
    ]
  },
  
  "readyForUse": true,
  "nextSteps": [
    "Edit business plan",
    "Schedule team meeting",
    "Research competitors"
  ]
}

Error Responses:
400 Bad Request: Invalid template or settings
401 Unauthorized: User not authenticated
403 Forbidden: User quota exceeded
429 Too Many Requests: Rate limited
500 Internal Server Error: Service coordination failed
```

### GetWorkspace

```
GET /api/v1/workspaces/:workspaceId
Description: Retrieve complete workspace state
Authentication: Required (JWT with user scope)

Response (200):
{
  "workspaceId": "ws-uuid",
  "name": "My Startup",
  "description": "...",
  "template": "startup",
  "status": "active",
  "createdAt": "...",
  "updatedAt": "...",
  "owner": "user-uuid",
  
  "contents": {
    "projects": [Project[]],
    "documents": [Document[]],
    "tasks": [Task[]],
    "contacts": [Contact[]],
    "memory": [DianaMemory[]]
  },
  
  "settings": {...},
  "permissions": {...}
}
```

### UpdateWorkspace

```
PATCH /api/v1/workspaces/:workspaceId
Description: Update workspace settings
Authentication: Required (owner/admin)

Request:
{
  "name": "New Name",
  "description": "New description",
  "settings": {
    "defaultVisibility": "shared"
  }
}

Response (200): Updated workspace
```

### ArchiveWorkspace

```
DELETE /api/v1/workspaces/:workspaceId
Description: Archive workspace (soft delete)
Authentication: Required (owner/admin)

Response (204): No content
```

### Webhook: WorkspaceCreated

```
Event: workspace.created
When: New workspace successfully initialized
Payload:
{
  "event": "workspace.created",
  "timestamp": "2026-07-06T10:00:00Z",
  "workspaceId": "ws-uuid",
  "userId": "user-uuid",
  "template": "startup",
  "itemCounts": {
    "projects": 1,
    "documents": 2,
    "tasks": 5
  }
}
```

---

## 6. Data Model

**Primary Entities:**

```
Workspace {
  id: uuid (primary key)
  userId: uuid (foreign key to User)
  organizationId: uuid (foreign key to Organization)
  
  name: string
  description: text
  template: enum ("startup", "freelancer", "team", "personal", "research", "custom")
  
  status: enum ("initializing", "active", "archived", "deleted")
  
  settings: json {
    defaultVisibility: enum ("private", "shared", "public")
    allowCollaboration: boolean
    theme: enum ("light", "dark", "auto")
    notifications: boolean
  }
  
  permissions: json {
    owner: uuid
    admins: uuid[]
    members: uuid[]
    viewers: uuid[]
  }
  
  contents: {
    projectCount: integer
    documentCount: integer
    taskCount: integer
    contactCount: integer
  }
  
  createdAt: timestamp
  updatedAt: timestamp
  archivedAt: timestamp (nullable)
}

Template {
  id: uuid
  name: string
  templateType: enum
  
  defaultProjects: ProjectTemplate[]
  defaultDocuments: DocumentTemplate[]
  defaultTasks: TaskTemplate[]
  defaultMemories: MemoryTemplate[]
  
  createdAt: timestamp
}

WorkspaceInitializationLog {
  id: uuid
  workspaceId: uuid (foreign key)
  
  step: enum ("validate", "create_projects", "create_documents", "create_tasks", "initialize_memory", "set_permissions", "publish_events")
  status: enum ("pending", "in_progress", "completed", "failed")
  
  result: json
  error: text (nullable)
  
  duration_ms: integer
  
  createdAt: timestamp
}
```

**Relationships:**
```
User --[owns many]--> Workspace
Organization --[contains many]--> Workspace
Workspace --[contains many]--> Project
Workspace --[contains many]--> Document
Workspace --[contains many]--> Task
Workspace --[initializes many]--> DianaMemory
```

**Constraints:**
- Workspace name unique per organization
- User can have up to 50 active workspaces (quota)
- All child entities must be deleted before workspace deletion
- userId and organizationId immutable after creation

---

## 7. Dependencies

**Internal Services Used:**
- **Projects Service:** `createProject(workspaceId, name, description)` - Create project
- **Documents Service:** `createDocument(projectId, name, template)` - Create doc from template
- **Tasks Service:** `createTask(projectId, title, description)` - Create task
- **Memory Service:** `createMemory(userId, type, content, confidence)` - Initialize memory
- **Event Service:** `publish(event)` - Publish workspace.created event
- **Identity Service:** `getUser(userId)`, `getOrganization(orgId)` - Verify permissions

**External APIs:** None (orchestrates internal services only)

**Databases:**
- PostgreSQL: Workspace, Template, WorkspaceInitializationLog tables
- Redis: Template cache (1-hour TTL)

**Third-party Libraries:**
- `uuid` (v4) - Generate workspace IDs
- `joi` - Validate workspace creation request

---

## 8. Security & Authentication

**Access Control:**
- Only authenticated users can create workspaces
- Only workspace owner/admin can update or delete
- Only organization members can access org workspaces
- Nested object access validated (can only create in accessible organization)

**Input Validation:**
- Workspace name: 1-255 characters, alphanumeric + spaces + hyphens
- Template: must be valid enum value
- Settings: must match schema
- SQL injection prevention: Use parameterized queries
- Rate limiting: 10 workspace creations per minute per user

**Authorization:**
- Ownership verified before all mutations
- Cross-organization data never leaked
- Cascading delete permissions verified

**Threat Mitigation:**
- DDoS: Rate limiting + queue backpressure
- Quota abuse: User workspace quota enforced (50 max)
- Resource exhaustion: Timeout on any service call (5s)

---

## 9. Privacy & Data Protection

**Data Classification:**
- Workspace name: Semi-public (visible to members)
- Settings: Private (user-only)
- Contents: Depends on child service (inherited from Projects, Documents, Tasks)
- Workspace owner: Private (audit only)

**Encryption:**
- Settings stored encrypted at rest
- All API calls over TLS 1.3
- No sensitive data in logs

**Data Retention:**
- Active workspaces: Indefinite (until deleted)
- Archived workspaces: 1 year (then auto-deleted)
- Initialization logs: 30 days
- Deletion verified (not recoverable)

**User Rights:**
- Users can download workspace state (JSON export)
- Users can delete workspace (irreversible)
- Users can transfer workspace ownership
- User can request data deletion

**GDPR Compliance:**
- Right to access: Full workspace export
- Right to delete: Workspace deletion removes all data
- Right to rectify: Edit workspace name/description
- Data processing agreement: Standard terms

---

## 10. Monitoring & Observability

**Key Metrics:**
- `workspace_creation_count`: Total workspaces created (alert if drops significantly)
- `workspace_creation_latency_p95`: 95th percentile creation time (alert if > 3s)
- `workspace_initialization_failure_rate`: Failed creations (alert if > 1%)
- `template_load_latency`: Time to load template (alert if > 200ms)
- `service_coordination_errors`: Errors calling dependent services (alert if > 10/min)

**Health Checks:**
```
GET /api/v1/health/ready
Response: { "status": "ready", "dependencies": { "projects": "ok", "documents": "ok", "tasks": "ok", "memory": "ok" } }
```

**Logging:**
- Level INFO: Workspace created, template loaded
- Level WARN: Service call slow (> 1s), fallback used
- Level ERROR: Initialization failed, service unavailable
- Mask: Never log workspace contents, only IDs and counts

**Alerting:**
- Alert if creation latency > 3 seconds
- Alert if failure rate > 1%
- Alert if any dependent service unreachable
- Alert if template cache miss rate > 20%

**Debugging:**
- Enable debug mode: `ORCHESTRATOR_DEBUG=true`
- Returns full initialization step-by-step details
- Can retry failed workspace creation

---

## 11. Testing Strategy

**Unit Tests:**
- Template loading and validation
- Workspace model validation
- Permission checks
- Service call composition

**Integration Tests:**
- Create workspace with each template type
- Verify all sub-services called correctly
- Verify atomic transactions (all succeed or all rollback)
- Verify events published

**E2E Tests:**
- User creates workspace via API
- All child objects created in database
- Verify complete workspace state returned
- Verify sidebar reflects new workspace
- Verify Diana can reference workspace

**Load Testing:**
- Create 100 concurrent workspaces
- Measure latency
- Verify no data corruption
- Verify event queue handles volume

---

## 12. Deployment & Operations

**Prerequisites:**
- PostgreSQL v12+
- Redis for template cache
- Access to dependent services (Projects, Documents, Tasks, Memory, Events)
- Service account with org write access

**Deployment Process:**
- Deploy service
- Run migrations for Workspace table
- Load template definitions
- Warmup template cache
- Smoke test with sample creation
- Verify all dependent services respond

**Rollback:**
- If new template breaks: Revert template definition
- If service breaks: Rollback deployment
- If database breaks: Restore from backup

**Maintenance:**
- Template cache warming: Every 6 hours
- Workspace count audit: Weekly
- Database cleanup (archived > 1 year): Monthly

---

## 13. Performance & Scalability

**Performance Targets:**
- Workspace creation: < 2 seconds (p95)
- Template loading: < 200ms
- Service coordination: < 500ms
- Event publishing: < 100ms

**Scaling Strategy:**
- Horizontal: Stateless, can scale to N instances
- Caching: Template cache in Redis (1-hour TTL)
- Queuing: Large creations queued (async pattern)
- Circuit breaker: If dependent service slow, fail fast

**Bottlenecks & Solutions:**
- Database writes slow: Use batch inserts for multiple sub-objects
- Service coordination slow: Parallel calls to independent services (Projects + Documents in parallel)
- Event publishing slow: Async publish (don't wait for subscribers)

---

## 14. Future Roadmap

**Planned Features:**
- Custom template builder (let users create templates)
- Workspace cloning (duplicate workspace + content)
- Workspace sharing (multi-user workspaces)
- Team workspace provisioning (admin bulk-creates)

**Technical Debt:**
- None identified yet (new service)

**Migration Path:**
- If architecture changes, workspaces can be migrated via export/import

---

## Usage Example

```typescript
// Diana wants to create a startup workspace
const workspaceService = new WorkspaceOrchestratorService();

const workspace = await workspaceService.createWorkspace(
  userId,
  {
    name: "My AI Startup",
    description: "Building an AI operating system",
    template: "startup",
    settings: {
      defaultVisibility: "private",
      allowCollaboration: true,
      theme: "dark"
    }
  }
);

// Response: Complete workspace with projects, docs, tasks, memory initialized
console.log(workspace);
// {
//   workspaceId: "ws-123",
//   status: "active",
//   projects: [{ id: "p-1", name: "Startup Launch" }],
//   documents: [{ id: "d-1", name: "Business Plan" }, ...],
//   tasks: [{ id: "t-1", title: "Validate idea" }, ...],
//   memory: [{ key: "workspace_intent", value: "startup" }],
//   readyForUse: true
// }
```

---

## Why This Service Exists

Most platforms make users click around and manually set things up.

The Workspace Orchestrator is what makes **Diana feel like an operating system** instead of a chatbot.

One sentence. One intent. Entire environment ready.

That's the product differentiator.

