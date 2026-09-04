# GenAI Application System Design — All Real Interview Questions
> **Focus:** What interviewers ACTUALLY ask at Google, Meta, Amazon, Microsoft, Anthropic, OpenAI, Deloitte AI, Accenture GenAI, Optum, Carelon, EPAM, Brillio, AI startups
> **Sources:** Glassdoor reports, LinkedIn posts, Blind, Reddit r/MachineLearning, r/cscareerquestions, candidate feedback 2023–2025
> **Level:** Senior / Staff / Principal | 8–15 Years | GenAI roles

---

## MASTER QUESTION LIST — Frequency Ranked (Real Sources)

| Rank | Question | Where Asked | Frequency |
|------|----------|-------------|-----------|
| 🔴1 | Design a RAG-based document Q&A system | Google, Amazon, Microsoft, Deloitte AI, Optum | Very High |
| 🔴2 | Design a scalable LLM chatbot / copilot | Microsoft (Copilot team), Google, all GenAI cos | Very High |
| 🔴3 | Design a multi-agent AI orchestration platform | Meta, Google DeepMind, Anthropic, enterprise AI | Very High |
| 🔴4 | How would you scale an LLM inference service to 1M req/day? | OpenAI, Anthropic, Cohere, AWS, Azure | Very High |
| 🔴5 | Design a semantic search engine | LinkedIn, Elastic, Algolia, Google, Amazon | Very High |
| 🔴6 | Design an AI-powered document processing pipeline | Optum, Carelon, Goldman Sachs, JP Morgan AI | High |
| 🔴7 | Design a vector database / embedding search system | Pinecone, Weaviate, MongoDB AI, all GenAI cos | High |
| 🔴8 | Design a GenAI prior authorization system | Optum, Carelon, Cigna, Elevance | High |
| 🔴9 | Design an AI content moderation system | Meta, TikTok, YouTube, Twitter/X | High |
| 🔴10 | Design a prompt management and versioning platform | All GenAI companies, LLMOps platforms | High |
| 🟡11 | Design a real-time AI fraud detection system | Stripe, PayPal, Visa, Mastercard AI | High |
| 🟡12 | Design an RLHF / feedback data collection pipeline | OpenAI, Anthropic, Google | High |
| 🟡13 | Design an LLM evaluation (evals) platform | All AI companies | High |
| 🟡14 | Design a fine-tuning pipeline for domain adaptation | All AI roles | High |
| 🟡15 | Design an AI code assistant (like GitHub Copilot) | GitHub, Amazon (CodeWhisperer), Google | High |
| 🟡16 | Design a knowledge graph + LLM hybrid system | Microsoft (GraphRAG), Google KG, enterprise | Medium |
| 🟡17 | Design a multi-modal AI system (text + image + audio) | Google (Gemini), OpenAI (GPT-4V), Meta | Medium |
| 🟡18 | Design a GenAI hallucination detection system | Healthcare AI, fintech AI, all critical domains | Medium |
| 🟡19 | Design an AI A/B testing platform for LLM prompts | All product companies with AI features | Medium |
| 🟡20 | Design a real-time AI recommendation + explanation system | Netflix, Spotify, Amazon, LinkedIn | Medium |
| 🟡21 | Design a GenAI data pipeline for training data curation | OpenAI, Anthropic, Google, Cohere | Medium |
| 🟡22 | Design a personal AI assistant with memory | OpenAI (Memory), Google (Gemini), Apple AI | Medium |
| 🟡23 | Design a GenAI voice assistant system | Amazon Alexa, Google Assistant, Apple Siri | Medium |
| 🟡24 | Design a token budget management system for LLM APIs | All companies using LLM APIs at scale | Medium |
| 🟡25 | Design a GenAI guardrails / safety layer | All regulated industry AI (healthcare, finance, legal) | Medium |

---

## SECTION 1: RAG SYSTEM DESIGN (Most Asked)

### Q1: Design a RAG-based Q&A system for enterprise documents
**Asked at:** Google, Amazon, Microsoft, Deloitte AI, Accenture GenAI, Optum, Carelon

#### What interviewers want to hear:

**Clarify first:**
```
- What documents? PDFs, Word, SharePoint, web? → affects ingestion pipeline
- Who queries? Internal employees or external customers? → affects auth + scale
- Freshness: how often docs update? → affects indexing strategy
- Hallucination tolerance? → affects guardrails design
- PII / sensitive data? → affects encryption, masking before LLM
- Expected scale? Users, QPS, corpus size?
```

**Scale Estimation:**
```
10K employees × 20 queries/day = 200K queries/day = 2.3 QPS avg, 7 QPS peak
100K documents × 50KB avg = 5GB raw text
After chunking (256-token chunks): ~2M chunks
2M chunks × 1536 dims × 4 bytes = ~12GB vector storage → fits in Pinecone free tier
```

