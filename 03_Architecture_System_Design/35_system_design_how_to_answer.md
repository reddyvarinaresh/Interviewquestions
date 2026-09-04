# System Design Interview — How To Start, Explain & What Interviewers Expect
> Focus: GenAI / RAG / Architecture Rounds | Senior/Staff Level | 8–15 Years
> All 10 High-Probability End-to-End Architecture Questions

---

## THE GOLDEN FRAMEWORK — Use For Every Question

```
Step 1 — CLARIFY (2 min)
  Ask: scale? users? latency requirement? real-time or batch?
  Shows: you don't jump to solutions without understanding the problem

Step 2 — ANALOGY (30 sec)
  Ground the system in a real-world mental model
  Shows: you can communicate to non-technical stakeholders

Step 3 — PILLARS (1 min)
  Name the 3-4 core problems this system must solve
  Shows: structured thinking, not just listing components

Step 4 — ARCHITECTURE DIAGRAM (5-7 min)
  Draw boxes and arrows, left to right, request flow
  Shows: depth, component knowledge

Step 5 — DEEP DIVE on 2-3 components (5 min)
  Interviewer will pick OR you offer: "Want me to go deeper on X?"
  Shows: you can go beyond surface level

Step 6 — TRADE-OFFS (2 min)
  What did you NOT choose and why?
  Shows: maturity, real-world experience

Step 7 — SCALE / FAILURE (2 min)
  What breaks first? How do you fix it?
  Shows: production thinking
```

---

## CAN YOU START WITH A HOTEL ANALOGY?

**YES — absolutely. It works perfectly for most GenAI systems.**

```
RAG System = 5-Star Hotel

Guest (User) arrives with a request
    ↓
Concierge (API Gateway) — greets, verifies identity, routes to right department
    ↓
Front Desk Cache (Semantic Cache) — "We've answered this before, here's the answer"
    ↓
Librarian (Retriever) — goes to the hotel's massive library (vector DB)
                         finds the most relevant books/pages for the guest
    ↓
Expert Consultant (Reranker) — reads the 20 pages librarian found,
                                picks the 3 most relevant ones
    ↓
Concierge again (LLM) — reads the 3 pages, crafts a perfect answer for the guest
    ↓
Guest gets response (streaming — sentence by sentence, not waiting for full answer)

Back office (Kafka + workers) — when new documents arrive, staff quietly
                                 files them into the library without
                                 disrupting guests
```

**Why hotel works:** It maps to every RAG component naturally and interviewers love it.

---

## WHAT INTERVIEWERS ACTUALLY EXPECT

```
They do NOT expect: perfect answers or memorized solutions

They DO expect:
  ✅ Structured thinking (pillar approach)
  ✅ You ask clarifying questions before diving in
  ✅ Trade-off awareness ("I chose X over Y because...")
  ✅ Scale awareness ("this breaks at 10M users because...")
  ✅ Real production experience signals ("in my experience, X is the bottleneck")
  ✅ You drive the conversation, not just answer yes/no

Red flags they watch for:
  ❌ Jumping to solution before clarifying
  ❌ Only knowing happy path, no failure handling
  ❌ Can't explain WHY, only WHAT
  ❌ No awareness of cost / latency tradeoffs
  ❌ Silent for long periods — think aloud always
```

---

## THE 3 PILLARS INTERVIEWERS WANT IN EVERY GENAI DESIGN

```
Pillar 1 — COST
  "How do I avoid paying $1M/day in LLM calls?"
  → Semantic cache, model routing, smaller models

Pillar 2 — LATENCY
  "How do I get response in <2 seconds?"
  → Async parallel retrieval, streaming, HNSW, embedding cache

Pillar 3 — QUALITY
  "How do I make sure the answer is correct and not hallucinated?"
  → Retrieval quality (hybrid search, reranker), grounding, eval pipeline
```

---

---

# THE 10 HIGH-PROBABILITY QUESTIONS — HOW TO ANSWER EACH

---

## Q1. Design an Enterprise RAG Chatbot for Millions of Users

