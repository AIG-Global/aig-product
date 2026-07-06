# Chapter 5: Ask Diana

## The Central Intelligence of AIGINVEST

**Version 1.0** | 2026

---

## What is Ask Diana?

Ask Diana is the service that processes every conversation, builds context, accesses memory, executes tools, and routes requests to AI providers.

It is the brain of AIGINVEST.

---

## Architecture

```
User Message
     │
     ▼
┌────────────────────────────────────────────────────┐
│                  Ask Diana Service                  │
│                                                     │
│  ┌─────────────────────────────────────────────┐   │
│  │              Context Engine                  │   │
│  │  • Conversation history (last 20 messages)  │   │
│  │  • User memories from DianaMemory table     │   │
│  │  • Diana system prompt + personality        │   │
│  └──────────────────────┬──────────────────────┘   │
│                         │                           │
│  ┌──────────────────────▼──────────────────────┐   │
│  │              Memory Engine                   │   │
│  │  • Extract facts from user messages         │   │
│  │  • Save to DianaMemory (async)              │   │
│  │  • Categories: profile, preferences,        │   │
│  │    project_context, technical_constraint    │   │
│  └──────────────────────┬──────────────────────┘   │
│                         │                           │
│  ┌──────────────────────▼──────────────────────┐   │
│  │               Tool Runner                    │   │
│  │  • Intent detection (before streaming)      │   │
│  │  • create_project → ProjectService         │   │
│  │  • create_document → DocumentService       │   │
│  │  • [future] create_task, search, calendar  │   │
│  └──────────────────────┬──────────────────────┘   │
│                         │                           │
│  ┌──────────────────────▼──────────────────────┐   │
│  │            Provider Router                   │   │
│  │  • OpenAI GPT (general chat)                │   │
│  │  • Anthropic Claude (long reasoning)        │   │
│  │  • Ollama (local/private)                   │   │
│  │  • Mock (development/testing)               │   │
│  │  • Auto-fallback on error                   │   │
│  └──────────────────────┬──────────────────────┘   │
│                         │                           │
│  ┌──────────────────────▼──────────────────────┐   │
│  │           SSE Streaming Layer                │   │
│  │  • Chunks sent word by word                 │   │
│  │  • Events: chunk / action / title / done    │   │
│  │  • AbortController support (cancel)         │   │
│  └──────────────────────────────────────────────┘   │
└────────────────────────────────────────────────────┘
     │
     ▼
User sees Diana typing
```

---

## Diana's Personality

Diana follows a strict system prompt that defines her voice and behavior:

- **Direct and precise.** No unnecessary hedging.
- **Genuinely helpful.** Focuses on what the user actually needs.
- **One good question.** Asks one clarifying question rather than many shallow ones.
- **Honest.** If she doesn't know, she says so.
- **Action-oriented.** Creates things when asked, doesn't just describe.

---

## Memory Categories

| Category | Key Examples | When Saved |
|----------|-------------|------------|
| `user_preference` | name, role, language | First mention |
| `technical_constraint` | programming language, stack | On use |
| `project_context` | company name, project names | On mention |
| `working_style` | communication preferences | Over time |

---

## Current Tool Set (Alpha 0.2)

| Tool | Trigger | Action |
|------|---------|--------|
| `create_project` | "create a project called X" | ProjectService.createProject() |
| `create_document` | "write a document called X" | DocumentService.createDocument() |

---

## Planned Tool Set (Alpha 0.3+)

| Tool | Trigger | Action |
|------|---------|--------|
| `create_task` | "add a task to X project" | TaskService.createTask() |
| `search_memory` | "do you remember..." | MemoryService.search() |
| `schedule_event` | "schedule a meeting for..." | CalendarService.create() |
| `summarize_conversation` | end of session | DianaMemory.saveConversationSummary() |
| `create_workspace` | "create a workspace for..." | WorkspaceService.create() |

---

## Provider Configuration

Set in `.env`:

```env
# Required: choose a provider
LLM_PROVIDER=openai          # or: anthropic | ollama | mock

# OpenAI
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-4o-mini     # default

# Anthropic
ANTHROPIC_API_KEY=sk-ant-...
ANTHROPIC_MODEL=claude-3-haiku-20240307

# Ollama (local)
OLLAMA_URL=http://localhost:11434
OLLAMA_MODEL=llama3.2
```

No code changes required to switch providers.

---

## Quality Gate for Diana Responses

Before any response is shown, the system evaluates:

1. Do I have enough context? (ContextEngine)
2. Should I search memory? (DianaMemory)
3. Should I use a tool? (Tool Runner intent detection)
4. Which model is best for this request? (Provider Router — future routing logic)
5. Should I stream the response? (Always yes in production)
6. Should I save anything from this interaction? (Memory extraction)

---

## SSE Event Protocol

| Event Type | Payload | Purpose |
|-----------|---------|---------|
| `chunk` | `{ content: string }` | Stream word to frontend |
| `action` | `{ action: string, result: any }` | Tool executed |
| `title` | `{ title: string }` | Auto-title set for conversation |
| `done` | `{ response: string }` | Stream complete |
| `error` | `{ message: string }` | Error occurred |
