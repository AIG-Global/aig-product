# Knowledge Graph: Connecting All Context

**How Diana understands your work holistically**

---

## The Concept

Most AI assistants operate in isolation: They see only the current message.

Diana's Knowledge Graph connects everything you do, creating a **searchable, queryable map of your work**.

```
One user message + entire work history + all related context = Diana can answer anything
```

---

## Architecture

### Three Layers

#### Layer 1: Entity Nodes

Every important thing is a node:

```
Users
├── User {id, name, email, company, role}

Workspaces
├── Workspace {id, name, template, created_date}

Projects
├── Project {id, name, status, owner, created_date}

Documents
├── Document {id, name, type, content_summary, owner}

Tasks
├── Task {id, title, status, priority, assigned_to, due_date}

Topics (Knowledge)
├── Topic {id, name, description, content}

Conversations
├── Conversation {id, participants, created_date}

Contacts
├── Contact {id, name, email, company, relationship}

Events (Calendar)
├── Event {id, title, date, participants, description}
```

#### Layer 2: Relationship Edges

Nodes connect through relationships:

```
User --[owns]--> Workspace
User --[created]--> Project
User --[mentioned_in]--> Conversation

Workspace --[contains]--> Project
Workspace --[contains]--> Document
Workspace --[contains]--> Task

Project --[has]--> Document
Project --[has]--> Task

Document --[references]--> Topic
Document --[created_by]--> User
Document --[mentions]--> Contact

Task --[assigned_to]--> User
Task --[blocks]--> Task
Task --[relates_to]--> Project

Conversation --[discusses]--> Project
Conversation --[discusses]--> Document
Conversation --[mentions]--> Contact

Topic --[links_to]--> Topic
Topic --[used_in]--> Document
```

#### Layer 3: Properties

Nodes have rich properties:

```
Document {
  id: uuid
  name: string
  type: enum ("business_plan", "spec", "proposal", "note", "template")
  content_summary: text (AI-generated)
  
  created_by: User
  created_date: timestamp
  updated_date: timestamp
  
  projects: Project[] (which projects does it relate to)
  topics: Topic[] (which knowledge areas)
  mentions: Contact[] (people mentioned)
  
  metadata: {
    sentiment: enum ("positive", "neutral", "negative")
    importance: float (0-1)
    stage: enum ("draft", "review", "approved", "published", "archived")
    tags: string[]
  }
}
```

---

## Storage Implementation

### Primary Store: Neo4j (or similar graph database)

```
MATCH (u:User {id: $userId})
  -[:OWNS]->(w:Workspace)
  -[:CONTAINS]->(p:Project)
  -[:CONTAINS]->(t:Task {status: "blocked"})

RETURN p, COUNT(t) as blocked_count
```

### Secondary Store: PostgreSQL (normalized)

All entities also stored in PostgreSQL for:
- Transactional consistency
- Complex queries (full-text search)
- Backups
- Analytics

### Cache Layer: Redis

Recent queries cached:
- "What projects did we discuss this week?" (1 hour)
- "Show all tasks assigned to Alice" (1 hour)
- "Which documents mention product roadmap?" (1 hour)

---

## Diana's Graph Queries

### Query Examples

**"What project did we discuss last Tuesday?"**

```cypher
MATCH (conversation:Conversation)
  -[:DISCUSSES]->(project:Project)
WHERE conversation.created_date >= date("2026-06-29")
  AND conversation.created_date < date("2026-07-01")
RETURN project, conversation.date as discussed_date
```

**"Show every document related to North Star ONE"**

```cypher
MATCH (doc:Document)
OPTIONAL MATCH (doc)-[:REFERENCES]->(topic:Topic)
WHERE doc.name CONTAINS "North Star" 
   OR topic.name CONTAINS "North Star"
   OR doc.content_summary CONTAINS "North Star"
RETURN doc, COLLECT(topic) as related_topics
```

**"Which tasks are blocking the marketplace launch?"**