### Start With (say this out loud):
> "Before I design, let me clarify — are we talking millions of concurrent users
> or millions of total users per day? Is this multi-tenant (multiple companies)
> or single org? What's the latency requirement — real-time chat or async?"

### Analogy:
> "Think of this like a 5-star hotel concierge. The guest asks a question,
> the concierge checks if they've answered it before (cache), if not they
> send the librarian to fetch relevant documents, an expert picks the
> best 3 pages, and the concierge crafts the final answer."

### 3 Pillars:
```
1. Don't hit the LLM if you don't have to (cost)
2. Make retrieval fast and isolated per customer (latency + isolation)
3. Keep index fresh without blocking users (freshness)
```

### Architecture Flow:
```
Request → API Gateway (auth, rate limit, tenant_id)
        → Semantic Cache (Redis) — 40-60% traffic dies here
        → Query Router — simple? cheap model. complex? full RAG
        → Parallel: Vector Search + BM25 (scoped to tenant)
        → Reranker (top 20 → top 3)
        → LLM streaming
        → Save to cache
```

### Key Numbers:
```
Semantic cache hit rate:  40-60%
Cost reduction:           80-90% vs naive
Vector search target:     <50ms (HNSW in-memory)
First token to user:      <800ms
```

### Trade-offs to mention:
```
Namespace vs Collection vs Separate cluster for multi-tenancy
  → Namespace: cheap, weak isolation
  → Separate cluster: expensive, strong isolation (use for regulated industries)
```

---

## Q2. Design a Document Ingestion & Extraction Platform (Millions of PDFs)

### Start With:
> "What types of documents? Pure text PDFs or scanned images too?
> What's the SLA — how fast must a document be searchable after upload?
> Is extraction structured (tables, forms) or free text?"

### Analogy:
> "Think of this like a post office + library. Documents arrive (post office),
> get sorted and catalogued (extraction + chunking), then filed into the
> right shelves (vector DB + metadata store) so anyone can find them later."

### 3 Pillars:
```
1. Handle any document type reliably (PDFs, scanned, tables, images)
2. Process millions without blocking users (async pipeline)
3. Make extracted content actually findable (chunking + metadata strategy)
```

### Architecture Flow:
```
PDF Upload → S3/Blob Storage (raw)
           → Event to Kafka: "new_document"
           → Worker Pool (50 parallel workers):
               - PDF parser (PyMuPDF / pdfplumber for text)
               - OCR if scanned (Tesseract / AWS Textract)
               - Table extractor (Camelot / AWS Textract)
               - Chunker (parent-child: 128 token retrieve, 512 token LLM)
               - Embed (batch, local model bge-small)
               - Upsert to vector DB with metadata tags
           → Document searchable in ~15-30 seconds
```

### Key Design Decisions:
```
Parent-child chunking:
  Index small chunks (128 tokens) → precise retrieval
  Store pointer to parent (512 tokens) → full context to LLM

Metadata tagging (critical for filtering):
  doc_type, upload_date, department, author, language
  → Pre-filter by metadata before vector search
  → Reduces 10M docs → 50K search space = 50x faster

OCR handling:
  Text PDF → direct extract (fast, accurate)
  Scanned PDF → OCR pipeline (slower, queue separately)
  → Split into two Kafka topics, two worker types
```

### Trade-off:
```
AWS Textract vs open source OCR
  → Textract: accurate, expensive ($1.50/1000 pages)
  → Tesseract: free, less accurate on complex layouts
  → Use Textract for financial/legal docs, Tesseract for internal notes
```

---

## Q3. Design a Multi-Tenant GenAI Platform (Multiple Business Domains)

### Start With:
> "How many tenants? Are they different companies or different departments
> of the same company? What compliance requirements — HIPAA, GDPR, SOC2?
> Do tenants have different LLM preferences or one shared model?"

### Analogy:
> "Think of this like a co-working space. Each company gets their own
> locked office (data isolation), shares the building infrastructure
> (compute, networking), but can customize their own space (model, prompts).
> The building manager (platform) ensures Company A can never walk into Company B's office."

