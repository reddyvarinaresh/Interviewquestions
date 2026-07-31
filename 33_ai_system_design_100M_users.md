# AI System Design for 100 Million Users
> **Sources:** Google System Design interviews, LinkedIn Engineering Blog, Meta Engineering, OpenAI, Anthropic, Bytedance, Netflix Tech Blog, AWS Architecture Blog, Reddit r/MachineLearning, Glassdoor interview reports, Blind, MLOps Community
> **Scope:** RAG Architecture · Scalable AI · MCP · Agents · LLM Systems · Vector DBs · Real-World Scale
> **Level:** Senior/Staff/Principal | 8–15 Years | FAANG + Top Tier Product + AI Companies

---

## THE MASTER FRAMEWORK — How to Tell the Story in Any AI System Design

> **Memorize this. Use it every time. It's your narrative structure.**

```
PHASE 1 — CLARIFY (5 min)
  → "Before I design, let me ask a few questions..."
  → Functional requirements: What does it DO?
  → Non-functional: Scale, latency, consistency, availability
  → AI-specific: accuracy SLA, hallucination tolerance, freshness of data, model type

PHASE 2 — ESTIMATE SCALE (3 min)
  → Users → Daily Active → QPS (queries per second)
  → Data volume → Storage → Throughput
  → LLM-specific: tokens/sec, context window, embedding dims, vector index size

PHASE 3 — HIGH-LEVEL DESIGN (10 min)
  → Draw the box diagram: Client → Gateway → Core Services → Data Stores
  → For AI: add Retrieval Layer + LLM Layer + Feedback Loop
  → Tell the data flow story: "A request comes in... it hits... then goes to... finally..."

PHASE 4 — DEEP DIVE (20 min)
  → Pick 2-3 hardest components and go deep
  → For AI: chunking strategy, embedding model, vector search, prompt engineering, caching
  → Show you know tradeoffs: "I chose X over Y because..."

PHASE 5 — BOTTLENECKS + FAILURES (10 min)
  → "Now let me think about what breaks..."
  → LLM: latency, cost, hallucination, token limits, rate limits
  → Scale: hot partitions, cache invalidation, vector DB scaling
  → Failures: fallbacks, circuit breakers, graceful degradation

PHASE 6 — TRADE-OFFS (5 min)
  → "If I had more time / different constraints, I'd consider..."
  → Accuracy vs latency, cost vs quality, freshness vs consistency
```

---

## MOST ASKED AI SYSTEM DESIGN QUESTIONS (2024–2025)

### FREQUENCY MAP — From Google, Meta, Amazon, Microsoft, Anthropic, LinkedIn

| Rank | Question | Companies | Frequency |
|------|----------|-----------|-----------|
| 🔴1 | Design a RAG-based Q&A system at scale | Google, Meta, Amazon, Microsoft, Optum | Very High |
| 🔴2 | Design a scalable LLM API gateway / inference service | Google, Amazon, Cohere, Anthropic | Very High |
| 🔴3 | Design a multi-agent AI system / orchestration platform | Meta, Google DeepMind, Waymo, enterprise AI | Very High |
| 🔴4 | Design a recommendation system for 100M users | Netflix, Spotify, Amazon, LinkedIn, TikTok | Very High |
| 🔴5 | Design a real-time AI chatbot / copilot system | Microsoft (Copilot), Google (Gemini), Amazon | Very High |
| 🟡6 | Design a vector database / embedding search system | Pinecone, Weaviate, MongoDB AI, Elastic | High |
| 🟡7 | Design an MCP server and tool-use agent system | Anthropic, enterprise AI startups | High |
| 🟡8 | Design a GenAI document processing pipeline | Optum, Carelon, Goldman Sachs, JP Morgan | High |
| 🟡9 | Design a model serving / MLOps infrastructure | Google, Meta, Netflix, Spotify | High |
| 🟡10 | Design a real-time fraud detection AI system | Stripe, PayPal, Visa, Mastercard | High |
| 🟡11 | Design a semantic search engine at scale | Elastic, Algolia, Google, LinkedIn | High |
| 🟡12 | Design a feedback loop / RLHF data pipeline | OpenAI, Anthropic, Google | Medium |
| 🟡13 | Design an AI evaluation / evals platform | All AI companies | Medium |
| 🟡14 | Design a prompt management / versioning system | Enterprise AI, LLMOps platforms | Medium |
| 🟡15 | Design a knowledge graph + LLM hybrid system | Microsoft (Graph RAG), Google KG | Medium |

---

## PART 1: DESIGN A RAG SYSTEM FOR 100 MILLION USERS

> **Asked at:** Google, Amazon, Microsoft, Optum, Carelon, Deloitte AI, Accenture GenAI
> **The #1 most asked AI system design question in 2024–2025**

---

### HOW TO TELL THE STORY

**Opening Line (memorize this):**
> *"I'll design this in 6 phases. First let me clarify what we're building, then estimate scale, then walk through the architecture."*

---

### STEP 1: CLARIFY REQUIREMENTS

**You say:**
> *"Before jumping into design, let me ask a few questions to make sure I build the right thing..."*

