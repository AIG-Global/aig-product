# STEP FORWARD #1: Diana Creates Workspace

**The First Real Diana Action: From Talk to Do**

**Date:** 2026-07-06  
**Timeline:** Sprint 2, Week 1 (Mon-Fri, Day 1-2 priority)  
**Developers:** 1 lead + 1 supporting  
**Status:** Ready to start immediately  

---

## The Promise We're Making

AIGINVEST's core promise:

> **Users describe outcomes. Diana assembles the tools.**

This ticket proves that promise.

---

## The User Experience

```
USER SIGNS IN
  ↓
DIANA: "Welcome to AIGINVEST. I'm Diana.
        What would you like to accomplish today?"
  ↓
USER: "I want to start an AI company"
  ↓
DIANA EXECUTES (instantly):
  ✓ Workspace created
  ✓ Project created
  ✓ README created
  ✓ First document created
  ✓ Task board created
  ✓ Conversation linked
  ✓ Memory initialized
  ↓
DIANA: "Everything is ready. 
        Let's start with your business vision."
```

That's it.

That's the demo.

That's the proof of concept.

---

## Technical Flow

```
User Input: "I want to start an AI company"
  ↓
MISSION CLASSIFIER
  - Detect: Mission (not question)
  - Classify: Startup goal
  - Confidence: 0.92
  ↓
WORKSPACE ORCHESTRATOR
  - Create workspace
  - Create project with user's goal as name
  - Initialize default folder structure
  ↓
DOCUMENT SERVICE
  - Create README template with goal
  - Title: "My AI Company"
  - Content: "Starting an AI company"
  ↓
TASK SERVICE
  - Create task board
  - Add 3 initial tasks:
    1. Define your mission
    2. Research the market
    3. Validate with customers
  ↓
MEMORY SERVICE
  - Store: user_goal, date_created, workspace_id
  - Extract: "AI company", "startup"
  - Confidence: 0.90
  ↓
LINK EVERYTHING
  - Connect workspace to conversation
  - Update sidebar real-time
  - Diana responds with confirmation
```

**No branches, no conditionals, no complexity.**

Just: Goal → Workspace.

---

## Scope: What's Included

### Completely Included ✅

1. **Input Detection**
   - Parse user goal from chat
   - Detect mission vs question
   - Extract domain/category

2. **Workspace Creation**
   - Create Workspace record
   - Create Project record
   - Create default folders (if applicable)

3. **README Document**
   - Create initial document
   - Title: User's goal (or templated version)
   - Content: Templated intro based on goal type
   - Link to workspace

4. **Task Board**
   - Create 3 initial tasks (generic)
   - No dependencies
   - Due dates: 1 week, 2 weeks, 3 weeks

5. **Memory Initialization**
   - Store: goal, created_date, user_context
   - Extract entities (company type, domain)
   - Confidence scoring

6. **Real-Time Updates**
   - Sidebar updates with new workspace
   - Chat shows confirmation
   - All in < 2 seconds

7. **Diana Response**
   - Confirm what was created
   - Suggest next step
   - Tone: Encouraging, clear

### Explicitly NOT Included ❌

- Mission templates (just generic tasks)
- Document generation (just README)
- AI content writing (just templates)
- Advanced memory learning
- Knowledge Graph integration
- Marketplace
- AIOS
- Team features
- Advanced analytics

**Goal:** Prove the model works. Not build the full feature set.

---

## Acceptance Criteria

### 1. Input Recognition
```
✓ User types goal starting with "I want to"
✓ System detects as mission (not question)
✓ Confidence > 0.80
✓ Extract: Type (startup, learning, business, writing, personal)
✓ E2E Test: 10 different goal inputs
```

### 2. Workspace Creation
```
✓ Workspace created in database < 500ms
✓ Project created with user's goal as name
✓ Workspace linked to user
✓ All relationships set correctly
✓ E2E Test: Workspace appears in sidebar immediately
```

### 3. Documents
```
✓ README document created
✓ Title: "My [Goal]" or user-provided name
✓ Content: Templated intro (4-5 sentences)
✓ Linked to workspace
✓ User can open and edit immediately
✓ E2E Test: Click README → opens in editor
```

### 4. Tasks
```
✓ Task board created
✓ 3 initial tasks added (generic)
✓ Tasks shown in sidebar
✓ Tasks editable immediately
✓ No errors in task creation
✓ E2E Test: See tasks in board, create new task works
```

### 5. Memory
```
✓ DianaMemory record created
✓ Goal stored with metadata
✓ Entities extracted (type, domain, confidence)
✓ Memory searchable by user_id
✓ E2E Test: Memory accessible in future conversations
```

### 6. Real-Time Updates
```
✓ Sidebar updates < 500ms
✓ Chat shows confirmation message
✓ No manual refresh required
✓ Multiple tabs stay in sync
✓ E2E Test: Open 2 tabs, create workspace in one, see in other
```

### 7. Error Handling
```
✓ Invalid input → Clear error message
✓ Database failure → Graceful recovery
✓ Network timeout → Retry logic
✓ User sees meaningful errors, not crashes
✓ E2E Test: Simulate failures, verify recovery
```

### 8. Performance
```
✓ Workspace creation: < 2 seconds end-to-end
✓ Chat response: < 500ms
✓ Database queries: < 100ms each
✓ No UI blocking
✓ Performance Test: Verify timing
```

---

## Implementation Steps