**Architecture:**
```
INGESTION (offline):
  Documents (S3/SharePoint)
    → Document Loader (LangChain / custom)
    → Text Extractor (PyMuPDF for PDF, python-docx)
    → Chunker (recursive text splitter, 256 tokens, 10% overlap)
    → Embedding model (text-embedding-3-small or BGE-large)
    → Vector DB (Pinecone / Weaviate / pgvector) upsert
    → Metadata DB (PostgreSQL): doc_id, chunk_id, source, page, updated_at

QUERY (online, real-time):
  User query
    → Query rewriting (LLM: expand abbreviations, fix typos)
    → Embed query (same model as indexing!)
    → Hybrid search: Dense (ANN) + Sparse (BM25) → fuse with RRF
    → Re-rank (Cohere Rerank or BGE cross-encoder) → top 5 chunks
    → Prompt: [System: answer only from context] + [Context: chunks] + [Query]
    → LLM (GPT-4o / Claude) → streaming response
    → Citation attachment: which chunks were used
    → Cache response in Redis (semantic cache, TTL 1 hour)
```

**Deep dive interviewers always ask:**
```
Q: "How do you chunk documents?"
A: Naive fixed-size is bad — splits mid-sentence.
   Use recursive text splitter: tries to split on \n\n → \n → sentence → word.
   Better: parent-child chunks — small child chunks (128 tokens) for retrieval,
   large parent chunk (512 tokens) returned to LLM for context.
   For PDFs: respect page boundaries + section headers as split points.

Q: "What if retrieval quality is poor?"
A: 1. Add re-ranker (cross-encoder) — biggest single quality improvement
   2. Switch to hybrid retrieval (dense + sparse) — helps keyword queries
   3. Query rewriting — LLM generates 3 query variants, union of results
   4. HyDE — generate hypothetical answer, embed it, search for similar chunks
   5. Metadata filtering — filter by doc_type, date before vector search

Q: "How do you handle staleness?"
A: Track doc modification timestamps.
   Change detection via S3 events or webhook → publish to Kafka
   Kafka consumer: re-extract → re-chunk → re-embed → upsert (overwrite old vectors)
   Critical docs: real-time pipeline (<1hr); archive docs: nightly batch

Q: "How do you reduce hallucinations?"
A: System prompt: "Answer ONLY from the provided context. If unsure, say 'I don't know'."
   Citation requirement: every claim must reference source chunk ID
   Faithfulness check: secondary LLM call validates answer vs context (RAGAS)
   Retrieval confidence: if top chunk score < 0.7 → "Not enough information found"
```

**Failure modes:**
```
- Vector DB down → fallback to BM25-only (Elasticsearch)
- LLM API down → circuit breaker → cached responses or "service unavailable"
- Hallucination → faithfulness check + abstain if fails
- PII in response → output scanner (Microsoft Presidio) catches + redacts
- Context overflow → map-reduce: summarize chunks → summarize summaries
```

---

### Q2: RAG vs Fine-tuning — when do you use each?
**Asked at:** Every GenAI interview — asked as follow-up to RAG design

```
RAG:
  Use when: knowledge changes frequently, knowledge is large (can't fit in weights),
            citations needed, domain docs already exist, interpretability required
  Pros: fresh data, citable sources, no training cost, easy to update
  Cons: retrieval quality affects answer quality, latency (+500ms), context window limits

FINE-TUNING:
  Use when: style/format adaptation (always respond as X), domain jargon,
            task-specific behavior (always output JSON), knowledge truly static,
            latency critical (no retrieval step), small focused knowledge set
  Pros: bakes in behavior, fast inference (no retrieval), lower token cost per call
  Cons: knowledge becomes stale, expensive to retrain, no citations

HYBRID (most common production answer):
  Fine-tune for: behavior, tone, output format, domain terminology
  RAG for: dynamic knowledge, current events, document-level facts
  
EXAMPLE: Healthcare chatbot
  Fine-tune on: medical conversation style, ICD-10 code formatting, clinical tone
  RAG for: current formulary, specific plan benefits, provider network docs
```

---

## SECTION 2: LLM SCALING SYSTEM DESIGN

### Q3: How would you scale an LLM API to serve 1 million requests per day?
**Asked at:** OpenAI, Anthropic, Cohere, AWS (Bedrock team), Azure OpenAI team, any senior AI infra role