### 3 Pillars:
```
1. Complete data isolation between tenants (security)
2. Shared infrastructure, dedicated experience (cost efficiency)
3. Per-tenant customization (model, prompts, RAG config)
```

### Architecture:
```
Request → API Gateway → extract tenant_id from JWT (NEVER from user input)
        → Tenant Config Service:
            - Which LLM model? (GPT-4 / Haiku / custom)
            - Which vector DB namespace/collection?
            - Custom system prompt?
            - Rate limits for this tenant?
        → Route to tenant's isolated RAG pipeline
        → Vector search scoped to tenant namespace
        → Tenant's LLM config applied
        → Response back
```

### Isolation Options:
```
Namespace per tenant   → cheapest, okay isolation, noisy neighbor risk
Collection per tenant  → better isolation, more overhead
Separate cluster       → max isolation, expensive (enterprise/regulated only)

Rule: regulated industry (healthcare, finance) → separate cluster
      SaaS product with 1000s of tenants → namespace/collection
```

### Trade-off:
```
Noisy neighbor problem:
  One heavy tenant slows everyone on shared cluster
  Fix: per-tenant rate limiting + dedicated node pools for premium tenants
```

---

## Q4. Design an AI Assistant That Searches Documents, Databases & APIs

### Start With:
> "How many sources? Are they all structured (SQL) or mix of unstructured?
> Do we need real-time data or is batch/near-real-time okay?
> What's acceptable latency — 1 second or 10 seconds?"

### Analogy:
> "Think of this like a brilliant research assistant. You give them a question.
> They simultaneously check the filing cabinet (documents), the spreadsheet
> database (SQL), and call relevant experts (APIs) — all at the same time.
> Then they read everything, pick the best pieces, and write you a summary."

### 3 Pillars:
```
1. Route query to the right source(s) intelligently
2. Fetch from all sources in parallel (not sequential)
3. Merge and rank results from different source types
```

### Architecture:
```
Query → Query Analyzer:
          - Is this a structured data question? → SQL route
          - Is this a document question? → Vector DB route
          - Is this real-time data? → API route
          - Is this all three? → all three in parallel

        Parallel execution:
          ├── Vector search (documents)     → top 10 chunks
          ├── SQL query (structured data)   → rows/aggregates
          └── API calls (live data)         → JSON responses

        Result merger:
          - Normalize all results to text
          - Reranker scores all against original query
          - Pick top 5 across all sources

        LLM with merged context → answer
```

### Key Challenges:
```
SQL results are structured, not text:
  Convert to natural language before reranking
  "Revenue Q3 2024: $4.2M" → treat as a text chunk

API source is down:
  Fallback: skip that source, answer from others
  Flag in response: "Live pricing data unavailable, using last known"

Latency: slowest API call = bottleneck
  Set timeout per source (e.g., 2 seconds max)
  Return partial results if one source times out
```

---

## Q5. Design an Agentic AI Platform (Thousands of Agents)

### Start With:
> "What kind of agents — task-specific or general? Do agents need to
> communicate with each other? What's the expected task duration —
> seconds or hours? What's the failure/retry requirement?"

### Analogy:
> "Think of this like a staffing agency. A client gives a complex project.
> The agency has a manager (orchestrator) who breaks it into tasks,
> assigns workers (agents) with specific skills, monitors their progress,
> handles when a worker gets stuck, and assembles the final deliverable."

### 3 Pillars:
```
1. Orchestration — how tasks are broken down and assigned
2. Isolation — agents can't interfere with each other
3. Reliability — handle agent failures, loops, timeouts
```

### Architecture:
```
User Goal → Orchestrator (LLM-based planner)
          → Breaks into subtasks
          → Task Queue (Kafka/Redis)
          → Agent Pool:
              Agent 1: Web search specialist
              Agent 2: Code executor
              Agent 3: Document analyzer
              Agent 4: API caller
          → Each agent:
              - Gets task from queue
              - Has own tool set
              - Has memory (short-term: Redis, long-term: vector DB)
              - Reports result back to orchestrator
          → Orchestrator assembles final answer
```

