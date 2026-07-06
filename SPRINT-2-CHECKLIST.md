# Sprint 2 Implementation Checklist

**Week 1-2: Diana Sprint (Real AI, Memory, Streaming, Cancel/Retry)**

**Reference:** AIG Master Product Design, Part VI (Ask Diana) + Part VIII (Core Services: AI Router, Memory Service)

---

## Week 1: Real AI Provider Integration

### Monday-Wednesday: OpenAI + Anthropic Integration

#### Task 1.1: Replace Mock AI Provider with Real APIs

**Reference:** MASTER-DESIGN.md, Part VI (Ask Diana), Section 6.2

**Acceptance Criteria:**
- [ ] OpenAI API key can be configured via `OPENAI_API_KEY` env var
- [ ] `apps/api/src/ai/llm.service.ts` calls OpenAI SDK (not mock)
- [ ] GPT-4 model returns real responses
- [ ] Stream returns actual tokens (not fake)
- [ ] Cost calculated and logged per request

**Implementation Steps:**

1. **Install OpenAI SDK**
   ```bash
   cd apps/api
   npm install openai@latest
   ```

2. **Update `llm.service.ts`**
   - Import OpenAI client: `import OpenAI from "openai"`
   - Initialize in constructor: `this.client = new OpenAI({ apiKey: process.env.OPENAI_API_KEY })`
   - Replace mock stream with real: `this.client.chat.completions.create({ stream: true, ... })`
   - Yield actual chunks: `for await (const chunk of stream)`
   - Calculate tokens: `chunk.usage.prompt_tokens + chunk.usage.completion_tokens`

3. **Update `.env`**
   - Add: `OPENAI_API_KEY=sk-...`
   - Keep: `LLM_PROVIDER=openai` (can toggle between providers)

4. **Test Locally**
   ```bash
   npm run dev
   # Send message via chat UI
   # Verify: Response from GPT-4, costs logged
   ```

**Test Acceptance:**
```typescript
// Test: Real OpenAI returns data
test("LLMService streams from OpenAI", async () => {
  const service = new LLMService();
  const stream = service.stream([
    { role: "user", content: "What is 2+2?" }
  ]);
  
  let output = "";
  for await (const chunk of stream) {
    output += chunk.content;
  }
  
  expect(output).toContain("4");
});
```

---

#### Task 1.2: Add Anthropic as Fallback Provider

**Reference:** MASTER-DESIGN.md, Part VIII (AI Router)

**Acceptance Criteria:**
- [ ] Anthropic API key can be configured via `ANTHROPIC_API_KEY`
- [ ] `LLM_PROVIDER=anthropic` switches to Claude
- [ ] Anthropic SDK installed and working
- [ ] Streaming works from Anthropic
- [ ] Cost calculation works for Anthropic models

**Implementation Steps:**

1. **Install Anthropic SDK**
   ```bash
   npm install @anthropic-ai/sdk@latest
   ```

2. **Update `llm.service.ts`**
   - Add Anthropic import
   - Add conditional initialization: `if (provider === "anthropic") this.anthropicClient = new Anthropic(...)`
   - Add `streamAnthropipc()` method parallel to `streamOpenAI()`
   - Route in main `stream()` method

3. **Update `.env`**
   - Add: `ANTHROPIC_API_KEY=sk-ant-...`

4. **Test Locally**
   ```bash
   LLM_PROVIDER=anthropic npm run dev
   # Send message
   # Verify: Response from Claude
   ```

---

#### Task 1.3: Cost Tracking & Logging

**Reference:** MASTER-DESIGN.md, Part VIII (Analytics Service)

**Acceptance Criteria:**
- [ ] Every LLM call logs cost to Analytics Service
- [ ] Cost calculation: (prompt_tokens * rate + completion_tokens * rate)
- [ ] OpenAI costs: $0.03/1K prompt, $0.06/1K completion (GPT-4)
- [ ] Anthropic costs: $0.003/1K prompt, $0.015/1K completion
- [ ] Dashboard shows cost per user, per day, total

**Implementation Steps:**

1. **Add cost calculation**
   ```typescript
   private calculateCost(tokens: number, type: "prompt" | "completion", provider: string): number {
     const rates = {
       openai: { prompt: 0.00003, completion: 0.00006 },
       anthropic: { prompt: 0.000003, completion: 0.000015 }
     };
     return tokens * rates[provider][type];
   }
   ```