**What interviewers want:**
```
STEP 1 — MATH:
  1M req/day ÷ 86,400 = 11.6 QPS avg
  Peak (3×) = 35 QPS
  Avg tokens per request: 500 input + 300 output = 800 tokens
  Total tokens/day: 1M × 800 = 800M tokens/day

STEP 2 — OPTIONS:
  Option A: API only (OpenAI/Anthropic)
    Cost: 800M × $0.01/1K = $8,000/day (GPT-4o) → $240K/month
    No GPU ops burden. Rate limits can be a problem at peak.

  Option B: Self-hosted vLLM
    Hardware: ~4× H100 GPUs for Llama-3-70B at 35 QPS
    Cost: ~$30K/month cloud GPU rental
    Control: full, no rate limits, data stays on-prem
    At 1M req/day → self-hosted is 8× cheaper

  Option C: Hybrid (real production answer)
    Primary: self-hosted vLLM for 80% of traffic
    Burst: OpenAI API for traffic spikes
    Fallback: OpenAI API if vLLM cluster fails

STEP 3 — KEY OPTIMIZATIONS:
  1. Semantic caching (Redis + vector similarity)
     → Hash query embedding → if cosine > 0.95 → return cached response
     → Hit rate 25-40% → saves 25-40% LLM cost
     
  2. Model routing by complexity
     → Classifier (fine-tuned BERT, <5ms) routes query to:
       Simple (FAQ-like) → GPT-4o-mini ($0.15/M) 
       Complex → GPT-4o ($10/M)
     → 70% queries are "simple" → 97% cost reduction on those queries
     
  3. Streaming (SSE)
     → User sees tokens as generated → perceived latency drops from 3s to 0.5s TTFT
     
  4. Request batching (vLLM continuous batching)
     → Process tokens from 50 concurrent requests simultaneously
     → PagedAttention: KV cache managed like virtual memory
     → 10-20× throughput vs naive single-request serving
     
  5. Prefix caching
     → System prompt (same for all requests) computed once, KV cached
     → Saves 20-40% GPU compute on every request
     
  6. Quantization
     → INT8: 2× memory reduction, <1% quality loss
     → INT4 (AWQ): 4× memory, ~3% quality loss
     → Llama-3-70B in FP16: 140GB → INT4: 35GB → fits on 1 H100

STEP 4 — RATE LIMITING + FAIRNESS:
  Per-user limits: 100 req/min (token bucket)
  Per-tenant limits: configurable
  Priority queues: paid tier > free tier
  Queue: Redis sorted set or Kafka with priority topics
  
STEP 5 — OBSERVABILITY:
  Latency: P50/P95/P99 TTFT and total response time
  Cost: tokens per request, cost per user, cost per model
  Quality: thumbs up/down, session abandonment rate
  Errors: 4xx (bad prompt), 5xx (model errors), timeouts
  Tool: Langfuse or custom OpenTelemetry pipeline
```

---

### Q4: Design an LLM Gateway / Router
**Asked at:** Enterprise AI companies, API platform teams, Microsoft, Amazon

```
WHAT IS IT:
  Single entry point for all LLM calls across the org
  Routes to correct model, provider, version
  Handles auth, cost tracking, rate limiting, fallbacks

ARCHITECTURE:
  Client → LLM Gateway → [OpenAI | Anthropic | Gemini | vLLM]

ROUTING RULES:
  by_task_type:
    code_generation → CodeLlama / GPT-4o
    summarization → GPT-4o-mini / Claude Haiku
    reasoning → o1 / Claude Sonnet
    embeddings → text-embedding-3-small
  by_cost_tier:
    free_user → cheap model only
    paid_user → full model access
  by_latency:
    real-time chat → streaming, fast models
    batch job → any model, no streaming

FALLBACK CHAIN:
  GPT-4o → Claude Sonnet → Gemini Pro → vLLM (self-hosted)
  On: 429 (rate limit), 500, 503, timeout > 10s

COST TRACKING:
  Every call: log model, tokens_in, tokens_out, cost_usd, user_id, tenant_id
  Budget alerts: notify when user crosses 80% of monthly budget
  Hard cutoff: return error when budget exhausted

KEY FEATURES:
  A/B testing: route 10% to new model, measure quality diff
  Prompt versioning: gateway applies correct prompt template by version
  PII detection: scan before sending to external APIs
  Audit log: immutable log of every LLM call (HIPAA, SOC2)
```

---

## SECTION 3: MULTI-AGENT SYSTEM DESIGN

### Q5: Design a multi-agent AI system for enterprise task automation
**Asked at:** Meta AI, Google (Vertex AI Agents), Anthropic, Microsoft (Copilot Studio), enterprise AI cos

**What interviewers check:**
- Do you know orchestration patterns (orchestrator vs choreography)?
- Can you handle failures in long-running agent chains?
- Do you understand human-in-the-loop?
- Do you know how to prevent runaway costs / infinite loops?