### Key Safeguards:
```
Infinite loop prevention:
  Max steps per agent (e.g., 10 steps limit)
  Timeout per task (e.g., 5 minutes max)

Isolation:
  Each agent runs in isolated container/sandbox
  Code execution in sandboxed environment (e2b / Docker)

Failure handling:
  Agent fails → retry up to 3 times → assign to different agent
  Orchestrator detects stuck agent → replans

Cost control:
  Each agent call = LLM call = $$$
  Budget per task: if agent exceeds token budget → stop + summarize
```

---

## Q6. Design a Claims / Benefits Explanation Assistant (Healthcare)

### Start With:
> "Who are the users — members (patients) or internal staff (agents)?
> What data sources — claims DB, policy documents, EOBs?
> Compliance requirements — HIPAA? PHI handling?"

### Analogy:
> "Think of this like a knowledgeable patient advocate. When a patient
> doesn't understand their medical bill or insurance denial, this advocate
> looks up their specific claim record, reads the relevant policy, and
> explains in plain English — never guessing, only stating what's documented."

### 3 Pillars:
```
1. Accuracy over creativity — hallucination is dangerous in healthcare
2. PHI security — patient data never leaks across users
3. Explainability — every answer must cite the source document/record
```

### Architecture:
```
Member query → Auth (verify member identity, get member_id)
             → PHI-safe retrieval:
                 ├── Claims DB (SQL) — filter by member_id ONLY
                 ├── Policy vector DB — tenant = insurance plan type
                 └── EOB documents — scoped to member
             → Strict reranker (only high-confidence chunks)
             → LLM with system prompt:
                 "Answer ONLY from provided context.
                  If not found, say 'I don't have that information.'
                  Never guess. Always cite the document."
             → Response with citations
             → Audit log every query + response (compliance)
```

### Healthcare-Specific Rules:
```
PHI isolation:
  member_id filter on EVERY query — no exceptions
  Separate vector namespaces per member (not just per tenant)

Hallucination prevention:
  Confidence threshold: if retrieval score < 0.7 → don't answer
  Force citation: LLM must quote the source sentence
  Human escalation: if confidence low → "Connect to a benefits specialist"

Audit trail:
  Every query, retrieved chunk, and response → stored with timestamp
  Required for HIPAA compliance
```

---

## Q7. Design a Scalable LLM Gateway (Multiple Models & Applications)

### Start With:
> "How many models? How many applications using the gateway?
> Is this internal (single org) or SaaS (multiple customers)?
> Priority: cost optimization, latency, or reliability?"

### Analogy:
> "Think of this like an airport control tower. Multiple airlines (apps)
> want to land/take off (use LLMs). The tower manages routing, prevents
> conflicts, ensures the right plane goes to the right runway (model),
> and handles emergencies (fallbacks) when a runway is closed (model down)."

### 3 Pillars:
```
1. Route requests to the right model (cost + capability match)
2. Protect models from overload (rate limiting, queuing)
3. Fallback when a model is down (reliability)
```

### Architecture:
```
App Request → LLM Gateway:
    ├── Auth & tenant identification
    ├── Rate limiter (per app, per model)
    ├── Model router:
    │     Simple query  → GPT-4o-mini / Claude Haiku ($$$-low)
    │     Complex query → GPT-4 / Claude Sonnet ($$$-high)
    │     Code task     → CodeLlama / GPT-4
    │     Internal only → self-hosted Llama
    ├── Prompt cache check (exact match → return cached tokens)
    ├── Request to selected model
    ├── Fallback: if model down/slow → try next model
    └── Response + usage tracking (tokens, cost per app)
```

### Key Features:
```
Prompt caching:
  Anthropic Claude: caches system prompt tokens (save 90% cost on repeated prompts)
  Gateway manages cache keys

Cost tracking:
  Per application: how many tokens used, how much cost
  Alert when app exceeds budget

Fallback chain:
  GPT-4 unavailable → try Claude Sonnet → try GPT-4o → queue + retry
  Zero downtime even when one provider has outage
```

---

## Q8. Design an Enterprise Knowledge Management / Search Platform