2. **Log to Analytics Service**
   ```typescript
   await this.analyticsService.logEvent({
     userId,
     event: "ai_request_completed",
     metadata: {
       provider,
       model,
       prompt_tokens: usage.prompt_tokens,
       completion_tokens: usage.completion_tokens,
       cost: totalCost
     }
   });
   ```

3. **Test in UI**
   - Send 10 messages
   - Check Analytics dashboard
   - Verify costs aggregate correctly

---

#### Task 1.4: Error Handling & Retries

**Acceptance Criteria:**
- [ ] Rate limit errors (429) retry with exponential backoff
- [ ] Timeout errors retry up to 3 times
- [ ] Quota exceeded errors surface to user
- [ ] Fallback to backup provider on failure (if configured)

**Implementation Steps:**

1. **Add retry logic**
   ```typescript
   private async retryWithBackoff(fn: () => Promise<T>, maxRetries = 3): Promise<T> {
     for (let i = 0; i < maxRetries; i++) {
       try {
         return await fn();
       } catch (error) {
         if (i === maxRetries - 1) throw error;
         const delay = Math.pow(2, i) * 1000; // 1s, 2s, 4s
         await new Promise(resolve => setTimeout(resolve, delay));
       }
     }
   }
   ```

2. **Wrap API calls**
   ```typescript
   const response = await this.retryWithBackoff(() =>
     this.client.chat.completions.create(...)
   );
   ```

---

### Thursday-Friday: Memory Extraction & Storage

#### Task 1.5: Memory Extraction from Conversations

**Reference:** MASTER-DESIGN.md, Part VIII (Memory Service), Section 8.2

**Acceptance Criteria:**
- [ ] After each user message, extract facts
- [ ] Identify: name, company, role, preferences, patterns
- [ ] Store to DianaMemory table with confidence scores
- [ ] No errors even if extraction fails (non-blocking)

**Implementation Steps:**

1. **Update `context.engine.ts`**
   - After LLM response, call `extractAndSaveMemories(userId, userMessage, response)`

2. **Implement extraction logic**
   ```typescript
   private extractMemories(userId: string, text: string): Memory[] {
     const memories: Memory[] = [];
     
     // Name extraction
     const nameMatch = text.match(/\b(i'm|my name is|i am)\s+(\w+)/i);
     if (nameMatch) {
       memories.push({
         userId,
         type: "fact",
         key: "name",
         value: nameMatch[2],
         confidence: 0.9,
         source: "extracted"
       });
     }
     
     // Company extraction
     const companyMatch = text.match(/\b(i work at|i'm at|company:|employed at)\s+([^,]+)/i);
     if (companyMatch) {
       memories.push({
         userId,
         type: "fact",
         key: "company",
         value: companyMatch[2].trim(),
         confidence: 0.85,
         source: "extracted"
       });
     }
     
     // Similar for role, preferences, patterns...
     
     return memories;
   }
   ```

3. **Save to database (upsert)**
   ```typescript
   for (const memory of memories) {
     await prisma.dianaMemory.upsert({
       where: {
         userId_type_key: {
           userId,
           type: memory.type,
           key: memory.key
         }
       },
       update: {
         value: memory.value,
         confidence: memory.confidence,
         updatedAt: new Date()
       },
       create: memory
     });
   }
   ```

4. **Test**
   - Send message: "Hi, I'm Alice from Acme, VP of Engineering"
   - Query DianaMemory table
   - Verify: name=Alice, company=Acme, role=VP of Engineering

---

#### Task 1.6: Memory Recall & Context Injection

**Reference:** MASTER-DESIGN.md, Part VI (Ask Diana), Section 6.2

**Acceptance Criteria:**
- [ ] When building LLM prompt, load user memories
- [ ] Add up to 5 most relevant memories
- [ ] Memories included in system prompt or as history
- [ ] Personalization visible in responses

**Implementation Steps:**