```
AGENT TYPES (clarify first):
  Planner agent: breaks task into steps
  Executor agent: executes a single tool call
  Researcher agent: searches web/docs for information
  Reviewer agent: validates/critiques another agent's output
  Coordinator agent: manages sub-agents

ORCHESTRATION PATTERNS:

  Pattern 1: Orchestrator-Worker (most common, use this)
    Central orchestrator (LangGraph StateGraph) decides next agent
    Each agent = one node in graph
    State persisted after each node → resume on failure
    
  Pattern 2: Peer-to-Peer
    Agents publish to Kafka topics, others subscribe and react
    Good for: parallel research agents
    Risk: harder to control, can loop

  Pattern 3: Hierarchical
    Manager → Team Lead agents → Worker agents
    Good for: very complex long tasks
    Risk: error propagation, cost explosion

STATE MANAGEMENT (critical for reliability):
  task_id, current_node, status, plan, completed_steps, accumulated_context
  Persist in: Redis (hot, current step) + PostgreSQL (durable, full history)
  On crash: read PostgreSQL → resume from last completed step

SAFETY MECHANISMS (interviewers love this):
  Loop detection: same tool called 3× with same args → abort
  Step limit: max 50 steps per task → abort with partial result
  Cost cap: token budget per task → abort if exceeded
  Timeout: max 30 min per task
  Human approval: writes/deletes above risk threshold → await human confirm
  Tool sandboxing: code execution in Docker container (2s timeout, no network)

TOOL CATEGORIES:
  Read: web_search, db_query, file_read, api_get
  Write: db_write, api_post, email_send (require approval)
  Compute: code_exec (sandboxed), math_eval
  AI: image_analyze, summarize, translate

FAILURE HANDLING:
  Tool fails → retry 3× with backoff → fallback tool → partial result
  LLM fails → retry → different model → human escalation
  Agent loop detected → break loop → return partial result with explanation
```

---

### Q6: Design a Human-in-the-Loop agent system
**Asked at:** Healthcare AI companies, fintech AI, enterprise automation — any high-stakes AI role

```
WHEN HUMAN APPROVAL NEEDED:
  Write operations above risk threshold (delete data, send external emails)
  Agent confidence below 0.7
  Cost of action above $100
  Regulated domain: medical decisions, financial transactions, legal filings

IMPLEMENTATION:
  Agent pauses at approval checkpoint
  Sends notification: Slack / email / web push with:
    - What action is planned
    - Why (agent's reasoning)
    - Expected outcome
    - Approve / Reject / Modify buttons
  
  Human options:
    Approve → agent continues
    Reject → agent tries alternative path
    Modify → human edits action params → agent continues with modified action
    Escalate → send to senior approver

SLA HANDLING:
  Timer: if no response in 30 min → auto-escalate
  Low-risk actions: auto-approve after 2 hours if no response
  High-risk: never auto-approve → task fails with explanation

AUDIT TRAIL:
  Every approval/rejection stored immutably
  Who approved, when, what was approved, outcome
  Required for: HIPAA, SOX, GDPR compliance
```

---

## SECTION 4: GENAI APPLICATION PATTERNS (Frequently Asked)

### Q7: Design an AI Chatbot that handles 100M users
**Asked at:** Microsoft (Teams AI), Google (Dialogflow), Amazon (Lex), all enterprise AI cos

```
CLARIFY:
  Purpose? Customer support / internal helpdesk / domain expert
  Response type? FAQ → deterministic | Complex → generative
  Channels? Web / mobile / Slack / WhatsApp
  Latency? <2s for first token
  Memory? Per-session only or persistent across sessions?

ARCHITECTURE:
  User → API Gateway (auth, rate limit)
       → Intent Classifier (<5ms, fine-tuned BERT)
         → FAQ intent → deterministic answer from DB (no LLM, <50ms)
         → General intent → RAG pipeline → LLM → streaming response
         → Sensitive intent (medical, legal) → disclaimer + escalate to human

  MEMORY:
    Short-term (session): last 10 turns in Redis, TTL 30 min
    Long-term (persistent): user profile in PostgreSQL — preferences, past issues
    Retrieval: at query time, fetch user profile → inject into system prompt

  PERSONALIZATION:
    System prompt includes: user name, account type, past interactions summary
    Routing: VIP users → better model, lower queue priority for free users

SCALE TO 100M USERS:
  Stateless services → horizontal scaling (K8s HPA)
  Redis cluster for session state (not sticky sessions)
  LLM gateway with semantic cache → 30-40% cache hit
  CDN for static responses (FAQ answers)
  Regional deployment: US / EU / APAC → latency reduction
```

---

### Q8: Design a GenAI Document Processing Pipeline
**Asked at:** Optum, Carelon, Goldman Sachs, JP Morgan, Insurance companies, legal tech