```cypher
MATCH (project:Project {name: "Marketplace"})
  -[:CONTAINS]->(blocking_task:Task {status: "blocked"})
  <-[:BLOCKS]-(blocker:Task)
RETURN blocker, blocking_task, project
```

**"What did I talk to Alice about?"**

```cypher
MATCH (me:User {id: $userId})
  -[:PARTICIPATED_IN]->(conv:Conversation)
  -[:INCLUDES]->(alice:User {name: "Alice"})
OPTIONAL MATCH (conv)-[:DISCUSSES]->(context)
RETURN conv, COLLECT(context) as discussed
ORDER BY conv.created_date DESC
```

**"Show me all work related to feature X"**

```cypher
MATCH (topic:Topic {name: "Feature X"})
  <-[:REFERENCES]-(doc:Document)
  <-[:HAS]-(project:Project)
  
OPTIONAL MATCH (project)-[:CONTAINS]->(task:Task)
OPTIONAL MATCH (doc)-[:MENTIONED_IN]->(conv:Conversation)

RETURN {
  projects: [project],
  documents: [doc],
  tasks: COLLECT(task),
  conversations: COLLECT(conv)
}
```

---

## Indexing Strategy

### Full-Text Indexes

```
Document.content_summary (for "show me docs about X")
Topic.description (for "what do we know about X")
Conversation.summary (for "what did we discuss about X")
Contact.name (for "who is X")
```

### Graph Indexes

```
User.id (quick lookup)
Project.name (popular query)
Document.type (filter by doc type)
Task.status (common filter)
Topic.category (common filter)
```

### Time Indexes

```
Document.created_date (time range queries)
Task.due_date (deadline queries)
Conversation.created_date (historical queries)
```

---

## Graph Population Strategy

### Automated Population

Every action creates graph nodes and edges:

```
User creates Document
  → Create Document node
  → Create CREATED_BY edge (User → Document)
  → Analyze content_summary
  → Extract mentioned Contacts
  → Create MENTIONS edges
  → Extract referenced Topics
  → Create REFERENCES edges

User assigns Task
  → Create/update Task node
  → Create ASSIGNED_TO edge
  → Link to Project
  → Update Task status timeline

User sends message in Conversation
  → Extract intent entities
  → Find related Projects/Documents/Tasks
  → Create DISCUSSES edges
  → Extract topics mentioned
  → Create MENTIONS edges
```

### Diana's Graph Building

After each conversation turn, Diana:

```
1. Extract topics discussed
2. Extract people mentioned
3. Extract projects referenced
4. Extract decisions made
5. Extract action items created

Create nodes + edges for each
```

### Graph Enrichment

Background jobs enrich the graph:

```
Daily (off-peak):
  - Compute Document importance scores
  - Update Topic similarity scores
  - Clean up dead relationships
  - Archive old conversations

Weekly:
  - Recompute project statistics
  - Summarize topic clusters
  - Generate usage analytics
  - Find cross-team connections

Monthly:
  - Deep clean (consistency checks)
  - Archive completed projects
  - Summarize quarterly themes
  - Generate leadership reports
```

---

## Privacy & Access Control

### Node-Level Permissions

```
Document node has:
  - owner: User
  - visibility: enum ("private", "shared", "public")
  - sharedWith: User[]

Graph queries respect permissions:
  MATCH (doc:Document)
  WHERE doc.owner = $userId
     OR $userId IN doc.sharedWith
     OR doc.visibility = "public"
```

### No Cross-Organization Leakage

```
Query must include:
  MATCH (...)-[:IN_ORGANIZATION]->(org:Organization {id: $orgId})

Organization boundary enforced at every query
```

### Data Minimization

Only users with need-to-know can see:
- Document content
- Private conversations
- Sensitive topics
- Personal information

---

## Performance Considerations

### Query Optimization

Most queries follow patterns:

```
User → Workspace → Project → Document/Task
User → Conversation → Discusses → Project
Document → References → Topic
Task → Assigned_To → User
```

Pre-computed views for common paths:

```
UserProjectsView (denormalized)
UserTasksView (denormalized)
ProjectDocumentsView (denormalized)
```

### Scalability