### Start With:
> "What data sources — SharePoint, Confluence, emails, PDFs, Slack?
> Users: all employees or specific teams? Real-time freshness needed?
> Is this search only or search + Q&A (RAG)?"

### Analogy:
> "Think of this like Google for your company. Just like Google crawls
> the internet, indexes pages, and lets you search — this system crawls
> all your internal tools, indexes documents, and lets employees find
> anything instantly — without knowing which tool it's stored in."

### 3 Pillars:
```
1. Connect all sources without manual effort (connectors)
2. Keep index fresh as documents change (incremental sync)
3. Search AND answer — not just find documents (RAG on top)
```

### Architecture:
```
Data Sources: SharePoint, Confluence, Google Drive, Slack, Notion, PDFs
    ↓
Connector Layer (one per source):
  - Polls for changes every 15 minutes
  - Extracts new/modified documents
  - Pushes to indexing pipeline
    ↓
Indexing Pipeline (Kafka + workers):
  - Parse + chunk
  - Embed (local model)
  - Upsert to vector DB with metadata (source, last_modified, author, dept)
    ↓
Search Layer:
  Query → metadata pre-filter (dept, date, source)
        → Hybrid search (vector + BM25)
        → Reranker
        → Results with source citations

Q&A Layer (optional RAG on top):
  Same retrieval → LLM → natural language answer with citations
```

### Key Decisions:
```
Incremental sync (not full re-index):
  Track document hash or last_modified timestamp
  Only re-embed documents that changed
  Saves 90%+ of compute on each sync cycle

Permission sync:
  Document has restricted access in SharePoint → must respect in search
  Store permissions as metadata → filter at query time
  "Only return docs this user has access to"
```

---

## Q9. Design ChatGPT for Internal Organizational Data

### Start With:
> "What's the primary data — documents, databases, both?
> Should it remember conversation history? Single user or multi-user?
> Security: can employees see each other's data?"

### Analogy:
> "Think of this like hiring a brilliant new employee who has read
> every document in the company — every policy, every project doc,
> every past contract — and remembers all of it. You just chat with
> them in plain English and they answer from company knowledge,
> not from the internet."

### 3 Pillars:
```
1. Ground answers in company data only (no hallucination from general knowledge)
2. Remember conversation context (multi-turn chat)
3. Know what each employee is allowed to see (permissions)
```

### Architecture:
```
Employee → Chat UI
         → Conversation Manager:
               Stores chat history in Redis (short-term)
               Summarizes old messages when context too long
         → Query = current message + last 3 exchanges (context window)
         → RAG Pipeline:
               Permission-filtered retrieval (only docs user can see)
               Hybrid search → reranker → top 3 chunks
         → LLM with:
               System prompt: "You are an assistant for [Company].
                               Answer ONLY from provided documents.
                               If not found, say you don't know."
               Context: chat history + retrieved chunks
         → Response streamed back
         → Audit log (enterprise compliance)
```

### Key Differences from Public ChatGPT:
```
Grounding:    Public ChatGPT uses training data → can hallucinate
              Internal: ONLY uses company docs → factual, citable

Privacy:      Public: your data goes to OpenAI
              Internal: LLM can be self-hosted (Llama) or private API

Permissions:  Public: everyone sees everything
              Internal: each user sees only what they're authorized for

Memory:       Public ChatGPT: per-session
              Internal: can persist memory across sessions per employee
```

---

## Q10. Design a Real-Time Customer Support AI Assistant

### Start With:
> "What channels — chat widget, email, voice? What's latency requirement
> for chat — under 2 seconds? Can it resolve tickets fully or just assist
> human agents? What's the escalation path?"

### Analogy:
> "Think of this like a Tier-1 support agent who has read every support
> ticket ever raised, every product manual, and every resolution.
> They answer 80% of questions instantly. For the other 20%, they brief
> the human agent with full context before handoff — so the customer
> never has to repeat themselves."

### 3 Pillars:
```
1. Answer instantly with high accuracy (latency + quality)
2. Know when to escalate to human (confidence threshold)
3. Seamless handoff — human gets full context, not a blank slate
```