```
USE CASES: Insurance prior auth forms, medical records, contracts, invoices

PIPELINE STAGES:
  1. INGESTION
     Files arrive: S3 upload / SFTP / email attachment / API upload
     Event trigger: S3 ObjectCreated → SQS → Lambda/worker

  2. EXTRACTION
     PDF text: PyMuPDF / pdfplumber
     Scanned PDF (image): Azure Document Intelligence / AWS Textract / Google Document AI
     Tables: camelot (PDF tables) or Document AI structured output
     Handwriting: Azure Document Intelligence Form Recognizer

  3. CLASSIFICATION
     What type of document is this?
     Fine-tuned classifier (BERT) → invoice / auth form / medical record / EOB
     Confidence < 0.8 → human review queue

  4. EXTRACTION (LLM-based)
     Structured extraction using LLM with output schema:
     prompt = "Extract fields from this document. Return JSON: {schema}"
     Use: GPT-4o with response_format={"type": "json_object"}
     Fields: patient_name, DOB, diagnosis_code, procedure_code, dates, signatures

  5. VALIDATION
     Required fields present? Valid ICD-10 codes? Date logic correct?
     Business rules: is procedure covered under plan?
     Validation fails → human review queue with specific error

  6. STORAGE
     Raw document: S3 (encrypted)
     Extracted data: PostgreSQL (structured)
     Embeddings: Vector DB (for RAG on documents later)
     Audit log: immutable event log (every step, timestamp, actor)

  7. OUTPUT
     Downstream system via API call or Kafka event
     Status webhook to submitter
     Human review UI for failed documents

ACCURACY METRICS:
  Field extraction accuracy (F1 score per field)
  End-to-end automation rate (% that needed no human touch)
  False positive rate (how often auto-approved wrong docs)
```

---

### Q9: Design a Semantic Search System
**Asked at:** LinkedIn (search team), Elastic, Algolia, Amazon, Google, enterprise search

```
CLARIFY:
  What are we searching? Documents, products, people, code?
  What does "relevant" mean? Exact match? Semantic similarity? Personalized?
  Scale: corpus size, QPS, latency SLA?

TWO SEARCH TYPES:
  Lexical (BM25/Elasticsearch): exact keywords, acronyms, IDs → fast, interpretable
  Semantic (vector ANN): meaning-based → "heart attack" matches "myocardial infarction"
  HYBRID: almost always better than either alone (use RRF to fuse scores)

INDEXING PIPELINE:
  Documents → chunking → embedding (bi-encoder) → Vector DB
  Metadata → Elasticsearch for lexical + filtering

QUERY PIPELINE:
  Query → embed → ANN search (dense) + BM25 (sparse)
       → RRF fusion → re-rank (cross-encoder) → top N results
       → Metadata enrich → return with highlights

SCALING:
  Sharding: shard vector index by document category / tenant
  Replicas: 3 read replicas per shard
  HNSW params: ef=64, M=16 → P99 < 10ms for 100M vectors
  Cache: popular queries cached in Redis (TTL 5 min)
  Async indexing: new docs indexed within 60 seconds via Kafka pipeline

PERSONALIZATION (advanced):
  User interaction signals (clicks, dwell) → user embedding
  At query time: blend query embedding with user embedding
  Re-rank by user-item affinity score
```

---

### Q10: Design an LLM Evaluation (Evals) Platform
**Asked at:** OpenAI, Anthropic, Google, any AI product company — increasingly common 2024-2025

```
WHY NEEDED:
  Can't ship LLM changes without knowing if quality regressed
  Model upgrades, prompt changes, RAG changes → all need eval
  Regulatory: healthcare/fintech AI needs documented quality evidence

EVAL TYPES:
  Automated metrics (no human needed):
    RAGAS: faithfulness, context precision, answer relevancy (for RAG)
    ROUGE/BLEU: for summarization (correlation with human, not perfect)
    Exact match: for structured output (JSON, SQL, codes)
    LLM-as-judge: use GPT-4 to score GPT-4o-mini output (scalable but circular)
    
  Human eval:
    Golden test set: 500 hand-curated question-answer pairs
    Human raters score: 1-5 on helpfulness, accuracy, harmlessness
    Pairwise comparison: which answer is better? (A/B)

PLATFORM ARCHITECTURE:
  Test case DB: question, expected answer, metadata (domain, difficulty)
  Eval runner: calls model → gets response → scores with metrics
  Results DB: model_version, prompt_version, metric_name, score, timestamp
  Dashboard: trend charts — did last deployment regress quality?
  Alerts: if RAGAS faithfulness drops >5% vs baseline → block deployment

CONTINUOUS EVAL PIPELINE:
  On every PR that changes prompts/model → run eval suite (CI/CD gate)
  Shadow mode: new model runs in parallel with prod → compare quality
  Online metrics: thumbs up/down, session completion rate, escalation rate

GOLDEN DATASET MANAGEMENT:
  Curate from: production failures, edge cases, domain expert review
  Version-controlled (git)
  Growing: add new cases when new failure modes discovered
```

---

### Q11: Design an AI Guardrails / Safety Layer
**Asked at:** Healthcare AI, fintech, regulated industries, Anthropic, any safety-focused AI role

