# Must Prepare First — Top Priority Python Interview Questions
> For: Senior / Lead / Architect Roles | 8–15 Years Experience
> These are the questions that appear in EVERY interview at consulting, healthcare, and GenAI companies.
> If you only have 2 weeks to prepare, master these first.

---

## HOW TO USE THIS FILE

This file is your **triage guide**. It distills the highest-frequency, highest-signal questions from all 27 files into one prioritized list.

**Tier Legend:**
- 🔴 **MUST KNOW** — Asked in 80%+ of senior Python interviews; failing these is a red flag
- 🟡 **HIGH VALUE** — Asked in 50–80% of interviews at mid/senior level; strong differentiators
- 🟢 **BONUS** — Asked at architect/principal level; shows depth; good-to-know for seniors

---

## PART 1: Python Core — Must Know 🔴

These are asked in virtually every Python technical interview regardless of company or role.

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | List vs Tuple vs Set vs Dict — when to use each? | Absolute baseline; tests understanding of Python data model |
| 2 | Mutable vs immutable — consequences in function args, dict keys, default params | Exposes Python footguns; tells if you've been burned in production |
| 3 | What is a generator? How does `yield` differ from `return`? Write a lazy file reader. | Memory efficiency; essential for production data pipelines |
| 4 | What is a decorator? Write a retry decorator. | Core Python; used in every production codebase |
| 5 | What is the GIL? When do you use threading vs multiprocessing vs asyncio? | Top interview question at every level 5+ yrs; reveals concurrency understanding |
| 6 | What is `asyncio`? Write an async producer-consumer. | Essential for modern FastAPI/async Python services |
| 7 | What is `functools.lru_cache`? How does it work? | Performance optimization; used constantly |
| 8 | What is a context manager? Write one that manages a DB transaction. | Used in every real application |
| 9 | Explain LEGB scope. What is a closure? | Classic interview; tests deep Python understanding |
| 10 | What is `*args`/`**kwargs`? What is unpacking? | Used daily; tests Pythonic fluency |
| 11 | What is `__slots__`? When do you use it? | Memory optimization; signals production experience |
| 12 | What is the difference between `deepcopy` and shallow copy? | Common bug source; asked at all levels |
| 13 | How does Python's memory management work? (reference counting + cyclic GC) | Asked at 8+ yrs; key internals question |
| 14 | What is `dataclass`? How does it compare to Pydantic? | Asked constantly; shows modern Python |
| 15 | What is `typing.Protocol`? What is structural subtyping? | Senior-level; shows advanced type system knowledge |

---

## PART 2: FastAPI & REST API — Must Know 🔴

Every backend Python role tests this regardless of company type.

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | How does FastAPI's `Depends()` work? Implement a DB session dependency. | Fundamental FastAPI pattern; asked at every level |
| 2 | How do you implement JWT authentication in FastAPI with dependency injection? | Security baseline; every API needs auth |
| 3 | How does Pydantic v2 differ from v1? What are the migration challenges? | Breaking changes; asked at every company still on v1 |
| 4 | How do you implement rate limiting in FastAPI with Redis? | Production requirement; reveals Redis + middleware knowledge |
| 5 | How do you implement pagination (offset and cursor-based) in FastAPI? | Every API has lists; tests API design maturity |
| 6 | What is idempotency? Implement idempotency keys for a POST endpoint. | Critical for financial/healthcare APIs; differentiator question |
| 7 | How do you implement middleware in FastAPI? Write a request timing + logging middleware. | Production observability; asked at every senior interview |
| 8 | How do you test a FastAPI endpoint with mocked dependencies? | Testing is core to senior roles; `app.dependency_overrides` pattern |
| 9 | How do you implement API versioning in FastAPI? | Architecture maturity signal |
| 10 | How do you implement streaming responses in FastAPI for LLM output? | GenAI + FastAPI convergence; asked at most AI companies |

---

## PART 3: SQL & Database — Must Know 🔴

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | What is the N+1 problem? How do you detect and fix it in SQLAlchemy? | Most common ORM bug; asked at every company |
| 2 | How does SQLAlchemy connection pooling work? What settings matter? | Production DB operations; critical for scaling |
| 3 | What is a window function? Write `RANK()`, `LAG()`, `ROW_NUMBER()` examples. | Analytics queries; asked at data-heavy companies |
| 4 | What is a CTE? How does it differ from a subquery? | Cleaner SQL; signal of SQL maturity |
| 5 | How do you bulk insert 1M records efficiently from Python into PostgreSQL? | Data engineering baseline |
| 6 | How do you implement transactions + savepoints in SQLAlchemy? | Data integrity; healthcare/finance essential |
| 7 | How do you implement optimistic locking with a version column? | Concurrent writes; common in enterprise apps |
| 8 | How do you run Alembic migrations safely in production? | DevOps + DB maturity; asked constantly |
| 9 | How does `pool_pre_ping` work? Why is it important in cloud environments? | Production DB ops; signals real-world experience |
| 10 | What is `EXPLAIN ANALYZE`? How do you use it to optimize a slow query? | Query optimization; asked at data companies |