### Architecture:
```
Customer message → Real-time channel (WebSocket for chat)
                 → Intent classifier (fast, small model):
                     "billing query" / "technical issue" / "complaint" / "cancel"
                 → Route:
                     ├── FAQ/known issue → semantic cache → instant answer
                     ├── Account-specific → CRM lookup + RAG → personalized answer
                     └── Complex/angry → flag for human immediately
                 → RAG pipeline:
                     Customer context (CRM: account, history, open tickets)
                     + Knowledge base (product docs, FAQs)
                     → LLM (small, fast: GPT-4o-mini or Haiku)
                     → Stream response
                 → Confidence check:
                     High confidence → send to customer
                     Low confidence  → "Let me connect you with a specialist"
                                       → human agent gets: full chat, retrieved context,
                                         suggested answer (agent can accept/edit)
```

### Real-Time Requirements:
```
WebSocket not REST:
  REST: request-response, no streaming
  WebSocket: keep connection open, stream tokens as generated
  Customer sees first word in <500ms even if full answer takes 3s

Sentiment detection:
  Angry customer detected → escalate immediately regardless of confidence
  "I'm going to cancel" → flag for retention specialist

Session memory:
  Customer said "my order #1234" earlier in chat
  Next message "when will it arrive" → must remember order context
  Store in Redis per session_id
```

---

## UNIVERSAL TRADE-OFF TABLE — Say These In Any Interview

```
Decision               Option A              Option B              When A         When B
─────────────────────────────────────────────────────────────────────────────────────────────
LLM model size         Small (Haiku/Mini)    Large (GPT-4/Sonnet)  Simple Q&A     Complex reasoning
Tenant isolation       Namespace             Separate cluster      SaaS 1000s     Regulated enterprise
Index type             HNSW (approximate)    Flat (exact)          >10K docs      <1K docs, need exact
Chunking               Small (128 tokens)    Large (512 tokens)    Precise retrieval  Full context
Indexing               Sync (wait)           Async (Kafka)         <100 docs/day  High volume uploads
Embedding model        Local (bge-small)     API (OpenAI)          High volume    Low volume, max quality
Retrieval              Vector only           Hybrid (vector+BM25)  Semantic only  Keyword matters too
Response               Wait for full         Stream tokens         Simple UIs     Chat UIs, long answers
Storage                Float32 (precise)     INT8 quantized        <100K docs     Millions of docs
```

---

## OPENING LINES THAT IMPRESS INTERVIEWERS

**For any RAG question:**
> "At this scale, RAG stops being an ML problem and becomes a distributed
> systems problem. The three axes I optimize for are cost, latency, and quality."

**For any multi-tenant question:**
> "The first thing I lock down is the isolation boundary — vector DBs search
> by similarity, not by ownership. Without explicit scoping, you get data leakage."

**For any scale question:**
> "Before I design for scale, I identify what breaks first. Usually it's the
> LLM call — it's the most expensive and slowest component. So I design
> everything upstream to avoid hitting it unnecessarily."

**For any document processing question:**
> "Documents are not just text. I design for three document types separately:
> native text PDFs, scanned PDFs needing OCR, and structured documents
> with tables — each needs a different processing path."

---

## THE ONE-PAGE CHEAT SHEET

```
EVERY GENAI DESIGN = same 6 components, different config:

[Ingestion]  →  [Index]  →  [Cache]  →  [Retrieve]  →  [Rerank]  →  [Generate]

Ingestion:   Kafka async pipeline, parallel workers, OCR if needed
Index:       HNSW, INT8 quantized, metadata tags, shard by tenant
Cache:       Semantic cache (answers) + Embedding cache (vectors) in Redis
Retrieve:    Hybrid (vector + BM25), metadata pre-filter, top_k=20
Rerank:      CrossEncoder MiniLM, top 20 → top 3
Generate:    Stream tokens, small model for simple, large for complex

COST levers:   Semantic cache → model routing → smaller embeddings
LATENCY levers: Parallel retrieval → HNSW → in-memory index → streaming
QUALITY levers: Hybrid search → reranker → parent-child chunks → citations
SAFETY levers:  tenant_id from JWT → permission filter → confidence threshold → fallback
```