```
WHAT TO GUARD:
  Input (before LLM): prompt injection, PII, toxic content, off-topic
  Output (after LLM): hallucination, PII leakage, harmful content, off-topic

INPUT GUARDRAILS:
  PII detection: Microsoft Presidio → detect SSN, DOB, credit card → mask/reject
  Prompt injection: "Ignore previous instructions" → detect + block
  Topic filter: if question not in allowed domains → polite rejection
  Jailbreak detection: classifier (fine-tuned BERT) on known jailbreak patterns

OUTPUT GUARDRAILS:
  PII scan: scan LLM response for any PII that shouldn't be there
  Faithfulness check (for RAG): does answer match context? RAGAS faithfulness
  Toxicity: Perspective API or local classifier
  Hallucination: if no source found in context but answer states fact → flag
  Format validation: if JSON expected → validate schema

ARCHITECTURE (parallel, adds minimal latency):
  Query → [Safety Check (async)] → LLM → [Output Check (async)] → Response
  Both safety checks run in parallel with main pipeline
  Total guardrail overhead: <200ms (fast local models, not API calls)

RESPONSE HANDLING:
  Critical violation: block response entirely, return safe error message
  Minor issue: add disclaimer, log for review
  PII in output: auto-redact, continue with redacted response
  
LOGGING:
  All violations logged with: type, severity, input, output, user_id, timestamp
  Violation dashboard for compliance team
  Threshold alerts: spike in violations → investigate
```

---

### Q12: Design a Fine-tuning Pipeline for Domain Adaptation
**Asked at:** All GenAI companies, any ML engineering role — very common 2024-2025

```
WHEN TO FINE-TUNE (clarify this first):
  Base model doesn't follow domain format (clinical note style, legal citations)
  Domain jargon not well handled (ICD codes, SWIFT codes, COBOL syntax)
  Task-specific behavior needed (always output structured JSON)
  Privacy: can't send data to external API → need self-hosted fine-tuned model
  Cost: fine-tuned smaller model can match large model on narrow tasks

PIPELINE:
  1. DATA COLLECTION
     Sources: expert demonstrations, existing high-quality docs, synthetic generation
     Volume: 1K–10K examples for instruction fine-tuning (LoRA)
     Format: {"instruction": ..., "input": ..., "output": ...} (Alpaca format)

  2. DATA QUALITY
     Deduplication: exact + near-duplicate removal (MinHash)
     Quality filter: perplexity scoring, LLM-as-judge quality score
     PII scrubbing: before any training (critical for compliance)
     Train/val/test split: 80/10/10

  3. TRAINING
     Full fine-tune: expensive, only for large budget / FAANG
     LoRA / QLoRA: fine-tune adapter weights only → 10-100× cheaper
       - r=16, alpha=32 typically good starting point
       - QLoRA: 4-bit base model + LoRA → fits 70B on 2× A100
     DPO (Direct Preference Optimization): train on preferred vs rejected pairs
     Infra: use Axolotl / HuggingFace TRL / torchtune

  4. EVALUATION
     Domain eval set (held out): compare fine-tuned vs base model
     Regression: does it still handle general queries well?
     Task-specific metrics: F1 for extraction, ROUGE for summarization
     Human eval: 100-sample review by domain expert

  5. SERVING
     Merge LoRA weights into base → single model checkpoint
     Deploy via vLLM or HuggingFace TGI
     Version: model registry (MLflow) tracks each fine-tuned version
     Rollback: keep previous version, easy switch if regression

COMMON INTERVIEW FOLLOW-UP:
  Q: "LoRA vs full fine-tune?"
  A: LoRA: 10-100× cheaper, nearly same quality on narrow tasks.
     Full fine-tune: when you need deep knowledge injection, have budget.
     Always start with LoRA.

  Q: "How much data do you need?"
  A: Instruction fine-tuning (style/format): 500-2K examples enough with LoRA
     Knowledge injection: 10K-100K examples, but RAG is usually better for this
     Rule of thumb: if task is narrow + style-based → LoRA with 1K examples
```

---

### Q13: Design an RLHF / Human Feedback Data Pipeline
**Asked at:** OpenAI, Anthropic, Google DeepMind, Cohere — less common but asked at frontier AI cos