```
FUNCTIONAL REQUIREMENTS:
✅ Users can ask natural language questions
✅ System retrieves relevant documents and generates answers
✅ Supports documents: PDF, DOCX, web pages, databases
✅ Answer includes citations/source references
✅ Supports follow-up questions (conversational context)
✅ Admin can add/update/delete documents

NON-FUNCTIONAL REQUIREMENTS:
✅ 100 million users
✅ P99 latency < 3 seconds for response
✅ 99.99% availability
✅ Support 50K concurrent queries
✅ Document corpus: 10 billion documents, updated daily

AI-SPECIFIC CLARIFICATIONS (always ask these):
✅ "What's the acceptable hallucination rate?" → <1% for healthcare, <5% for general
✅ "How fresh does data need to be?" → Real-time vs hourly vs daily
✅ "Multilingual support?" → English-only vs global
✅ "PII / sensitive data in documents?" → Affects encryption, masking
✅ "Model preference?" → OpenAI GPT-4o vs Claude vs Gemini vs self-hosted
```

---

### STEP 2: ESTIMATE SCALE

**You say:**
> *"Let me back-of-envelope this before designing..."*

```
USERS:
  100M total users
  10M Daily Active Users (DAU) — 10% DAU ratio
  Peak: 2M concurrent users

QPS CALCULATION:
  10M DAU × 5 queries/day = 50M queries/day
  50M / 86,400 seconds = ~580 QPS average
  Peak = 3× = ~1,750 QPS

DOCUMENT CORPUS:
  10 billion documents × 2KB avg = 20TB raw text
  Chunked: 10B docs × 5 chunks avg = 50B chunks
  Embedding: 50B chunks × 1536 dims × 4 bytes = 300TB vector storage
  After compression/quantization: ~75TB vector index

LLM TOKENS:
  Per query: ~500 tokens context + 200 tokens answer = 700 tokens
  1,750 QPS × 700 tokens = 1.2M tokens/sec → needs ~100 A100 GPUs or GPT-4o API

CACHING:
  30% of queries are repeat → cache hit ratio target
  Cache: 50M unique queries × 2KB response = 100GB → fits in Redis cluster

BANDWIDTH:
  1,750 QPS × 2KB response = 3.5 MB/s (manageable)
```

---

### STEP 3: HIGH-LEVEL DESIGN

**You say:**
> *"Here's my high-level design. A query comes in through the API Gateway, gets routed to the RAG Query Service, which retrieves relevant chunks from the Vector DB, builds a prompt, calls the LLM, and streams the response back."*

```
┌─────────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                                │
│  Web / Mobile / API Consumer                                        │
└──────────────────────┬──────────────────────────────────────────────┘
                       │ HTTPS / WebSocket (streaming)
┌──────────────────────▼──────────────────────────────────────────────┐
│                    API GATEWAY (Kong / AWS API GW)                  │
│  Auth (JWT/OAuth) · Rate Limiting · Routing · SSL Termination       │
└──────────┬──────────────────────────────────────────────────────────┘
           │
     ┌─────▼──────────────────────┐
     │   RAG QUERY SERVICE        │  ← FastAPI / Go service
     │  (Query understanding,     │    Stateless, horizontally scaled
     │   query rewriting,         │    50 instances behind LB
     │   orchestration)           │
     └─────┬──────────────────────┘
           │
     ┌─────▼───────────────────────────────────────────┐
     │              RETRIEVAL PIPELINE                  │
     │                                                 │
     │  ┌──────────────┐    ┌───────────────────────┐ │
     │  │ Query Encoder│    │  Sparse Search (BM25)  │ │
     │  │ (bi-encoder) │    │  (Elasticsearch)       │ │
     │  └──────┬───────┘    └───────────┬───────────┘ │
     │         │                        │              │
     │  ┌──────▼───────┐               │              │
     │  │ Vector Search│               │              │
     │  │ (ANN - HNSW) │               │              │
     │  │ Pinecone /   │               │              │
     │  │ Weaviate /   │               │              │
     │  │ pgvector     │               │              │
     │  └──────┬───────┘               │              │
     │         │     HYBRID FUSION     │              │
     │         └───────────┬───────────┘              │
     │               ┌─────▼──────┐                   │
     │               │  Re-ranker │ (cross-encoder)    │
     │               │  Cohere /  │ Top-K → Top-N      │
     │               │  BGE       │                   │
     │               └─────┬──────┘                   │
     └─────────────────────┼───────────────────────────┘
                           │ Top-N chunks
     ┌─────────────────────▼───────────────────────────┐
     │              LLM GENERATION LAYER               │
     │                                                 │
     │  ┌─────────────────────────────────┐            │
     │  │        PROMPT BUILDER           │            │
     │  │  System Prompt + Context +      │            │
     │  │  Retrieved Chunks + Question    │            │
     │  └──────────────┬──────────────────┘            │
     │                 │                               │
     │  ┌──────────────▼──────────────────┐            │
     │  │       LLM Router / Gateway      │            │
     │  │  GPT-4o / Claude / Gemini /     │            │
     │  │  Self-hosted (vLLM)             │            │
     │  └──────────────┬──────────────────┘            │
     │                 │ Streaming tokens               │
     └─────────────────┼───────────────────────────────┘
                       │
     ┌─────────────────▼───────────────────────────────┐
     │              RESPONSE LAYER                     │
     │  Citation attachment · Safety filter ·          │
     │  Response caching (Redis) · Streaming SSE       │
     └─────────────────────────────────────────────────┘

DATA INGESTION PIPELINE (offline):
  Documents → Document Processor → Chunker → Embedding Model
           → Vector DB (upsert) + Metadata DB (PostgreSQL)
```

---

### STEP 4: DEEP DIVE — CRITICAL COMPONENTS

**You say:**
> *"Let me go deep on the 3 hardest parts: chunking strategy, retrieval pipeline, and LLM serving."*

#### 4A: CHUNKING STRATEGY

