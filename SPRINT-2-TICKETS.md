# Sprint 2 Developer Tickets

**Discipline: One Sprint = One Capability (Done Right)**

Everything else waits. No exceptions.

---

## AIG-101: Replace Mock LLM with Real AI Provider

**Epic:** Make Diana Real  
**Sprint:** 2  
**Capability:** Understand (Real AI responses)  
**Estimated:** 2 days  
**Priority:** Blocker  

### Objective

Replace the mock LLM provider with OpenAI GPT-4 while keeping the existing API unchanged.

A user asks Diana a question and gets a real AI response, not a fake one.

### Success Criteria

- [ ] OpenAI API key configurable via `.env`
- [ ] `LLMService.stream()` returns real GPT-4 responses (not mock)
- [ ] Existing chat UI works without changes
- [ ] `/api/chat/stream` endpoint returns same response format
- [ ] E2E tests still pass (17/17)
- [ ] Streaming still works (first chunk < 100ms)
- [ ] No TypeScript compilation errors
- [ ] No console errors

### Technical Details

**Reference:** [SPRINT-2-CHECKLIST.md](SPRINT-2-CHECKLIST.md#task-11-replace-mock-ai-provider-with-real-apis) Task 1.1

**Changes Required:**

1. Add OpenAI SDK to dependencies
2. Update `apps/api/src/ai/llm.service.ts`:
   - Initialize OpenAI client
   - Replace mock stream with real OpenAI stream
   - Yield actual tokens

3. Update `.env`:
   - `OPENAI_API_KEY=sk-...`
   - Keep `LLM_PROVIDER=openai`

4. Test locally:
   - Send message via chat UI
   - Verify real response appears
   - Check cost logging

### Test Acceptance

```typescript
test("LLMService.stream() returns real GPT-4 response", async () => {
  const service = new LLMService();
  const stream = service.stream([
    { role: "user", content: "What is 2+2?" }
  ]);
  
  let output = "";
  for await (const chunk of stream) {
    output += chunk.content;
  }
  
  expect(output.length).toBeGreaterThan(0);
  expect(output).toContain("4");
});
```

### Definition of Done

- [x] Code merged to main
- [x] All tests passing (17/17)
- [x] Deployed to staging
- [x] QA verified real responses
- [x] No regressions vs. mock version

### How This Feels

**Before:** Diana says "I'm thinking..." and returns a pre-written response  
**After:** Diana actually calls OpenAI and returns something real and useful

---

## AIG-102: Streaming Optimization & Cancel Button

**Epic:** Make Diana Real  
**Sprint:** 2  
**Capability:** Respond in Real-Time (Stream < 100ms latency)  
**Estimated:** 2 days  
**Depends On:** AIG-101  
**Priority:** High  

### Objective

Perfect the streaming experience so responses feel instant.

User clicks send → first token appears in < 100ms.  
User clicks Stop → generation stops immediately.

### Success Criteria

- [ ] First chunk latency < 100ms (p95)
- [ ] No stalled streams (> 1 second gap)
- [ ] Stop button aborts immediately
- [ ] Partial response saved (don't lose generated content)
- [ ] Can send new message immediately after Stop
- [ ] Frontend console has zero errors
- [ ] E2E test for streaming latency

### Technical Details

**Reference:** [SPRINT-2-CHECKLIST.md](SPRINT-2-CHECKLIST.md#task-21-perfect-sse-streaming--100ms-latency) Task 2.1 & 2.2

**Changes Required:**

1. Backend optimization:
   - Verify SSE headers set correctly (no buffering)
   - Remove any delays in chunk streaming
   - Ensure AbortSignal properly stops generation

2. Frontend optimization:
   - Measure time from fetch to first token
   - Implement AbortController properly
   - Show Stop button only during generation

3. Performance testing:
   - Benchmark with DevTools Network tab
   - Measure in production-like conditions
   - Document latency metrics

### Test Acceptance

```typescript
test("First chunk arrives in < 100ms", async () => {
  const start = performance.now();
  const stream = streamMessage("Hello");
  
  let firstChunk = null;
  for await (const chunk of stream) {
    firstChunk = chunk;
    break;
  }
  
  const latency = performance.now() - start;
  expect(latency).toBeLessThan(100);
});

test("Stop button aborts stream immediately", async () => {
  const abort = new AbortController();
  setTimeout(() => abort.abort(), 50);
  
  let chunkCount = 0;
  try {
    for await (const chunk of streamMessage("...", abort.signal)) {
      chunkCount++;
    }
  } catch (e) {
    expect(e.message).toContain("aborted");
  }
  
  expect(chunkCount).toBeLessThan(10); // Should stop quickly
});
```

### Definition of Done

- [x] Code merged to main
- [x] Latency benchmarks documented
- [x] E2E streaming test added
- [x] Stop button works reliably
- [x] No regressions

### How This Feels

**Before:** Response appears word-by-word over several seconds  
**After:** First word appears instantly, rest flows smoothly

---

## AIG-103: Long-Term Memory

**Epic:** Make Diana Real  
**Sprint:** 2  
**Capability:** Remember (User context persists)  
**Estimated:** 3 days  
**Depends On:** AIG-101  
**Priority:** High  

### Objective

Diana learns about the user and remembers across conversations.

First conversation:
> User: "I'm building a SaaS company"

Next day, Diana says:
> "Yesterday you were building a SaaS company. How's it going?"

### Success Criteria

- [ ] After each message, extract facts (name, company, role, goals)
- [ ] Facts stored to `DianaMemory` table with confidence scores
- [ ] When building prompts, load user's memories
- [ ] Memories appear in Diana's responses (personalized)
- [ ] Memory extraction non-blocking (no latency impact)
- [ ] E2E test: User context recalled across conversations
- [ ] No TypeScript errors

### Technical Details

**Reference:** [SPRINT-2-CHECKLIST.md](SPRINT-2-CHECKLIST.md#task-15-memory-extraction-from-conversations) Task 1.5 & 1.6

**Changes Required:**

1. Implement `extractAndSaveMemories()` in ContextEngine:
   - Regex patterns for: name, company, role, goals, preferences
   - Confidence scoring (0.0-1.0)
   - Upsert to DianaMemory table

2. Update `buildMessages()` to include memories:
   - Load top 5 memories by relevance
   - Include in system prompt
   - Make Diana aware of context

3. Test in UI:
   - Send: "I'm Alice building at Acme"
   - Later: "Who am I?"
   - Verify Diana responds with remembered info

### Test Acceptance

```typescript
test("Extracts and recalls user name", async () => {
  await sendMessage("Hi, I'm Alice and I work at Acme");
  
  // Check DianaMemory table
  const memories = await prisma.dianaMemory.findMany({
    where: { userId, type: "fact", key: "name" }
  });
  
  expect(memories[0].value).toBe("Alice");
  expect(memories[0].confidence).toBeGreaterThan(0.8);
});

test("Uses memories in responses", async () => {
  await sendMessage("My name is Alice");
  
  const response = await sendMessage("Who am I?");
  expect(response.content).toContain("Alice");
});
```

### Definition of Done

- [x] Code merged to main
- [x] Memory extraction working
- [x] Memory recall in responses verified
- [x] E2E tests passing
- [x] No console errors

### How This Feels

**Before:** Diana treats every conversation like the first time  
**After:** Diana knows the user's context and references it naturally

---

## AIG-104: Project Creation Tool

**Epic:** Make Diana Real  
**Sprint:** 2  
**Capability:** Act (Actually create resources)  
**Estimated:** 2 days  
**Depends On:** AIG-101  
**Priority:** High  

### Objective

When user says "Create a project," Diana actually creates it.

User:
> "Create a project called Q3 Planning"

Diana:
> "✓ Created project 'Q3 Planning'. Would you like to add team members?"

Project appears in sidebar immediately (real-time sync).

### Success Criteria

- [ ] Intent detection: "create project" understood
- [ ] Projects API called correctly
- [ ] Project created in database
- [ ] Response confirms creation with project name
- [ ] Sidebar updates in real-time (via Event Bus)
- [ ] E2E test: Create project via chat
- [ ] Tool calling framework tested

### Technical Details

**Reference:** [SPRINT-2-CHECKLIST.md](SPRINT-2-CHECKLIST.md#task-14-error-handling--retries) (existing structure)

**Changes Required:**

1. Intent detection in ChatController:
   - Add pattern: "create.*project"
   - Extract project name via LLM tool calling

2. Call ProjectService:
   - `createProject(name, userId, organizationId)`
   - Get back projectId

3. Emit success event:
   - Publish to Event Bus: `project.created`
   - Subscribers (sidebar) update in real-time

4. Diana's response:
   - "✓ Created project 'Q3 Planning'"
   - Proactive suggestion: "Would you like to..."

### Test Acceptance

```typescript
test("Create project via Diana chat", async () => {
  const response = await sendMessage("Create a project called Q3 Planning");
  
  expect(response.content).toContain("Created");
  expect(response.content).toContain("Q3 Planning");
  
  // Verify in database
  const project = await prisma.project.findFirst({
    where: { name: "Q3 Planning" }
  });
  
  expect(project).toBeTruthy();
});
```

### Definition of Done

- [x] Code merged to main
- [x] Project actually created in DB
- [x] Sidebar shows new project
- [x] E2E test passing
- [x] No regressions

### How This Feels

**Before:** Diana says "I'll create a project" but nothing happens  
**After:** Diana creates it and you see it appear instantly

---

## AIG-105: Proactive Guidance

**Epic:** Make Diana Real  
**Sprint:** 3  
**Capability:** Guide (Next steps suggested)  
**Estimated:** 2 days  
**Depends On:** AIG-101, AIG-104  
**Priority:** Medium  

### Objective

After an action, Diana suggests the next step.

Not waiting for the user to ask what to do next.

### Examples

After creating project:
> "Q3 Planning created. Would you like to add team members or start planning tasks?"

After first message:
> "I can help you build your company. Should we start with a business plan or project roadmap?"

### Success Criteria

- [ ] After project creation, offer next steps
- [ ] After document creation, suggest related actions
- [ ] Suggestions feel natural (not pushy)
- [ ] Users can ignore suggestions and ask for something else
- [ ] E2E test: Guidance suggestions appear

### How This Feels

**Before:** User creates project → Diana stops, waits for next command  
**After:** User creates project → Diana suggests what to do next → User feels guided

---

## Implementation Order

1. **AIG-101** (2 days) → Real AI provider
2. **AIG-102** (2 days) → Streaming perfect
3. **AIG-103** (3 days) → Long-term memory
4. **AIG-104** (2 days) → Project creation tool
5. **AIG-105** (2 days, Sprint 3) → Proactive guidance

**Total: ~11 days = 2 weeks** ✅

Each ticket is:
- ✅ Completely defined (no ambiguity)
- ✅ Implementable in 2-3 days
- ✅ Testable with E2E tests
- ✅ Makes Diana visibly better
- ✅ Can be demoed to stakeholders

---

## The Rule Going Forward

**One sprint = One ticket = One capability**

Not:
- ❌ Five features half-done
- ❌ Ambiguous requirements
- ❌ Code that might work
- ❌ "We'll test later"

But:
- ✅ One thing done perfectly
- ✅ Crystal clear requirements
- ✅ 100% test coverage
- ✅ Deployable immediately
- ✅ Demonstrable to anyone

This is how we build products people love.

---

## Next Steps

1. Developer picks up **AIG-101**
2. Implements to spec
3. All tests pass
4. Deployed Friday
5. Demo on Monday
6. Move to **AIG-102**

That's it.

No planning meetings about future sprints.

No "what if we also add...?"

Just:

> What's the one thing that makes Diana better this week?

Build that.

Ship that.

Move to the next one.

