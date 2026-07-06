# AIG-201: Mission Engine MVP

**The First Complete Mission: Prove the AI-First Model Works**

**Date:** 2026-07-06  
**Timeline:** Sprint 2 (starting Monday) - Complete by Friday  
**Developers:** 1 lead (Mission Engine architecture) + 2 supporting (UI + testing)  
**Status:** Ready to start Monday  

---

## Objective

Deliver the first complete end-to-end mission experience where a user:

1. Onboards
2. Tells Diana what they want to accomplish
3. Diana creates a complete, functional workspace
4. User completes a measurable milestone
5. Diana recognizes and celebrates completion

**Success = One complete mission experience, polished, working, demonstrable.**

---

## The Scenario We're Building Toward

```
MONDAY 9am: Developer starts coding

FRIDAY 5pm: This is what works:

USER ONBOARDS
├─ Account created
├─ Logs into chat
└─ Sees Diana

USER: "I want to start a business"

DIANA:
"Great! Let me prepare your startup workspace...

[2-second loading]

✓ Created workspace
✓ Generated business plan
✓ Created financial model
✓ Setup roadmap
✓ Created task timeline
✓ Initialized memory

Everything is ready!

Phase 1: Ideation & Validation

I recommend you:
1. Write your problem statement
2. Interview 5 potential customers
3. Research your top 3 competitors

What sounds good?"

USER: Types problem statement

WORKSPACE UPDATES IN REAL-TIME
├─ Document appears in sidebar
├─ Changes auto-save
├─ Diana sees it
└─ Task board updates

DIANA:
"I see you've validated your problem. Next:
- Schedule interviews with your target customers
- I'll create an interview guide

Ready to proceed?"

USER: "Yes"

DIANA:
"Perfect! I've created:
✓ Interview guide
✓ Interview task (due in 3 days)
✓ Research notes template

Go talk to your customers."

FRIDAY 5pm DEMO:
User signs up → talks to Diana → 
entire startup workspace appears → 
user completes first milestone → 
Diana celebrates
```

---

## Scope: What's Included

### Core Mission Engine

**Intent Recognition**
- Detect when input is a mission ("I want to...")
- Classify into mission category
- Match against templates
- Confidence scoring

**Mission Selection**
- Suggest 3 matching missions
- User picks one
- Confirm choice

**Workspace Assembly**
- Create project
- Generate documents (from templates)
- Create tasks (phase 1 only)
- Initialize memory profile
- Setup real-time sync

**Diana Guidance**
- Phase-specific coaching messages
- Suggest next steps
- Celebrate milestones

### One Complete Mission Template

**Focus: "Start a Business" (simplified MVP)**

Documents (simplified):
- Business Plan (outline only, not full 8 sections)
- Financial Model (simple projections)
- Roadmap (high-level)

Tasks (Phase 1 only):
- [ ] Write problem statement
- [ ] Research market
- [ ] Interview potential customers

Diana Guidance:
- Intro: Welcome to startup mission
- Phase 1: Validation phase
- Completion: You've validated the problem!

### User Experience

**Onboarding Flow**
1. User signs up
2. Chat opens with Diana
3. Diana: "What would you like to accomplish?"
4. User describes goal
5. Diana suggests missions
6. User picks "Start a Business"
7. Workspace loads
8. Everything appears

**First Session**
1. User sees business plan
2. User sees roadmap
3. User sees task list
4. Diana suggests: "Start with problem statement"
5. User fills it in
6. Diana acknowledges completion

---

## Scope: What's NOT Included

### NOT in MVP
- Other 4 mission templates (future)
- Advanced Diana intelligence
- Marketplace
- AIOS
- North Star ONE
- Team collaboration
- Advanced memory learning
- Knowledge Graph depth
- Marketplace integrations

### NOT in MVP (Features)
- Video conferencing
- Email integration
- Slack bot
- Calendar sync
- Advanced reporting

Just... Diana + missions + core.