```
NAIVE (don't use): Fixed 512-token chunks
  Problem: splits sentences mid-thought → bad retrieval

BETTER OPTIONS:
┌────────────────────┬────────────────────────────────────────┐
│ Strategy           │ When to Use                            │
├────────────────────┼────────────────────────────────────────┤
│ Sentence-level     │ Short docs, FAQ, legal clauses         │
│ Semantic chunking  │ Long docs, research papers             │
│ Recursive split    │ Hierarchical docs (headers → sections) │
│ Sliding window     │ Context continuity critical            │
│ Agentic chunking   │ LLM decides where to split (expensive) │
└────────────────────┴────────────────────────────────────────┘

BEST PRACTICE: Parent-Child chunks
  → Store large parent chunk (2048 tokens) for context
  → Embed small child chunks (256 tokens) for precision retrieval
  → Return parent chunk to LLM after child retrieval match
  → Called: "Small-to-Big Retrieval" or "Sentence Window Retrieval"

OVERLAP: 10-20% overlap between chunks to avoid boundary misses
METADATA: Store doc_id, chunk_id, page_num, section, timestamp with every chunk
```

#### 4B: HYBRID RETRIEVAL PIPELINE

```
WHY HYBRID (Dense + Sparse)?
  Dense (vector): great for semantic similarity → "treatment for hypertension"
  Sparse (BM25): great for exact keywords → "ICD-10 code I10"
  Hybrid beats both alone — especially for technical/medical/legal domains

IMPLEMENTATION:
  1. Run dense vector search → top 100 results with scores
  2. Run BM25 keyword search → top 100 results with scores
  3. Fuse with RRF (Reciprocal Rank Fusion):
     score = Σ 1 / (rank_i + 60) for each result list
  4. Re-rank top 20 with cross-encoder → return top 5

RE-RANKER:
  Cross-encoder reads (query + chunk) together → much better relevance
  Cohere Rerank API: ~$0.001 per 1000 calls
  Open source: BGE-reranker, ms-marco-MiniLM-L-6-v2
  Adds 100-300ms but dramatically improves precision

QUERY EXPANSION (advanced):
  → HyDE: Generate a hypothetical answer → embed it → search for similar chunks
  → Multi-query: LLM generates 3 variants of the query → union of results
  → Step-back prompting: abstract the query before searching
```

#### 4C: LLM SERVING AT SCALE

```
OPTIONS:
┌──────────────────┬──────────────────────────────────────────────────┐
│ Option           │ Trade-off                                        │
├──────────────────┼──────────────────────────────────────────────────┤
│ OpenAI API       │ No infra, high cost, latency varies, data leaves │
│ Azure OpenAI     │ Private endpoint, enterprise SLA, same cost      │
│ vLLM (self-host) │ Full control, cheaper at scale, ops overhead     │
│ Ollama (local)   │ Dev only, not for production scale               │
│ Bedrock (AWS)    │ Managed multi-model, serverless pricing          │
└──────────────────┴──────────────────────────────────────────────────┘

KEY OPTIMIZATIONS FOR SCALE:
  1. Streaming: SSE (Server-Sent Events) → user sees tokens as generated
  2. Semantic caching: Redis with embedding similarity
     → Store (query_embedding → response) pairs
     → Cosine similarity > 0.95 → return cached response
     → Saves 30-40% LLM calls
  3. KV Cache (vLLM): system prompt cached, not re-computed each call
  4. Batching: vLLM continuous batching → 10× GPU throughput vs naive serving
  5. Speculative decoding: small draft model generates, large model verifies
  6. Quantization: INT4/INT8 → 4× memory reduction, ~5% quality loss

COST ESTIMATE:
  1,750 QPS × 700 tokens × $0.01/1K tokens (GPT-4o) = $12.25/sec = $1.06M/day
  → With 30% caching: ~$740K/day
  → Self-hosted vLLM (H100s): ~$100K/day at scale
  → Decision: hybrid (cache + self-hosted + API fallback)
```

---

### STEP 5: BOTTLENECKS, FAILURES, SCALE

**You say:**
> *"Let me think about what can go wrong and how I handle it..."*

```
BOTTLENECK 1: Vector Search Latency
  Problem: ANN search on 50B vectors → can be slow
  Fix:
    → Partition index by tenant/domain (shard by category)
    → Use HNSW with ef=64 → P99 < 10ms for 1B vectors
    → Replica reads: 3+ read replicas per shard
    → Pre-filter by metadata before vector search (reduces search space)

BOTTLENECK 2: LLM Latency (Time-to-First-Token)
  Problem: GPT-4o TTFT = 1-3 seconds, unacceptable UX
  Fix:
    → Streaming SSE → user sees first token in 300ms
    → Prefetch: predictive retrieval while user is still typing
    → Smaller models for simple queries (router decides GPT-3.5 vs 4)

BOTTLENECK 3: Embedding Model Throughput
  Problem: Embedding 50B chunks during indexing + new docs daily
  Fix:
    → Async batch embedding pipeline (Kafka + worker pool)
    → Cache embeddings — don't re-embed unchanged docs
    → GPU batch inference: 512 chunks per GPU call

FAILURE: LLM API Down
  Fix: Circuit breaker → fallback to self-hosted model → degraded mode

FAILURE: Vector DB Overloaded
  Fix: Read from replica → if all fail → fall back to BM25-only retrieval

FAILURE: Hallucination
  Fix:
    → Grounding: answer MUST cite retrieved context (instructed in system prompt)
    → Faithfulness check: secondary LLM call validates answer vs context
    → Confidence score: if retrieval score < threshold → "I don't know"
    → RAGAS metrics in production: faithfulness, context precision, answer relevancy
```