---

## PART 4: Redis, Caching & Messaging — Must Know 🔴

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | How do you implement a Redis-backed function cache with TTL? | Ubiquitous pattern; performance optimization |
| 2 | How do you implement a distributed lock with Redis Lua script? | Concurrency in distributed systems; asked at 8+ yrs |
| 3 | What is cache stampede? How do you prevent it? | Production pitfall; differentiator question |
| 4 | How do you implement a sliding window rate limiter with Redis sorted sets? | Security + performance; asked at API-heavy companies |
| 5 | What Redis data structures do you use? Use case for each (String, Hash, List, Set, ZSet, Stream)? | Redis depth check; asked at all levels |
| 6 | How do you implement a task queue using Redis Lists vs Celery? | Architecture decision; asked at all backend companies |

---

## PART 5: System Design — Must Know 🔴

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | Design a claims processing API with auth, validation, idempotency, and audit logging. | Healthcare standard system design |
| 2 | Design a notification service (email/SMS/push) from events. | Classic async design question |
| 3 | What is microservices vs monolith? How do you decide? | Architecture maturity; always asked at senior level |
| 4 | What is the circuit breaker pattern? How does it differ from retry? | Resilience; asked at every production-minded company |
| 5 | What is the repository pattern? Implement it in Python. | Clean architecture; asked at enterprise companies |
| 6 | What is CQRS? When do you use it? | Architect-level; signals advanced design knowledge |
| 7 | How do you design for zero-downtime deployments of a Python service? | DevOps maturity; asked at all cloud-native companies |
| 8 | How do you add observability (logging, metrics, tracing) to a Python microservice? | Production engineering; always asked at senior level |

---

## PART 6: GenAI — Must Know 🔴 (for AI roles)

These are mandatory for any GenAI Engineer / AI Architect / LLM Engineer role.

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | What is RAG? Explain the full pipeline end-to-end. | Baseline for every GenAI role |
| 2 | What chunking strategies do you know? How do you choose? | Core RAG design decision |
| 3 | What is hybrid search? How does dense + sparse + RRF work? | Differentiates senior RAG engineers |
| 4 | What is reranking? How does a cross-encoder differ from a bi-encoder? | Production RAG quality improvement |
| 5 | How do you implement structured output from an LLM using Pydantic? | Essential for any production LLM service |
| 6 | What is function/tool calling in LLMs? Implement a ReAct agent. | Agentic AI baseline |
| 7 | What is LangGraph? How does it differ from LangChain chains? | Asked at every LangGraph shop |
| 8 | How do you implement HITL in a LangGraph workflow? | Production agentic AI; critical for regulated industries |
| 9 | How do you track LLM costs and control per-user spend? | LLMOps baseline; every company cares about cost |
| 10 | How do you evaluate a RAG pipeline? What is RAGAS? | Quality engineering for LLMs |
| 11 | What is the MCP (Model Context Protocol)? Build a simple MCP server. | Fast-growing ecosystem; asked in 2025 interviews |
| 12 | How do you implement a HIPAA-compliant LLM pipeline? | Healthcare AI mandatory |

---

## PART 7: Production Engineering — High Value 🟡

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | How do you find a memory leak in a Python service? (`tracemalloc`, `py-spy`) | Production ops; differentiates seniors |
| 2 | How do you profile a FastAPI service that has high P99 latency? | Performance engineering |
| 3 | Walk me through your RCA process for a production 500 error spike. | Incident management; leadership signal |
| 4 | How do you implement structured logging with correlation IDs in Python? | Observability; asked at all cloud-native companies |
| 5 | How do you implement OpenTelemetry tracing in a Python microservice? | Distributed tracing; critical for microservices |
| 6 | How do you implement graceful shutdown for a FastAPI service in Kubernetes? | K8s maturity; always asked at cloud companies |
| 7 | How do you write a production Dockerfile for a Python FastAPI service? | DevOps baseline; asked at every cloud company |
| 8 | How do you implement K8s liveness vs readiness probes in FastAPI? | K8s production ops; asked at every K8s shop |

---

## PART 8: Cloud & DevOps — High Value 🟡

| # | Question | Why It's Asked |
|---|----------|----------------|
| 1 | How does `boto3` work? Give examples for S3, SQS, DynamoDB, Secrets Manager. | AWS standard; asked at every AWS shop |
| 2 | How do you process SQS events in Lambda with partial batch failure? | Lambda + SQS standard pattern |
| 3 | How do you use `DefaultAzureCredential` in Python? | Azure standard; asked at every Azure shop |
| 4 | How do you set up CI/CD for a Python service with GitHub Actions? | DevOps baseline for senior engineers |
| 5 | How do you implement secret management in K8s for a Python service? | Security + K8s ops; always asked |
| 6 | How do you implement canary deployments for a Python service? | Release engineering maturity |

---