---

## Technical Requirements

### Database

**New Prisma Models**
```typescript
model Mission {
  id                 String @id @default(cuid())
  userId             String
  user               User @relation(fields: [userId], references: [id])
  
  // Mission data
  missionTemplateId  String
  missionTemplate    MissionTemplate @relation(...)
  
  // Instance
  name               String
  status             String // ACTIVE, COMPLETED, ARCHIVED
  phase              String // ideation, validation, build, launch, etc.
  progress           Int // 0-100
  
  // Workspace
  workspaceId        String?
  workspace          Workspace? @relation(...)
  
  // Relationships
  documents          Document[]
  tasks              ProjectTask[]
  memories           DianaMemory[]
  
  createdAt          DateTime @default(now())
  updatedAt          DateTime @updatedAt
}

model MissionTemplate {
  id                 String @id @default(cuid())
  name               String
  slug               String @unique // "start-business"
  description        String
  
  // Template structure
  documents          TemplateDocument[]
  taskTemplates      MissionTaskTemplate[]
  phases             MissionPhase[]
  
  // Metadata
  estimatedDuration  Int // days
  
  createdAt          DateTime @default(now())
}

model MissionPhase {
  id                 String @id @default(cuid())
  missionTemplateId  String
  missionTemplate    MissionTemplate @relation(...)
  
  name               String // "ideation", "validation", etc.
  order              Int
  guidanceMessages   String[] // Diana's coaching for this phase
  
  createdAt          DateTime @default(now())
}
```

### Services

**MissionService**
```typescript
// Recognize mission from user input
suggestMissions(input: string): Promise<MissionSuggestion[]>

// Create mission + assemble workspace
launchMission(userId: string, templateId: string): Promise<Mission>

// Update phase when complete
advancePhase(missionId: string): Promise<void>

// Mark mission complete
completeMission(missionId: string): Promise<void>

// Get mission progress
getMissionProgress(missionId: string): Promise<MissionProgress>
```

**WorkspaceOrchestratorService** (existing, enhanced)
```typescript
// Already exists - used by Mission Engine
createWorkspace(config: WorkspaceConfig): Promise<Workspace>
```

### API Endpoints

```
POST /api/missions/suggest
  Input: { input: string }
  Output: { suggestions: MissionSuggestion[] }
  Purpose: Diana suggests 3 missions based on user input

POST /api/missions/launch
  Input: { templateId: string }
  Output: { mission: Mission, workspace: Workspace }
  Purpose: User picks a mission, workspace assembles

GET /api/missions/:id/progress
  Output: { mission: Mission, progress: 0-100 }
  Purpose: Track mission progress

PATCH /api/missions/:id/advance-phase
  Purpose: Move to next phase

POST /api/missions/:id/complete
  Purpose: Mark mission as complete
```

### Frontend

**Chat Integration**
```
User types: "I want to start a business"
  ↓
ChatController detects mission input
  ↓
Calls MissionService.suggestMissions()
  ↓
Returns suggestions: ["Start Business", "Learn Something", "Manage Biz"]
  ↓
Chat displays: "I found 3 missions for you. Pick one:"
  ↓
User clicks "Start Business"
  ↓
Calls /api/missions/launch
  ↓
Workspace appears in sidebar
  ↓
Documents + tasks load
  ↓
Diana: "Everything is ready!"
```

---

## Acceptance Criteria

### Intent Recognition (AIG-201-1)
```
✓ Detect "I want to..." inputs
✓ Classify as mission (not question)
✓ Match against templates
✓ Return top 3 suggestions
✓ Confidence scoring > 0.7
✓ E2E test: 10 different inputs
```

### Mission Selection (AIG-201-2)
```
✓ User can pick from 3 suggestions
✓ Selection UI works cleanly
✓ No errors during selection
✓ Fast (<500ms) response
✓ E2E test: Select mission → workspace loads
```