Graph can scale to:
- 1M users
- 10M documents
- 100M tasks
- 1B relationships

Via:
- Graph sharding (by workspace)
- Query time limits (5s timeout)
- Result limits (100 nodes max per query)
- Caching (1-hour TTL for popular queries)

### Latency Targets

- Simple lookup (find Project by name): < 50ms
- Complex query (show all work related to X): < 200ms
- Graph traversal (3 hops): < 500ms

---

## Example: Complete Graph for Startup Workspace

```
Workspace: "My AI Startup"
│
├─ Project: "Product Development"
│  ├─ Document: "Product Roadmap"
│  │  └─ References:
│  │     ├─ Topic: "Feature Prioritization"
│  │     ├─ Topic: "AI Model Architecture"
│  │     └─ Topic: "Enterprise Features"
│  │
│  ├─ Document: "Technical Specification"
│  │  └─ References: [Same topics]
│  │
│  └─ Tasks:
│     ├─ "Build MVP" (in_progress, assigned to Alice)
│     ├─ "Design API" (todo, assigned to Bob, blocks "Build MVP")
│     └─ "Write tests" (blocked by "Build MVP")
│
├─ Project: "Go-to-Market"
│  ├─ Document: "GTM Strategy"
│  ├─ Document: "Customer Research"
│  └─ Tasks:
│     ├─ "Identify first 10 customers" (todo)
│     ├─ "Create pitch deck" (todo)
│     └─ "Schedule founder calls" (in_progress)
│
├─ Conversations:
│  ├─ Conversation (Jul 4): Discusses Product Development + Design
│  ├─ Conversation (Jul 5): Discusses Go-to-Market + Customer Research
│  └─ Conversation (Jul 6): Discusses both projects
│
├─ Contacts:
│  ├─ Alice (engineer, alice@ai.startup)
│  ├─ Bob (designer, bob@ai.startup)
│  ├─ Sarah (investor, sarah@vc.com)
│  └─ John (advisor, john@advisor.com)
│
└─ Knowledge Base:
   ├─ Topic: "AI Model Architecture" (links to Feature Prioritization)
   ├─ Topic: "Enterprise Features" (links to GTM Strategy)
   └─ Topic: "Customer Segments" (used in Customer Research)
```

When Diana is asked "What's blocking us?" she queries:

```cypher
MATCH (task:Task {status: "blocked"})
  <-[:BLOCKS]-(blocker:Task)
  -[:IN_PROJECT]->(project:Project)
WHERE project.workspace = $workspaceId

RETURN project, blocker, task, task.assigned_to as assigned_to
```

Result: "The MVP is blocked by API design (assigned to Bob). The pitch deck is blocked by customer research."

---

## The Power

With a fully populated Knowledge Graph, Diana can:

- **Answer questions**: "Show me all work related to X"
- **Make connections**: "This document relates to your conversation from Tuesday"
- **Suggest actions**: "Based on your roadmap, you should start task X"
- **Identify bottlenecks**: "Alice is assigned to 5 tasks and 2 are overdue"
- **Surface context**: "This task relates to the customer research from last month"
- **Predict next steps**: "Based on project status, you'll need to schedule the launch meeting"

This is what makes Diana not just a chatbot, but a **work operating system**.

---

## Implementation Phases

### Phase 1 (Sprint 2-3): Foundation
- PostgreSQL storage (normalized data)
- Basic relationships (User → Project, Project → Task, etc.)
- Memory as graph nodes

### Phase 2 (Sprint 4-5): Graph Database
- Add Neo4j (parallel to PostgreSQL)
- Migrate relationships to graph
- Implement caching layer

### Phase 3 (Sprint 6-7): Rich Queries
- Full-text search integration
- Complex query optimization
- Diana query interface

### Phase 4 (Sprint 8+): Intelligence
- Automatic enrichment jobs
- Importance scoring
- Predictive suggestions
- Advanced analytics

---

## This is the Moat

Every AI company can build a chatbot.

Few can build a **knowledge graph** that understands your entire work context.

That's AIGINVEST.