```
RLHF STAGES:
  1. Supervised Fine-tuning (SFT): fine-tune on human-written demonstrations
  2. Reward Model training: human ranks responses → train reward model
  3. PPO training: RL to maximize reward model score while staying close to SFT

DATA COLLECTION PIPELINE:
  Task → multiple LLM responses generated (4-8 responses per prompt)
  Human raters see responses side-by-side → rank them (pairwise comparison)
  Ranking stored as: (prompt, chosen_response, rejected_response)
  
RATING INFRASTRUCTURE:
  Labeling platform: Scale AI, Surge AI, or internal
  Instructions: detailed rubric (helpfulness, harmlessness, honesty = HHH)
  Quality control: 15% of tasks shown to multiple raters → agreement rate
  Disagreement > 30% → task is ambiguous → add more rater guidance

SIMPLER ALTERNATIVE (DPO — Direct Preference Optimization):
  Skip reward model + RL entirely
  Train directly on (prompt, chosen, rejected) pairs
  85% of RLHF quality at 20% of the cost and complexity
  Most teams now use DPO instead of PPO

ONLINE FEEDBACK LOOP (production):
  Thumbs up/down buttons in production UI
  Session abandonment rate (implicit negative signal)
  Response editing (user rewrites LLM answer → strong training signal)
  Low-cost alternative to formal RLHF
```

---

### Q14: Design a Prompt Management and Versioning Platform
**Asked at:** Enterprise GenAI teams, LLMOps companies (Langfuse, PromptLayer), product AI teams

```
PROBLEM:
  Prompts are code — need versioning, review, testing, rollback
  Multiple teams managing hundreds of prompts → chaos without system
  Prompt change broke production → need audit trail

FEATURES:
  Prompt registry: name, version, template (Jinja2), variables, model, parameters
  Version control: git-like history, diff view between versions
  A/B testing: route X% to version A, Y% to version B → compare metrics
  Eval integration: run eval suite before promoting to production
  Access control: who can edit which prompts

ARCHITECTURE:
  DB: PostgreSQL — prompts, versions, eval results
  API: FastAPI — CRUD for prompts, promote/rollback endpoints
  SDK: Python client — `prompt_registry.get("claim_extractor", version="prod")`
  CI/CD: on PR → auto-run eval suite → block merge if quality regresses
  Cache: Redis — cache prompt by (name, version), TTL 5 min

PROMPT TEMPLATE FORMAT:
  {
    "name": "prior_auth_extractor",
    "version": "2.3",
    "model": "gpt-4o",
    "temperature": 0,
    "system": "You are a healthcare data extractor...",
    "user_template": "Extract from: {{document}}\nReturn JSON: {{schema}}",
    "variables": ["document", "schema"],
    "tags": ["production", "healthcare"],
    "eval_score": 0.94,
    "promoted_by": "alice@company.com",
    "promoted_at": "2025-03-15"
  }
```

---

### Q15: Design a Real-time AI Fraud Detection System
**Asked at:** Stripe, PayPal, Visa, Mastercard, JPMorgan, fintech AI teams

```
REQUIREMENTS:
  Decision within 100ms (during payment processing)
  100K transactions/second at peak
  <0.1% false positive rate (block legitimate transactions)
  <0.01% false negative rate (miss fraud) — asymmetric cost!

ARCHITECTURE:
  Transaction → Feature Enrichment (<10ms) → ML Scoring (<20ms) → Decision → Response

FEATURE ENGINEERING (real-time, <10ms):
  Transaction features: amount, merchant, location, time, card present
  User history (Redis): avg spend, usual merchants, velocity (txns in last 1hr)
  Device signals: IP, device fingerprint, browser UA
  Graph features: is merchant known bad? is IP associated with fraud?

ML MODEL STACK:
  Rule engine (first, <1ms): block obvious fraud (impossible geo, blacklisted cards)
  Gradient boosted model (GBM — LightGBM/XGBoost, <5ms): tabular features
  Deep learning (optional, for complex patterns, <20ms): sequence model on txn history
  LLM layer (async, not on critical path): analyze narrative patterns for new fraud types

DECISION:
  Score 0-1 → threshold determines approve/review/decline
  Threshold tuned per merchant type, user segment, time of day
  Human review queue: 0.5-0.8 score range → analyst reviews within 1 hour

FEEDBACK LOOP:
  Chargebacks → confirmed fraud → add to training data
  False positives (customer calls to dispute) → retrain
  Model retrained weekly on 90-day rolling window
  Shadow models: new model runs silently, scores compared, A/B before deploy
```

---

## SECTION 5: SCALING GENAI APPLICATIONS — PATTERNS & ANTI-PATTERNS

### What interviewers ask about scaling specifically

```
Q: "Your RAG system works at 100 QPS. How do you scale to 10,000 QPS?"

A: Walk through each bottleneck:
   
   API Layer: stateless FastAPI behind K8s HPA → auto-scales on CPU/RPS
   
   Embedding bottleneck:
     → GPU-based embedding service (batch inference)
     → Cache embeddings for repeated queries (Redis, query fingerprint)
   
   Vector DB bottleneck:
     → Shard by tenant or document category
     → Horizontal read replicas (3-5 per shard)
     → HNSW parameters: increase ef for quality, decrease for speed
     → Pinecone/Weaviate: managed horizontal scaling
   
   LLM bottleneck:
     → Semantic cache → 30-40% hit rate → eliminates those LLM calls
     → vLLM continuous batching → 10-20× throughput vs naive
     → Model routing: 70% cheap model, 30% expensive model
     → Multiple regions for geographic load distribution
   
   Database bottleneck:
     → Read replicas for metadata queries
     → Redis cache for hot metadata (document titles, sources)

Q: "How do you handle a traffic spike (10× normal)?"
A: 
   Short-term (seconds): K8s HPA kicks in, scales pods → ~2 min
   Immediate: rate limiting kicks in, queue excess requests
   LLM: switch to cheaper/faster model temporarily, increase concurrency
   Cache: pre-warm cache for likely queries during low traffic periods
   Circuit breaker: if vector DB overwhelmed → fallback to BM25 only
   CDN: static/cached responses served from edge, not origin
```