## PART 9: Healthcare-Specific — Bonus 🟢

Only required for healthcare/insurance roles but very high signal if you know these.

| # | Question |
|---|----------|
| 1 | What is HIPAA? What is PHI? How do you handle it in Python? |
| 2 | What is FHIR? How do you model FHIR resources in Python with Pydantic? |
| 3 | How do you implement an eligibility verification service in Python? |
| 4 | How do you implement a prior authorization workflow with Python agents? |
| 5 | How do you implement automated medical coding (ICD/CPT) using LLMs? |
| 6 | How do you build a HIPAA-compliant RAG system for clinical documentation? |
| 7 | How do you implement a claims fraud detection rule engine? |
| 8 | How do you implement audit logging that meets HIPAA audit control requirements? |

---

## PART 10: 30-Day Preparation Plan

### Week 1 — Python Core + FastAPI
- Day 1–2: Core Python (Parts 1); practice coding questions from `03_top100_core_python.md`
- Day 3–4: Advanced Python (decorators, generators, asyncio); practice from `04_top100_advanced_python.md`
- Day 5–7: FastAPI + Pydantic + REST API design; practice from `10_top100_fastapi_api.md`

### Week 2 — Database + Production Engineering
- Day 8–9: SQL (window functions, CTEs, query optimization); practice from `11_top100_sql_mongo_redis.md`
- Day 10–11: SQLAlchemy (N+1, pooling, transactions, Alembic migrations)
- Day 12–13: Redis (caching, distributed locks, rate limiting, pub/sub)
- Day 14: Production debugging and observability; practice from `09_top100_production_debugging.md`

### Week 3 — System Design + Cloud
- Day 15–16: System design patterns (repository, CQRS, saga, circuit breaker); from `12_top100_architecture.md`
- Day 17–18: Microservices design; practice 3 full system design questions out loud
- Day 19–20: Docker + Kubernetes + CI/CD; practice from `13_top100_cloud_devops.md`
- Day 21: AWS/Azure SDK practice with `boto3` and `azure-sdk-for-python`

### Week 4 — GenAI (if applying to GenAI roles)
- Day 22–23: LLM APIs, structured output, streaming, tool calling; from `15_top100_genai.md`
- Day 24–25: RAG pipeline end-to-end; hybrid search, reranking, RAGAS; from `16_top100_rag.md`
- Day 26–27: LangGraph agents, HITL, checkpointing; from `17_top100_agentic_ai.md` + `18_top100_langchain_langgraph.md`
- Day 28–29: LLMOps, MCP, cost tracking, evaluation pipelines; from `19_top100_mcp.md` + `20_top100_llmops.md`
- Day 30: Full mock interview — system design (GenAI) + 3 coding questions + 2 behavioral

---

## QUICK REFERENCE: Most Common Coding Questions

These are the coding questions asked most frequently in live interview sessions (30–45 min). Practice until you can solve each in < 15 minutes.

### Must Solve Cold
```
1. Two Sum (using dict for O(n))
2. Retry decorator with exponential backoff
3. Timing decorator
4. Async fetch N URLs with semaphore (max concurrent)
5. Chunk a list into batches of size N
6. Flatten a nested list (recursive)
7. Remove duplicates preserving order
8. Word frequency counter (Counter)
9. Deep merge two dicts
10. Safe nested dict getter by dot-path
11. Sort list of dicts by multiple keys
12. Producer-consumer with asyncio.Queue
13. Thread-safe counter with Lock
14. LRU cache from scratch
15. Generator that lazily reads a large file line by line
```

### Important to Know
```
16. Circuit breaker class
17. Rate limiter class (token bucket)
18. Redis-based cache decorator with TTL
19. SQLAlchemy N+1 fix with joinedload
20. FastAPI endpoint with JWT auth + RBAC
21. Paginated FastAPI response with total count
22. Bulk insert 1M records to PostgreSQL
23. Distributed lock with Redis Lua
24. Parse and validate a large CSV in chunks
25. Async context manager for timing code blocks
```

---

## Behavioral Questions You Must Prepare

| # | Question | What They're Testing |
|---|----------|---------------------|
| 1 | Describe the most complex Python system you've built. | Technical depth + ownership |
| 2 | Tell me about a production incident you handled. | Incident management + RCA |
| 3 | How do you mentor junior engineers? | Leadership signal |
| 4 | How do you handle technical debt under delivery pressure? | Engineering maturity |
| 5 | Describe a time you disagreed with a technical decision. What happened? | Courage + collaboration |
| 6 | How do you decide when to use a microservice vs extend the monolith? | Architecture judgment |
| 7 | What's the most impactful technical decision you've made? | Judgment + impact |
| 8 | How do you stay current with Python and GenAI ecosystem changes? | Growth mindset |

---

> **Final Tip:** In interviews, always explain your reasoning before and while you code. Senior interviewers care more about *how you think* than whether you finish the code perfectly. Ask clarifying questions. Mention tradeoffs. Acknowledge edge cases.