1. **Update `context.engine.ts` - buildMessages()**
   ```typescript
   async buildMessages(userId: string, conversationId: string): Promise<Message[]> {
     // Load conversation history
     const history = await this.loadHistory(conversationId);
     
     // Load user memories
     const memories = await this.loadMemories(userId);
     
     // Add memories to system prompt
     const systemPrompt = `
       You are Diana, an intelligent AI companion.
       
       About the user:
       ${memories.map(m => `- ${m.key}: ${m.value}`).join('\n')}
       
       Remember to personalize responses based on this information.
     `;
     
     // Return messages array with system prompt + history
     return [
       { role: "system", content: systemPrompt },
       ...history
     ];
   }
   ```

2. **Test**
   - Send: "I'm Alice from Acme"
   - Later send: "What do I do?"
   - Verify Diana responds: "You're VP of Engineering at Acme"

---

### Week 2: Streaming & Polish

#### Task 2.1: Perfect SSE Streaming (< 100ms latency)

**Reference:** MASTER-DESIGN.md, Part VI (Ask Diana), Section 6.2

**Acceptance Criteria:**
- [ ] First chunk arrives in < 100ms
- [ ] Stream never stalls for > 1 second
- [ ] No dropped chunks
- [ ] Frontend receives 100% of content
- [ ] Browser console has zero errors

**Implementation Steps:**

1. **Verify SSE headers in `chat.controller.ts`**
   ```typescript
   res.setHeader('Content-Type', 'text/event-stream');
   res.setHeader('Cache-Control', 'no-cache');
   res.setHeader('Connection', 'keep-alive');
   res.setHeader('X-Accel-Buffering', 'no'); // Disable nginx buffering
   ```

2. **Optimize streaming pipeline**
   ```typescript
   for await (const chunk of stream) {
     const eventData = JSON.stringify({
       type: 'chunk',
       content: chunk.content
     });
     res.write(`data: ${eventData}\n\n`);
     // Don't wait between chunks (send ASAP)
   }
   ```

3. **Frontend SSE parsing (verify correct)**
   ```typescript
   const reader = response.body.getReader();
   const decoder = new TextDecoder();
   
   while (true) {
     const { done, value } = await reader.read();
     if (done) break;
     
     const text = decoder.decode(value);
     const lines = text.split('\n');
     
     for (const line of lines) {
       if (line.startsWith('data: ')) {
         const data = JSON.parse(line.slice(6));
         setMessage(prev => prev + data.content);
       }
     }
   }
   ```

4. **Performance test**
   - Measure time from request to first chunk received
   - Target: < 100ms
   - Use browser DevTools Network tab

---

#### Task 2.2: Cancel Generation (Stop Button)

**Reference:** MASTER-DESIGN.md, Part VI (Ask Diana), Test Acceptance Criteria