---

### STEP 6: TRADE-OFFS

```
LATENCY vs ACCURACY:
  → Fewer retrieved chunks (K=3) → faster but less context
  → More chunks (K=10) → better accuracy but 2× cost + latency
  → My choice: K=5 with re-ranking beats K=10 without

COST vs QUALITY:
  → GPT-4o: best quality, $10/M tokens
  → GPT-4o-mini: 80% quality, $0.15/M tokens → use for simple queries
  → LLM Router: classify query complexity → route accordingly

FRESHNESS vs CONSISTENCY:
  → Real-time indexing (Kafka → embed → upsert): fresh but complex
  → Daily batch indexing: simpler, slight staleness
  → My choice: critical docs real-time; bulk archive daily

MANAGED vs SELF-HOSTED VECTOR DB:
  → Pinecone: fully managed, $70/M vectors/month, no ops
  → pgvector: in your PostgreSQL, free, ops burden, scales to ~100M
  → Weaviate Cloud: managed open-source, hybrid search built-in
  → My choice: pgvector for <100M vectors, Pinecone/Weaviate beyond that
```

---

## PART 2: DESIGN A SCALABLE MULTI-AGENT AI SYSTEM

> **Asked at:** Google (Vertex AI Agents), Meta (internal agent systems), Anthropic, Microsoft Copilot, enterprise AI companies
> **Emerging top question in 2025**

---

### HOW TO TELL THE STORY

**Opening:**
> *"Multi-agent systems are complex. Let me clarify what agents we need, their responsibilities, how they communicate, and then design the orchestration layer."*

---

### STEP 1: CLARIFY

```
FUNCTIONAL:
✅ Multiple specialized AI agents collaborating on a task
✅ Agent types: researcher, writer, coder, data analyst, reviewer
✅ Human-in-the-loop: humans can approve/reject agent actions
✅ Tool use: agents can call APIs, search web, execute code, query DBs
✅ Long-running tasks (minutes to hours)
✅ Task persistence: resume after failure

NON-FUNCTIONAL:
✅ 1M tasks/day
✅ P95 task completion < 2 minutes for simple tasks
✅ Tasks can have 5-50 agent steps
✅ Full audit trail of every agent decision and action
✅ Cost control: cap spend per task / per user
```

---

### STEP 2: ESTIMATE SCALE

```
Tasks: 1M/day = ~12 tasks/sec
Each task: avg 10 agent steps × 500ms each = 5 sec/task
Parallel tasks: 12 TPS × 5 sec = 60 concurrent tasks (manageable)
LLM calls: 1M tasks × 10 steps = 10M LLM calls/day = 116 calls/sec
Tool calls: 10M LLM × 3 tool calls = 30M tool executions/day
Storage: task state × 10KB = 10GB/day → 3.6TB/year
Audit logs: 30M events × 1KB = 30GB/day
```

---

### STEP 3: HIGH-LEVEL DESIGN

```
┌─────────────────────────────────────────────────────────────────┐
│                    USER / CLIENT                                │
│           Web App · Slack Bot · API Consumer                    │
└──────────────────────┬──────────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────────┐
│                  TASK SUBMISSION API                            │
│         FastAPI · Auth · Rate Limiting · Task Validation        │
└──────────────────────┬──────────────────────────────────────────┘
                       │ Publish Task
┌──────────────────────▼──────────────────────────────────────────┐
│                    TASK QUEUE (Kafka)                           │
│           Topics: task.created · task.step · task.done          │
└──────────────────────┬──────────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────────┐
│              ORCHESTRATOR (LangGraph / custom)                  │
│                                                                 │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐    │
│  │ Planner  │→  │ Executor │→  │ Reviewer │→  │ Reporter │    │
│  │  Agent   │   │  Agent   │   │  Agent   │   │  Agent   │    │
│  └──────────┘   └──────────┘   └──────────┘   └──────────┘    │
│                                                                 │
│  State Machine: PENDING → PLANNING → EXECUTING → REVIEWING →   │
│                 AWAITING_HUMAN → DONE / FAILED                  │
└──────────────────────┬──────────────────────────────────────────┘
                       │
          ┌────────────┼────────────┐
          │            │            │
    ┌─────▼──┐   ┌─────▼──┐   ┌────▼────┐
    │ TOOL   │   │  LLM   │   │ STATE   │
    │  HUB   │   │GATEWAY │   │  STORE  │
    │        │   │        │   │(Redis + │
    │Web     │   │GPT-4o  │   │Postgres)│
    │Search  │   │Claude  │   │         │
    │CodeExec│   │Gemini  │   │         │
    │DB Query│   │vLLM    │   │         │
    │API Call│   │        │   │         │
    └────────┘   └────────┘   └─────────┘
                       │
┌──────────────────────▼──────────────────────────────────────────┐
│                   HUMAN-IN-THE-LOOP SERVICE                     │
│         Approval UI · Slack Notifications · Webhook callbacks   │
└──────────────────────┬──────────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────────┐
│                  OBSERVABILITY LAYER                            │
│     Langfuse · OpenTelemetry · Prometheus · Task audit logs     │
└─────────────────────────────────────────────────────────────────┘
```

---

### STEP 4: DEEP DIVE

#### Agent State Management