---

## SECTION 6: KEY NUMBERS EVERY INTERVIEWER EXPECTS YOU TO KNOW

```
LLM COSTS (2025 approximate):
  GPT-4o:        $5/M input tokens,  $15/M output tokens
  GPT-4o-mini:   $0.15/M input,      $0.60/M output
  Claude Sonnet: $3/M input,         $15/M output
  Claude Haiku:  $0.25/M input,      $1.25/M output
  Gemini Pro:    $1.25/M input,      $5/M output
  text-embedding-3-small: $0.02/M tokens

VECTOR DB SIZING:
  1536-dim float32 = 6KB per vector
  1M vectors = 6GB raw; with HNSW index overhead = ~15GB
  100M vectors = ~150GB → needs Pinecone/Weaviate or distributed pgvector

LATENCY TARGETS (production):
  Embedding query: <20ms (GPU) or <100ms (CPU)
  Vector ANN search: <10ms for 10M vectors (HNSW ef=64)
  Re-ranker: 50-200ms (depends on batch size)
  LLM TTFT (time to first token): 300-800ms (GPT-4o), 200-500ms (Claude Haiku)
  End-to-end RAG: P95 < 3 seconds (target)

vLLM THROUGHPUT (H100 80GB):
  Llama-3-8B:  ~5,000 tokens/sec
  Llama-3-70B: ~500 tokens/sec (FP16); ~2,000 tokens/sec (INT4)
  Continuous batching: 10-20× better than naive single-request serving

SCALE MATH:
  1M req/day = 11.6 QPS avg = ~35 QPS peak (3×)
  100M req/day = 1,160 QPS avg = ~3,500 QPS peak
  1B req/day = 11,600 QPS avg = ~35,000 QPS peak

CHUNKING SWEET SPOT:
  Child chunks: 128-256 tokens (for precision retrieval)
  Parent chunks: 512-1024 tokens (for context returned to LLM)
  Overlap: 10-20% between chunks
  HNSW M=16, ef_construction=200, ef=64 → good balance quality vs speed
```

---

## SECTION 7: FOLLOW-UP QUESTIONS INTERVIEWERS ALWAYS ASK

| Follow-up | Strong Answer |
|-----------|--------------|
| "How do you handle context window overflow?" | Map-reduce summarization, parent-child chunks, hierarchical retrieval, longer context model (Gemini 1.5 Pro 1M tokens) |
| "How do you make RAG multilingual?" | Multilingual embedding model (mE5, multilingual-e5-large), translate queries to English or embed in native language |
| "What if your embedding model changes?" | Re-embed entire corpus with new model (costly but necessary), use embedding model versioning in metadata, migration script |
| "How do you handle tables in documents?" | Extract tables as markdown or JSON at ingestion time, store separately, retrieve by table type metadata filter |
| "How do you test agent systems?" | Unit test each tool, mock LLM responses, integration test full agent graph with deterministic seed, use evals on golden tasks |
| "How do you prevent prompt injection in agents?" | XML delimiters between user input and context, validate tool outputs before injecting back, secondary safety LLM, whitelist allowed tool calls |
| "How do you version LLM outputs for compliance?" | Immutable log: request, response, model_version, prompt_version, timestamp → append-only S3 or DynamoDB |
| "How do you do zero-downtime LLM model upgrade?" | Shadow mode first → blue-green deploy → canary (5% traffic) → full rollout with rollback |
| "How do you reduce LLM cost by 80%?" | Model routing (70% to cheap model) + semantic cache (30% hit rate) + prompt optimization + output token reduction = ~80% total |
| "What is GraphRAG? When would you use it?" | Microsoft's approach: extract knowledge graph from docs → use graph traversal + RAG together → better for multi-hop reasoning questions ("what connects X to Y?") |

---

*Sources: Glassdoor GenAI interview reports 2023-2025, LinkedIn interview posts, Blind, Reddit r/MachineLearning, r/cscareerquestions, Google SWE interview prep, Anthropic blog, OpenAI engineering blog, Chip Huyen "Designing ML Systems", Alex Xu "System Design Interview Vol 2", MLOps Community Slack*