### Workspace Assembly (AIG-201-3)
```
✓ Workspace created in <2 seconds
✓ Project created with mission name
✓ Business Plan document created
✓ Financial Model created
✓ Roadmap created
✓ Task board created with Phase 1 tasks
✓ Memory profile initialized
✓ All created resources appear in sidebar
✓ E2E test: Complete assembly + sidebar update
```

### Diana Guidance (AIG-201-4)
```
✓ Diana sends welcome message
✓ Diana describes what was created
✓ Diana suggests Phase 1 steps
✓ Messages are contextual (mention mission)
✓ Diana tone is encouraging
✓ E2E test: Chat flow from mission selection to guidance
```

### Real-Time Sync (AIG-201-5)
```
✓ User edits document
✓ Change appears in real-time
✓ Diana sees the change
✓ Task status updates live
✓ Multiple tabs stay in sync
✓ E2E test: Open 2 windows, edit in one, verify other updates
```

### Phase Advancement (AIG-201-6)
```
✓ User completes Phase 1 tasks
✓ Diana detects completion
✓ Diana: "Great! Moving to Phase 2"
✓ Phase 2 tasks appear
✓ Diana sends Phase 2 guidance
✓ E2E test: Complete phase → advance → see phase 2
```

### Error Handling (AIG-201-7)
```
✓ Network errors handled gracefully
✓ Invalid mission input handled
✓ Workspace creation failure handled
✓ User sees meaningful errors, not crashes
✓ E2E test: Simulate failures, verify recovery
```

---

## Testing Requirements

### Unit Tests (Minimum 20 tests)
```
✓ MissionService.suggestMissions()
  - Valid input → 3 suggestions
  - Ambiguous input → lower confidence
  - Invalid input → error
  - Empty input → error

✓ MissionService.launchMission()
  - Valid template → workspace created
  - Invalid template → error
  - Missing user → error

✓ WorkspaceOrchestratorService.createWorkspace()
  - All documents created
  - All tasks created
  - Memory initialized
  - Event sent

✓ Intent detection
  - "I want to..." → mission
  - "How do I...?" → question
  - "Tell me..." → question
  - Edge cases handled
```

### E2E Tests (Minimum 5 scenarios)

**Scenario 1: Full Mission Launch**
```
1. User onboards
2. Sends "I want to build a startup"
3. Sees 3 mission suggestions
4. Clicks "Start a Business"
5. Workspace loads
6. Sidebar shows all documents + tasks
7. Diana sends guidance
8. Chat shows success
✓ Assert: 0 errors, proper state
```

**Scenario 2: Mission Completion**
```
1. User completes Phase 1 tasks
2. Checks off all items
3. Diana detects completion
4. Phase 2 loads
5. Diana sends Phase 2 guidance
6. Progress shows > 30%
✓ Assert: State transition correct
```

**Scenario 3: Real-Time Sync**
```
1. Open workspace in 2 tabs
2. Edit document in Tab 1
3. Change appears in Tab 2 in <500ms
4. Task update in Tab 1 reflected in Tab 2
✓ Assert: < 500ms sync latency
```

**Scenario 4: Error Recovery**
```
1. Network fails during mission launch
2. User sees error message
3. User retries
4. Succeeds on retry
✓ Assert: No corrupted state
```

**Scenario 5: Mission Context Awareness**
```
1. Launch "Start Business" mission
2. Business Plan is mission-specific
3. Diana's messages mention "startup"
4. Tasks relate to business launch
✓ Assert: Context flows through
```

---

## Implementation Steps

### Step 1: Database (Monday morning)
- Create Mission, MissionTemplate, MissionPhase models
- Create 1 MissionTemplate ("start-business")
- Populate Phase 1 with tasks

Estimated: 2 hours

### Step 2: MissionService (Monday afternoon)
- Implement suggestMissions()
- Implement launchMission()
- Implement getMissionProgress()
- Implement advancePhase()

Estimated: 4 hours