```python
# Every agent step persists state — enables resume on failure
class AgentState(BaseModel):
    task_id: str
    current_step: int
    total_steps: int
    status: Literal["planning", "executing", "reviewing", "awaiting_human", "done", "failed"]
    plan: list[str]
    completed_steps: list[AgentStepResult]
    context: dict  # accumulated context across steps
    cost_usd: float
    started_at: datetime
    updated_at: datetime
    
# Persisted in: Redis (hot state) + PostgreSQL (durable)
# On worker crash: re-read from PostgreSQL, resume from last completed step
```

#### Tool Hub Design

```
TOOL CATEGORIES:
  Read tools: web_search, db_query, file_read, api_get
  Write tools: db_write, api_post, file_write, email_send
  Compute: code_executor (sandboxed Docker), math_eval
  AI tools: image_analyze, doc_summarize, translate

SAFETY:
  → All write tools require approval if confidence < 0.8
  → Code execution in sandboxed Docker container (2s timeout, no network)
  → Rate limits per tool per task (prevent runaway agents)
  → Cost cap: task killed if cost > $5 (configurable)

TOOL REGISTRY:
  → Each tool has: name, description, JSON schema for params
  → LLM sees tool descriptions → decides which to call (function calling)
  → Tools return structured JSON responses
```

#### Inter-Agent Communication Patterns

```
PATTERN 1: Orchestrator-Worker (most common)
  Central orchestrator decides which agent to call next
  Works well for: linear workflows, when you need control
  LangGraph implementation: StateGraph with conditional edges

PATTERN 2: Peer-to-Peer (decentralized)
  Agents publish messages → other agents subscribe and act
  Works well for: parallel research tasks, debate/critique agents
  Risk: harder to control, can loop

PATTERN 3: Hierarchical
  Manager agent → sub-agents → sub-sub-agents
  Works well for: complex long tasks broken into sub-tasks
  Risk: error propagation, cost explosion

MY RECOMMENDATION: Orchestrator-Worker with LangGraph
  → Explicit state machine: easy to debug, resume, audit
  → Conditional routing: orchestrator checks output → routes to next agent
  → Checkpointing: built-in to LangGraph (saves state after each node)
```

---

### STEP 5: BOTTLENECKS + FAILURES

```
PROBLEM: Agent loops (agent calls same tool repeatedly)
  Fix: Loop detection — if same tool called 3× with same args → stop

PROBLEM: Context window overflow in long tasks
  Fix: Rolling summarization — summarize older steps, keep recent raw

PROBLEM: Cost runaway
  Fix: Token budget per task → abort if exceeded → return partial result

PROBLEM: Tool call failure (API down)
  Fix: Retry with exponential backoff → fallback tool → graceful failure

PROBLEM: Human approval bottleneck
  Fix: SLA timer — if no response in 30min → auto-approve low-risk actions
       Flag high-risk actions → escalate to senior approver
```

---

## PART 3: DESIGN AN MCP SERVER ECOSYSTEM

> **Model Context Protocol — Anthropic's open standard for tool/context injection**
> **Asked at:** Anthropic, AI-forward startups, enterprise AI companies (2025)

---

### WHAT IS MCP? (Explain in interview)

**You say:**
> *"MCP is like a USB standard for AI. Instead of every LLM integration being custom, MCP defines a standard protocol for how AI models can discover and call tools, access context, and interact with external systems."*

```
WITHOUT MCP:                    WITH MCP:
  App ←→ Custom OpenAI code      App
  App ←→ Custom Anthropic code   ↕ MCP Protocol (JSON-RPC 2.0)
  App ←→ Custom tool integration MCP Server (exposes tools/resources)
  → N×M integration problem      → 1×N: any client, any server

MCP COMPONENTS:
  MCP Host: Claude Desktop, VS Code Copilot, your custom app
  MCP Client: lives inside the host, speaks MCP protocol
  MCP Server: exposes Tools, Resources, Prompts
  Transport: stdio (local) or HTTP/SSE (remote)

MCP SERVER CAPABILITIES:
  Tools: functions the LLM can call (search DB, send email, etc.)
  Resources: static/dynamic context (files, API responses, DB rows)
  Prompts: reusable prompt templates with arguments
```

---

### DESIGN A SCALABLE MCP SERVER

```
┌────────────────────────────────────────────────────────┐
│              MCP CLIENT (Claude / LLM App)             │
└──────────────────────┬─────────────────────────────────┘
                       │ MCP Protocol (JSON-RPC 2.0)
                       │ Transport: HTTP + SSE
┌──────────────────────▼─────────────────────────────────┐
│               MCP GATEWAY / ROUTER                     │
│   Auth (API Key) · Rate Limiting · Tool Registry       │
│   Routes to correct MCP microserver                    │
└──────┬──────────────┬──────────────┬───────────────────┘
       │              │              │
┌──────▼──────┐ ┌─────▼──────┐ ┌────▼───────┐
│ Database    │ │ Web Search  │ │ Document   │
│ MCP Server  │ │ MCP Server  │ │ MCP Server │
│ (SQL tools) │ │(Brave/Serp) │ │(PDF/DOCX)  │
└─────────────┘ └────────────┘ └────────────┘

TOOL DEFINITION EXAMPLE:
{
  "name": "search_claims",
  "description": "Search healthcare claims by member ID, date range, or status",
  "inputSchema": {
    "type": "object",
    "properties": {
      "member_id": {"type": "string"},
      "date_from": {"type": "string", "format": "date"},
      "status": {"enum": ["pending", "approved", "denied"]}
    },
    "required": ["member_id"]
  }
}

SCALABILITY FOR 100M USERS:
  → MCP servers are stateless → scale horizontally
  → Auth: JWT per MCP session → short-lived tokens
  → Rate limit: 100 tool calls/min per user
  → Cache: tool results cached in Redis (TTL per tool type)
  → Observability: log every tool call, input, output, latency
```