**Acceptance Criteria:**
- [ ] Stop button visible during generation
- [ ] Clicking Stop aborts request immediately
- [ ] Partial message saved (don't lose what was generated)
- [ ] Can immediately send new message
- [ ] No errors in console

**Implementation Steps:**

1. **Backend: Handle abort signal**
   ```typescript
   async stream(userId: string, messages: Message[], signal?: AbortSignal): AsyncGenerator<LLMChunk> {
     const stream = await this.client.chat.completions.create(
       { ... },
       { signal } // Pass abort signal to OpenAI
     );
     
     for await (const chunk of stream) {
       if (signal?.aborted) {
         throw new Error('Stream aborted by user');
       }
       yield chunk;
     }
   }
   ```

2. **Frontend: AbortController**
   ```typescript
   const abortController = new AbortController();
   
   // When user clicks Stop
   const handleStop = () => {
     abortController.abort();
     setIsGenerating(false);
   };
   
   // Pass signal to fetch
   fetch('/api/chat/stream', {
     signal: abortController.signal,
     ...
   });
   ```

3. **UI Updates**
   - Show Stop button only during generation
   - Disable message input during generation
   - Enable both after generation completes or aborts

4. **Test**
   - Send long message (e.g., "write a 1000-word essay")
   - Click Stop after 2-3 seconds
   - Verify: Stops immediately, partial text saved

---

#### Task 2.3: Retry Generation

**Acceptance Criteria:**
- [ ] Retry button appears after generation completes
- [ ] Clicking Retry resends same user message
- [ ] Generates new response from Diana
- [ ] Previous response hidden (or marked as old attempt)
- [ ] Retry can be used multiple times

**Implementation Steps:**

1. **UI State**
   ```typescript
   const [messages, setMessages] = useState([...]);
   const [lastUserMessage, setLastUserMessage] = useState(null);
   
   const handleRetry = async () => {
     // Remove last assistant message
     setMessages(prev => prev.slice(0, -1));
     
     // Resend last user message
     await sendMessage(lastUserMessage);
   };
   ```

2. **Backend**
   - Just normal message flow
   - No special handling needed (retry = send same message again)

3. **Test**
   - Send message
   - Click Retry
   - Verify: New response generated, old one gone

---

#### Task 2.4: Full E2E Test Suite

**Acceptance Criteria:**
- [ ] All 17 existing tests pass
- [ ] New tests for: Real AI, memory, streaming, cancel, retry
- [ ] Test coverage > 80%
- [ ] All tests pass locally and in CI

**Implementation Steps:**

1. **New E2E Tests**
   ```typescript
   describe("Diana Sprint (Real AI)", () => {
     test("Receives response from real AI provider", async () => {
       const response = await sendMessage("Hello");
       expect(response.content).toBeTruthy();
       expect(response.content.length).toBeGreaterThan(0);
     });
     
     test("Extracts and recalls memories", async () => {
       await sendMessage("My name is Alice");
       const response = await sendMessage("Who am I?");
       expect(response.content).toContain("Alice");
     });
     
     test("Streams response word-by-word", async () => {
       const chunks = [];
       for await (const chunk of streamMessage("Hello")) {
         chunks.push(chunk);
       }
       expect(chunks.length).toBeGreaterThan(10); // Should have multiple chunks
     });
     
     test("Cancels generation when abort signal received", async () => {
       const abort = new AbortController();
       setTimeout(() => abort.abort(), 100);
       
       try {
         for await (const chunk of streamMessage("...", abort.signal)) {
           // Stream should stop
         }
       } catch (e) {
         expect(e.message).toContain("aborted");
       }
     });
     
     test("Retries generation with same message", async () => {
       const response1 = await sendMessage("Explain quantum physics");
       const response2 = await retryMessage();
       
       // Same input, potentially different output (randomness)
       expect(response1.content).toBeTruthy();
       expect(response2.content).toBeTruthy();
       expect(response2.content).not.toBe(response1.content); // Likely different
     });
   });
   ```

2. **Run tests**
   ```bash
   npm run test:e2e
   ```

3. **Must pass**
   - [ ] 17 original tests
   - [ ] 5 new tests for Sprint 2
   - [ ] Total: 22/22 passing

---

## Verification Checklist

After implementation, verify:

- [ ] TypeScript compiles without errors
- [ ] All E2E tests pass (22/22)
- [ ] Console has zero errors/warnings
- [ ] Real API calls work (not mock)
- [ ] Stream latency < 100ms
- [ ] Memory extraction working
- [ ] Memory recall in responses
- [ ] Cancel/Stop button works
- [ ] Retry button works
- [ ] Cost logged correctly
- [ ] Error handling for rate limits
- [ ] No memory leaks (check DevTools)

---

## Sprint 2 Definition of Done

A feature is complete when:

1. ✅ Implemented per Master Design specification
2. ✅ Unit + integration + E2E tests passing
3. ✅ No TypeScript compilation errors
4. ✅ No console errors/warnings
5. ✅ Performance requirements met
6. ✅ Security requirements met
7. ✅ Code reviewed and approved
8. ✅ Documentation updated (this file)
9. ✅ Ready for production deployment

---

## Success Criteria for Sprint 2

**Product Metrics:**
- Diana responds with real AI (not mock) ✅
- Memory extraction and recall working ✅
- Streaming < 100ms latency ✅
- Cancel/retry fully functional ✅

**Technical Metrics:**
- 22/22 E2E tests passing ✅
- Zero console errors ✅
- API latency p95 < 200ms ✅
- Error rate < 0.1% ✅

**Deployment:**
- Sprint 2 branch merged to main ✅
- Deployed to staging for QA ✅
- Ready for v0.2.0-sprint2 tag ✅

---

## References

- **MASTER-DESIGN.md** — Part VI (Ask Diana), Part VIII (AI Router, Memory Service)
- **ECOSYSTEM-MAP.md** — Diana's request flow
- **SERVICE-SPECIFICATION-TEMPLATE.md** — How services are defined

---

**Sprint 2 is the foundation for everything else.**

Make it solid.

Make it work.

Make it beautiful.