### Step 3: MissionController + Routes (Tuesday morning)
- POST /api/missions/suggest
- POST /api/missions/launch
- GET /api/missions/:id/progress
- PATCH /api/missions/:id/advance-phase

Estimated: 2 hours

### Step 4: Intent Recognition (Tuesday afternoon)
- Integrate with ChatController
- Detect mission vs question
- Route to MissionService

Estimated: 2 hours

### Step 5: Frontend Chat Integration (Wednesday morning)
- Display mission suggestions
- Handle mission selection
- Show workspace loading
- Update sidebar in real-time

Estimated: 4 hours

### Step 6: Diana Guidance (Wednesday afternoon)
- Send mission welcome message
- Send phase guidance
- Celebrate completion

Estimated: 2 hours

### Step 7: Testing (Thursday)
- Unit tests
- E2E tests
- Error handling
- Manual testing

Estimated: 6 hours

### Step 8: Refinement (Friday)
- Bug fixes
- Polish
- Performance tuning
- Demo preparation

Estimated: 3 hours

**Total: 25 developer-hours (achievable in 1 week with 1-2 developers)**

---

## Success Demo (Friday 5pm)

Developer shows:

```
1. User onboards
   "What would you like to accomplish?"

2. User types:
   "I want to launch my startup"

3. Diana responds:
   "I found 3 missions for you:
   - Start a Business
   - Manage My Company
   - Learn Business Strategy
   
   Pick one:"

4. User clicks "Start a Business"

5. Workspace loads instantly
   ✓ Business Plan in sidebar
   ✓ Financial Model in sidebar
   ✓ Roadmap in sidebar
   ✓ Tasks with "Problem Validation" phase
   ✓ Diana message: "Phase 1: Validation"

6. User clicks Business Plan

7. Opens document
   "Diana is preparing your business plan...
   
   You're in Phase 1: Ideation & Validation
   
   Next steps:
   1. Write your problem statement
   2. Research your market
   3. Interview 5 customers
   
   What would you like to start with?"

8. User writes problem statement

9. Sidebar updates in real-time

10. Diana detects writing:
    "Great start! Here's your problem statement:
    [summarizes what user wrote]
    
    Next: Research your market. I'll create a research guide."

11. Diana creates research task

12. Task appears in board

13. Demo complete:
    "Entire mission experience works end-to-end.
    One user. One goal. One complete workspace.
    That's the AIGINVEST difference."
```

---

## Definition of Done

- [ ] Code merged to main branch
- [ ] All 20+ unit tests passing
- [ ] All 5 E2E tests passing
- [ ] Zero TypeScript compile errors
- [ ] Zero console errors
- [ ] Database migrations run successfully
- [ ] API endpoints tested and responding
- [ ] Frontend updates in real-time
- [ ] Diana sends appropriate guidance
- [ ] 5-minute demo works without errors
- [ ] README updated with mission engine docs
- [ ] Team can understand code structure

---

## Why This Matters

This ticket proves the entire thesis:

**Users don't start with apps. Users start with goals.**

**Diana helps them achieve those goals by creating complete environments.**

If AIG-201 works, we have proven:
- Intent recognition works
- Workspace assembly works
- Diana orchestration works
- The AI-first model works

Then we scale to 5 missions.

Then teams.

Then enterprises.

Then devices.

But this ticket is where the proof happens.

---

## Monday Morning Briefing

Developer opens ticket and sees:

✅ Clear objective (one complete mission experience)
✅ Technical specs (database models, APIs, services)
✅ Acceptance criteria (7 areas, each with specific checks)
✅ Testing requirements (20+ unit tests, 5 E2E scenarios)
✅ Implementation steps (8 clear phases)
✅ Success demo (what it should look like Friday)
✅ Definition of done (concrete checklist)

No ambiguity.

No "what about features?"

No scope creep.

Just build one complete mission experience.

Make it work.

Make it delightful.

Prove the model.