---

## PART 4: DESIGN A RECOMMENDATION SYSTEM FOR 100M USERS

> **Asked at:** Netflix, Spotify, Amazon, LinkedIn, TikTok, YouTube (most common non-AI FAANG question)

---

### THE STORY

**Opening:**
> *"Recommendation at 100M scale is a two-tower problem: candidate generation then ranking. Let me walk through both."*

---

### STEP 1: CLARIFY

```
What are we recommending? Content (articles, videos, products)
Signals available? Clicks, views, likes, purchase, dwell time, skips
Freshness: New content should surface within 1 hour
Latency: <100ms for homepage recommendations
Cold start: Handle new users + new items
Diversity: Don't show same-category items repeatedly
```

---

### STEP 2: ESTIMATE SCALE

```
Users: 100M total, 20M DAU
Recommendation requests: 20M DAU × 10 page loads = 200M req/day = 2,300 QPS
Catalog: 10M items (videos/articles)
New items: 100K new items/day
User interactions: 200M DAU × 50 events = 1B events/day = 11K events/sec
```

---

### STEP 3: HIGH-LEVEL DESIGN

```
OFFLINE PIPELINE:
  User Events (Kafka) → Feature Store (Feast) → Model Training (weekly)
  Item metadata → Item embeddings (daily) → ANN Index (Faiss/Pinecone)

ONLINE PIPELINE (real-time, <100ms):
  Request → Feature Fetch (Redis) → Candidate Generation → Ranking → Response

CANDIDATE GENERATION (recall, find ~1000 candidates from 10M):
  Multi-source retrieval:
  ├── Collaborative Filtering: user2vec → ANN search item embeddings
  ├── Content-based: user preferences → similar item embeddings
  ├── Trending: real-time popular in user's region/category
  ├── Recently interacted: items similar to last 10 interactions
  └── Editorial: human-curated lists (for cold start)

RANKING (precision, rank 1000 → 20):
  Features: user features + item features + context (time, device)
  Model: Gradient Boosted Trees or Deep & Cross Network (DCN)
  Output: P(click), P(watch_30s), P(purchase) → weighted score
  Diversity: MMR (Maximal Marginal Relevance) → rerank for diversity

REAL-TIME SIGNALS:
  User sessions streamed via Kafka → Flink processes → Redis updated
  If user clicks "comedy" → boost comedy in this session immediately
```

---

### STEP 4: TWO-TOWER MODEL (Deep Dive)

```
TWO-TOWER ARCHITECTURE:
  User Tower:  user_id → embedding → MLP → 128-dim user vector
  Item Tower:  item_id + features → embedding → MLP → 128-dim item vector
  Score = cosine_similarity(user_vector, item_vector)

TRAINING:
  Positive pairs: (user, clicked_item)
  Negatives: random unclicked items (hard negatives improve quality)
  Loss: in-batch softmax / contrastive loss

SERVING:
  Pre-compute all item embeddings offline → store in Faiss
  Real-time: compute user embedding → ANN search → top 1000 candidates
  User embedding: re-computed daily + session-adjusted in real-time

COLD START:
  New user: use demographic → popular items in category → explore-exploit
  New item: use content features (title, tags) → content-based embedding
            After 100 interactions → collaborative signal takes over
```

---

## PART 5: DESIGN LLM INFERENCE AT SCALE (API Gateway for 100M Users)

> **Asked at:** OpenAI, Anthropic, Cohere, AWS (Bedrock), Azure (OpenAI Service), Google (Vertex AI)

---

### THE STORY

**Opening:**
> *"This is a high-throughput, low-latency serving problem with variable input/output lengths — very different from traditional APIs. The key innovations are continuous batching, KV caching, and speculative decoding."*

---

### ARCHITECTURE

```
┌──────────────────────────────────────────────────────────────┐
│                    CLIENT REQUESTS                           │
│   Web · Mobile · API · Internal Services                    │
└──────────────────────┬───────────────────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────────────────┐
│              LLM API GATEWAY (Kong / custom)                 │
│  Auth · Rate Limit · Request Routing · Cost Tracking         │
│  Model selection: smart routing by task type + load          │
└──────┬──────────────────────────────────────────────────────┘
       │
┌──────▼────────────────────────────────────────────────────────┐
│                  REQUEST CLASSIFIER                          │
│   Simple query → small model (GPT-4o-mini / Haiku)          │
│   Complex query → large model (GPT-4o / Sonnet / Opus)      │
│   Code generation → specialized model (Codex / DeepSeek)    │
│   Classifier: fine-tuned BERT, <5ms latency                  │
└──────┬─────────┬──────────────────────────────────────────────┘
       │         │
┌──────▼──┐ ┌────▼───────────────────────────────────────────┐
│Semantic │ │              MODEL FLEET                       │
│ Cache   │ │  ┌──────────────┐  ┌───────────────────────┐  │
│(Redis + │ │  │ Cloud Models │  │  Self-Hosted (vLLM)   │  │
│vector   │ │  │ GPT-4o API   │  │  8× H100 per node     │  │
│lookup)  │ │  │ Claude API   │  │  Continuous batching  │  │
│         │ │  │ Gemini API   │  │  KV cache             │  │
│30-40%   │ │  └──────────────┘  └───────────────────────┘  │
│hit rate │ │                                                │
└─────────┘ └────────────────────────────────────────────────┘

KEY OPTIMIZATIONS:
  Continuous Batching: process tokens from multiple requests simultaneously
    → vLLM's PagedAttention: manages KV cache like virtual memory
    → Throughput: 10-20× better than naive batching

  Speculative Decoding:
    → Small draft model generates 4-8 tokens
    → Large model verifies all in parallel (1 forward pass)
    → Speed: 2-3× faster for latency-sensitive workloads

  Prefix Caching:
    → System prompt computed once, KV cached
    → Subsequent requests reuse → saves 20-40% compute

  Quantization:
    → INT8: 2× memory reduction, <1% quality loss
    → INT4: 4× memory reduction, ~3% quality loss (AWQ/GPTQ)

COST OPTIMIZATION:
  Route 70% queries to GPT-4o-mini ($0.15/M) vs GPT-4o ($10/M)
  = 97% cost reduction for simple queries
  Semantic cache: 30% cache hit = 30% cost reduction
  Combined: 70% overall cost reduction
```