### Step 1: Intent Detection (Day 1, 1 hour)
- Update ChatController to detect "I want to..."
- Extract goal from message
- Classify mission type (heuristic OK for MVP)
- Route to WorkspaceService

### Step 2: Workspace Creation Service (Day 1, 2 hours)
- Implement WorkspaceService.createFromGoal()
- Create Workspace + Project records
- Link to user
- Return workspace ID

### Step 3: Document Creation (Day 1, 1 hour)
- Generate README document
- Link to workspace
- Create default content template

### Step 4: Task Board (Day 1, 1 hour)
- Create 3 initial tasks
- Link to project
- Set default due dates

### Step 5: Memory Storage (Day 1, 1 hour)
- Store goal in DianaMemory
- Extract entities
- Confidence scoring

### Step 6: Diana Response (Day 2, 1 hour)
- Generate confirmation message
- Include what was created
- Suggest next step

### Step 7: Frontend Updates (Day 2, 1 hour)
- Sidebar real-time update
- Chat confirmation display
- Loading state during creation

### Step 8: Testing (Day 2, 2 hours)
- Unit tests: Each component
- E2E tests: Full flow
- Performance tests: Timing
- Error scenario tests

**Total: 10 developer-hours (achievable Day 1-2)**

---

## Success Demo (Friday 5pm)

Developer shows:

```
1. FRESH BROWSER (logged in user)

2. Chat window opens

3. Diana: "What would you like to accomplish today?"

4. Developer types: "I want to launch my startup"

5. [System processes]

6. SIDEBAR UPDATES
   ├─ "My Startup" appears
   ├─ README document visible
   ├─ Task board shows
   └─ All in < 2 seconds

7. Chat shows: 
   "Perfect! I've created your startup workspace:
   ✓ README
   ✓ Task board
   ✓ Project folder
   
   Everything is ready. Let's start with your
   business vision. What's your idea?"

8. Developer clicks README
   → Document opens
   → User can edit immediately

9. Developer clicks task board
   → Shows 3 tasks
   → Can mark complete, add new tasks

10. Demo Complete
    "One user. One goal. 
     Entire workspace appears instantly.
     That's Diana in action."
```

---

## Definition of Done

```
✓ Code merged to main
✓ 15+ unit tests passing
✓ 3 E2E tests passing (intent, creation, feedback)
✓ Zero TypeScript errors
✓ Zero console errors
✓ No performance regressions
✓ Sidebar updates in real-time
✓ Diana confirms with message
✓ 2-minute demo works without errors
✓ README updated
✓ Code review approved
✓ Tested in prod-like environment
```

---

## Why This First?

Because it **proves the thesis**:

- ✅ Users describe outcomes
- ✅ Diana understands
- ✅ System assembles complete workspace
- ✅ No manual setup required
- ✅ User immediately productive

Everything else builds on this foundation.

---

## What Comes Next

Only AFTER this is complete:

### Phase 2: Real Responses
- Replace keyword classifier with real AI
- Diana gives intelligent suggestions

### Phase 3: Streaming
- Long-form responses
- Streaming SSE < 100ms

### Phase 4: Memory Learning
- Extract facts from conversations
- Improve suggestions over time

### Phase 5: Content Generation
- Generate meaningful documents
- Custom content per goal type

### Phase 6: Mission Templates
- Startup template (full)
- Learning template (full)
- Business template (full)
- etc.

But not until Step Forward #1 is rock solid.

---

## The New Operating Principle

**Starting TODAY, adopt this rule for every sprint:**

> **Every sprint must end with ONE capability that you can demonstrate in under 2 minutes.**

### Examples of Valid Demos

✅ "User says goal → workspace appears" (this sprint)  
✅ "Diana gives personalized advice" (future)  
✅ "Document auto-generates from goal" (future)  
✅ "Diana detects project blocks" (future)  
✅ "Real-time team collaboration" (future)  

### Examples of Invalid Demos

❌ "New database schema that enables..."  
❌ "Backend refactoring that makes..."  
❌ "Infrastructure that will later support..."  
❌ "Feature 80% complete, finishing next sprint"  

### Why This Works

**2-minute demo rule ensures:**
1. Features are **complete, not partial**
2. Product is **always usable**
3. Team stays **focused on user experience**
4. Progress is **always visible**
5. Momentum is **maintained**

---

## One More Thing

This sprint, build STEP FORWARD #1.

Next sprint, build STEP FORWARD #2 (real Diana).

Sprint after, build STEP FORWARD #3 (streaming).

Each one a complete, demonstrable capability.

Each one delivers user value immediately.

No "foundation building" with no user experience.

No "infrastructure work" that users can't see.

**Every commit brings us closer to a product people can use.**

---

## Ready to Start Monday?

Developer opens this ticket and sees:

✅ **Clear scope:** Just create workspace  
✅ **Technical steps:** 8 phases  
✅ **Acceptance criteria:** 8 areas, concrete  
✅ **Testing:** Unit + E2E  
✅ **Success demo:** What Friday shows  
✅ **Definition of done:** 12-point checklist  

No ambiguity.

No scope creep.

No vague requirements.

Just: Build one capability that works.

---

## Why "Step Forward"?

We're done with strategy.

We're done with architecture.

We're done with planning.

Now we take steps forward.

Each sprint, one step.

Each step, one complete capability.

Each capability, demonstrable and valuable.

This is how we build AIGINVEST.

One executable milestone at a time.