---

## PART 6: THE COMPLETE FRAMEWORK — How to Answer ANY AI System Design

### OPENING LINES (memorize these)

| Scenario | Opening Line |
|----------|-------------|
| RAG system | *"I'll design a retrieval-augmented generation system. The three hardest parts are chunking strategy, retrieval quality, and LLM serving at scale."* |
| Agent system | *"Multi-agent systems are complex. Let me clarify agent roles, design the orchestration layer, then handle failures."* |
| Recommendation | *"At 100M scale, recommendation is a two-stage problem: candidate generation for recall, then ranking for precision."* |
| LLM API | *"This is a variable-length, high-throughput serving problem. The key is continuous batching, KV caching, and smart routing."* |
| Scalable ML | *"I'll design this as an offline training pipeline plus online serving pipeline, with a feature store connecting both."* |

---

### SCALE ESTIMATION CHEAT SHEET

```
QUICK MATH:
  1M users × 10 req/day = 10M req/day ÷ 86,400 sec ≈ 116 QPS
  100M users × 10 req/day = 1B req/day ÷ 86,400 sec ≈ 11,574 QPS
  1B req/day with 1KB payload = 1TB/day of data transfer

LLM TOKENS:
  GPT-4o input:  $5.00 per million tokens
  GPT-4o output: $15.00 per million tokens
  GPT-4o-mini:   $0.15 input / $0.60 output per million tokens
  Claude Sonnet: $3.00 input / $15.00 output per million tokens

VECTOR DB:
  1536-dim float32 embedding = 6KB per vector
  1M vectors = 6GB; 100M vectors = 600GB; 1B vectors = 6TB
  HNSW index overhead: 2-3× raw vector size

GPU MATH:
  A100 80GB: can hold ~7B param model in FP16
  H100 80GB: ~2× A100 throughput
  1 H100: ~1000 tokens/sec for 7B model; ~200 tokens/sec for 70B model
  vLLM with continuous batching: 10× naive serving throughput
```

---

### TRADE-OFF VOCABULARY (use these phrases)

```
"I'd choose X over Y because..."
"The trade-off here is latency vs accuracy..."
"At this scale, managed services make more sense because ops cost > infra cost..."
"I'd start with simple [approach] and evolve to [complex approach] as we scale..."
"This is a classic consistency vs availability trade-off; given our SLA, I'd pick..."
"The failure mode I'm most worried about is... so I'd add..."
"If the team is small, I'd use [managed service]; at FAANG scale, self-host makes sense..."
```

---

### AI-SPECIFIC TRADE-OFFS TABLE

| Decision | Option A | Option B | When to choose A | When to choose B |
|----------|----------|----------|-----------------|-----------------|
| Vector DB | Managed (Pinecone) | Self-hosted (pgvector) | <100M vectors, no ops team | >100M, control needed |
| LLM | OpenAI API | vLLM self-hosted | <100K req/day, small team | >1M req/day, cost matters |
| Chunking | Fixed size | Semantic/parent-child | Simple docs, low budget | Complex docs, quality matters |
| Retrieval | Dense only | Hybrid (dense+sparse) | Semantic queries only | Mixed keyword + semantic |
| Agent framework | LangGraph | Custom orchestrator | Standard workflows | Extreme performance/control |
| Embeddings | OpenAI ada-002 | Self-hosted (BGE) | Quick start, small scale | Cost at scale, privacy |
| Re-ranker | Cohere API | Local cross-encoder | Latency tolerant | Low latency, self-hosted |

---

### COMMON INTERVIEWER FOLLOW-UP QUESTIONS

```
Q: "How do you handle stale data in your RAG system?"
A: "Delta indexing pipeline — track doc modification timestamps.
    Changed docs re-embedded within 1 hour via Kafka event.
    Version vector entries with timestamp; retrieval filters by max_age if needed."

Q: "How do you prevent prompt injection in your agent system?"
A: "Input sanitization layer before tool calls.
    System prompt clearly separates user input with XML-like delimiters.
    Tool outputs treated as untrusted data, not injected directly into prompts.
    Separate validation LLM call checks tool output before agent acts on it."

Q: "Your RAG answer is wrong — how do you detect and improve it?"
A: "Three-layer approach:
    1. Offline RAGAS evaluation: faithfulness + context precision + answer relevancy
    2. Online signals: thumbs up/down, user corrections, session abandonment
    3. Continuous: failed queries → added to golden test set → regression suite
    Improvement: better chunking → better retrieval → better prompts → fine-tune"

Q: "How do you handle multi-modal inputs (images + text) in RAG?"
A: "Multi-modal embeddings (CLIP, GPT-4V) embed both modalities into same space.
    Images stored with text descriptions generated by vision model at index time.
    Query: text → embed → search across both text and image vectors."

Q: "What if your LLM context window isn't big enough?"
A: "Options:
    1. Map-reduce: summarize each chunk, then summarize summaries
    2. Hierarchical retrieval: coarse retrieval → fine retrieval within top docs
    3. Longer context model: Gemini 1.5 Pro (1M tokens), Claude (200K)
    4. Better chunking + re-ranking: send fewer, better chunks"

Q: "How do you reduce hallucinations to near zero?"
A: "Strict grounding: answer only from retrieved context (instructed in prompt).
    Citation requirement: every claim must cite source chunk.
    Faithfulness check: second LLM call validates answer against context.
    Abstain when uncertain: if top retrieval score < 0.7 → 'I don't have enough info'.
    Human review for critical decisions (healthcare, legal, financial)."
```

---

## QUICK REFERENCE: TOP QUESTIONS BY COMPANY

| Company | Most Asked | Secondary |
|---------|-----------|-----------|
| **Google** | Design YouTube recommendation · Design Google Search · Design Gemini RAG | Distributed systems, Bigtable, Spanner |
| **Meta** | Design Facebook Feed ranking · Design AI content moderation · Multi-agent system | Cassandra, TAO graph, real-time inference |
| **Amazon** | Design product recommendation · Design Alexa skills platform · AWS Bedrock | DynamoDB, SQS, Lambda |
| **Microsoft** | Design Copilot RAG system · Design Teams bot platform · MCP ecosystem | Azure OpenAI, CosmosDB |
| **Anthropic** | Design Claude tool use system · MCP server architecture · RLHF pipeline | Constitutional AI, evals platform |
| **LinkedIn** | Design job recommendation · Design feed ranking · Design Skill assessment AI | Kafka, Espresso, Venice |
| **Netflix** | Design content recommendation · Design A/B testing platform · LLM subtitle gen | EVCache, Flink, Mantis |
| **Spotify** | Design music recommendation · Design podcast discovery · Audio embedding search | Luigi, BigQuery, NN serving |
| **Stripe** | Design fraud detection AI · Design anomaly detection · ML feature pipeline | Kafka, Flink, feature store |
| **Optum/Carelon** | Design RAG for prior auth · Design claims AI triage · Agent for PA workflow | HIPAA, FHIR, HL7 |

---

## THE 6-STEP FRAMEWORK — FILLED EXAMPLE

> **Question: "Design a RAG-based AI assistant for a healthcare company with 100M members"**

### WHAT YOU SAY IN THE INTERVIEW

```
PHASE 1 — CLARIFY (5 min):
"Before designing, a few questions:
 - What documents are in the corpus? Clinical guidelines, member handbooks, formularies, EOBs?
 - Who are the users? Members asking about benefits, or clinicians querying clinical info?
 - What's the hallucination tolerance? Zero for dosage info, some for general FAQ?
 - Is PHI in the documents? If yes, we need HIPAA-compliant LLM (Azure OpenAI or private model)
 - Freshness: formulary changes daily, guidelines quarterly — do we need real-time indexing?"

PHASE 2 — ESTIMATE (3 min):
"100M members. Assume 5% use the assistant = 5M DAU.
 5M × 3 queries/day = 15M queries/day = 174 QPS average, peak 520 QPS.
 Document corpus: 50K clinical docs × 200 pages = 10M pages.
 After chunking: ~50M chunks × 6KB = 300GB vector storage.
 LLM: 520 QPS × 800 tokens = 416K tokens/sec → need Azure OpenAI + fallback."

PHASE 3 — HIGH-LEVEL (10 min):
"Here's the data flow: Member query → API Gateway → Query Safety Filter (detect PHI in query)
 → PHI Masking → Embedding → Hybrid Retrieval (Vector + BM25) → Re-ranking
 → Context assembly → System prompt with 'only answer from provided context' instruction
 → Azure OpenAI GPT-4o (HIPAA BAA signed) → Response → PHI check on output → Member.
 Separately, offline: document ingestion pipeline with PHI scanning before indexing."

PHASE 4 — DEEP DIVE (20 min):
"Three critical components:
 1. PHI handling: mask SSNs, member IDs, DOBs before sending to LLM. De-identify corpus.
 2. Grounding: system prompt instructs LLM to ONLY answer from context, cite sources.
 3. Faithfulness check: secondary LLM call validates answer against retrieved context.
 The guardrail pipeline runs in parallel to add <300ms."

PHASE 5 — FAILURES (10 min):
"Main failure modes:
 1. LLM hallucination on clinical info → faithfulness check + 'consult your provider' fallback
 2. PHI leak in response → output scanner catches and redacts
 3. Azure OpenAI down → fallback to self-hosted Llama-3 (HIPAA compliant on our infra)
 4. Stale formulary → daily re-indexing with change detection"

PHASE 6 — TRADE-OFFS (5 min):
"Key trade-off: cost vs safety. I'd use GPT-4o (not mini) for clinical queries — 
 quality matters more than cost in healthcare. For general FAQ, route to cheaper model.
 Trade-off 2: freshness vs cost. Real-time indexing for formularies (changes daily),
 batch for clinical guidelines (quarterly updates). Saves 80% indexing cost."
```

---

*Document Version: 2025.07 | Covers: RAG · Agents · MCP · Recommendation · LLM Serving · Scale*
*Sources: Google System Design resources, LinkedIn Engineering Blog, Meta Research, Anthropic docs, AWS Architecture Center, MLOps Community, System Design Interview (Xu), Designing ML Systems (Huyen)*
