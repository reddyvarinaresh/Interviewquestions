# Most Asked Python Questions — 12 to 15 Years Experience
> Target: Principal / Architect / Staff Engineer | Enterprise, Healthcare, GenAI, Consulting
> Roles: Principal Engineer, Solutions Architect, Staff Engineer, Engineering Manager, AI Architect
> Sources: Glassdoor, LinkedIn, Reddit, Engineering Blogs, GenAI Interview Reports

---

## What Interviewers Expect at 12–15 Years

At this level, interviewers test:
- Enterprise architecture: microservices, distributed systems, event-driven, DDD
- Deep GenAI platform design: RAG, multi-agent systems, LLMOps, MCP
- Cross-functional leadership: influencing tech strategy, stakeholder management
- Platform thinking: building reusable platforms and developer tooling
- Risk & compliance: HIPAA, SOC2, AI governance, security architecture
- Breadth + depth: wide enough to guide teams; deep enough to debate tradeoffs
- Hiring and growing senior engineers
- Making build vs buy decisions at organizational scale

**Expected:** Own architectural decisions, define engineering standards, mentor staff+ engineers, drive platform strategy

---

## SECTION 1: Architecture & Platform Design (Q1–Q30)

### Q1
- **Question:** You are asked to build a GenAI platform for a healthcare insurance company processing 2M claims/month. Walk through your complete architecture.
- **Tier:** T1
- **Answer Points:**
  - **Data layer:** PostgreSQL (claims, members, providers), MongoDB (documents, clinical notes), Redis (caching, rate limiting), S3/Azure Blob (file storage)
  - **RAG pipeline:** Document ingestion → Azure Document Intelligence → chunking → embedding → Qdrant vector DB; nightly refresh pipeline
  - **Agent platform:** LangGraph multi-agent system; supervisor agent routes to specialized workers (eligibility, clinical, adjudication); PostgreSQL checkpointing; HITL gates for high-value claims
  - **API layer:** FastAPI with JWT auth, RBAC, rate limiting, idempotency; async endpoints for AI calls; streaming for real-time UI
  - **LLMOps:** Langfuse for observability; prompt registry; RAGAS evaluation pipeline; nightly quality scoring; cost controls per tenant
  - **Security:** PHI masking before LLM calls; Azure OpenAI (data residency); audit log immutable; SOC2 + HIPAA controls
  - **Infra:** Kubernetes (EKS/AKS), HPA on CPU + queue depth, canary deployments, GitOps with ArgoCD
  - **Observability:** OpenTelemetry → Grafana; LLM traces → Langfuse; business KPIs → BI dashboard

---

### Q2
- **Question:** How do you define service boundaries using DDD bounded contexts? Apply it to healthcare.
- **Tier:** T1
- **Answer Points:**
  - Identify core domains: Claims, Eligibility, Provider, Member, Authorization, Billing, Clinical
  - Each bounded context: owns its data model, its API, its database
  - Integration via domain events (Kafka): `ClaimSubmitted`, `EligibilityVerified`, `AuthorizationApproved`
  - Anti-corruption layer: translate between bounded contexts; don't let Claims model bleed into Billing
  - Team alignment: one team per bounded context (Conway's Law)
  - Shared kernel: only for truly shared concepts (Address, Money, Date range)

---

### Q3
- **Question:** How do you implement an event-driven architecture for claims processing? What guarantees do you need?
- **Tier:** T1
- **Answer Points:**
  - Kafka topics per business event type; partitioned by member_id for ordering
  - At-least-once delivery: idempotent consumers; deduplication by event_id
  - Outbox pattern: write event to DB table in same transaction as business write; Debezium CDC publishes to Kafka
  - Dead letter topics: failed messages after N retries; alerting + manual review
  - Schema registry: Avro schemas for event contracts; consumer must handle backward-compatible schema changes
  - Exactly-once where critical (financial): Kafka transactions + idempotent producers

---

### Q4–Q30 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 4 | What is the saga pattern? When do you choose orchestration vs choreography? | Architecture | T1 |
| 5 | How do you implement CQRS + event sourcing for a claims adjudication system? | Architecture | T2 |
| 6 | How do you migrate a 10-year-old monolith to microservices safely? | Architecture | T1 |
| 7 | What is a hexagonal architecture? How do you enforce it in a Python codebase? | Architecture | T2 |
| 8 | How do you design for multi-region deployment of a Python GenAI service? | Architecture | T2 |
| 9 | How do you implement zero-downtime database migrations at scale? | Database | T1 |
| 10 | How do you design a platform for 100 microservices with shared cross-cutting concerns? | Platform | T1 |
| 11 | What is a service mesh? When do you use Istio vs custom Python middleware? | Architecture | T2 |
| 12 | How do you implement a developer platform (internal PaaS) for Python microservices? | Platform | T2 |
| 13 | How do you design a Python service for 1M concurrent WebSocket connections? | Architecture | T2 |
| 14 | How do you implement a distributed tracing strategy across 20+ Python services? | Observability | T1 |
| 15 | How do you design an API gateway for a complex microservices landscape? | Architecture | T1 |
| 16 | How do you implement a global rate limiter that works across multiple K8s regions? | Design | T2 |
| 17 | How do you implement consistent hashing for cache routing across Python services? | Design | T2 |
| 18 | How do you design a data mesh architecture for a healthcare analytics platform? | Architecture | T2 |
| 19 | How do you implement a feature flag platform for 50+ Python services? | Platform | T2 |
| 20 | How do you design a secrets management system for a large Python platform? | Security | T1 |
| 21 | How do you implement chaos engineering for a Python microservices platform? | Reliability | T2 |
| 22 | How do you implement SLO-based alerting and error budgets for Python services? | Observability | T1 |
| 23 | How do you design a real-time fraud detection system for healthcare claims? | Architecture | T1 |
| 24 | How do you implement a Python platform for HIPAA-compliant AI processing? | Compliance | T1 |
| 25 | What is FinOps for a GenAI platform? How do you implement cost governance? | LLMOps | T1 |
| 26 | How do you design a Python-based document intelligence pipeline for insurance forms? | GenAI | T1 |
| 27 | How do you implement an AI governance framework for a production GenAI platform? | AI Governance | T1 |
| 28 | How do you implement a platform for A/B testing LLM prompts at scale? | LLMOps | T1 |
| 29 | How do you design a multi-agent platform that supports 100 concurrent agent runs? | GenAI | T1 |
| 30 | How do you implement responsible AI practices in a healthcare GenAI platform? | AI Governance | T1 |

---

## SECTION 2: GenAI Architecture Deep Dive (Q31–Q60)

### Q31
- **Question:** Design a multi-agent system for healthcare prior authorization. Include HITL, compliance, and scaling.
- **Tier:** T1
- **Answer Points:**
  - **Agent roles:** Intake (parse request), Eligibility (check coverage), Clinical (match criteria), Adjudicator (decision), Notification (communicate)
  - **Orchestration:** LangGraph supervisor routes between agents; PostgreSQL checkpoint persistence
  - **HITL:** Interrupt at adjudication for: amount > $50K, confidence < 0.7, rare diagnosis codes; medical director review via web portal
  - **Compliance:** All agent steps logged to immutable audit log; LLM decisions include reasoning trace; PHI masked before LLM calls
  - **Scaling:** Kubernetes; HPA on Celery queue depth; max 100 concurrent workflows; per-tenant rate limiting
  - **SLA:** < 4 hours for auto-adjudicated; < 24 hours for HITL cases; pager alert if SLA at risk

---

### Q32
- **Question:** How do you implement LLMOps for an enterprise platform serving 20 teams?
- **Tier:** T1
- **Answer Points:**
  - **Prompt registry:** centralized versioned prompt store; teams reference by name+version; rollback capability
  - **Observability:** Langfuse per-team workspaces; aggregate view for platform team; cost attribution per team/use case
  - **Evaluation:** shared evaluation harness (RAGAS + LLM-judge); each team maintains golden dataset; nightly CI evaluation runs
  - **Model governance:** approved model list; new model onboarding process; security review (data residency, HIPAA compliance)
  - **Cost control:** per-team token budgets; alerts at 80%/100% of budget; auto-fallback to cheaper model at budget cap
  - **CI/CD:** prompt changes require evaluation gate; 5% canary for new agent versions; automatic rollback on quality degradation

---

### Q33–Q60 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 33 | How do you design a RAG system for 10M+ documents with multi-tenant access control? | GenAI | T1 |
| 34 | How do you implement a real-time re-indexing pipeline for a production RAG system? | GenAI | T1 |
| 35 | How do you implement GraphRAG for a healthcare knowledge graph? | GenAI | T2 |
| 36 | How do you design an MCP server ecosystem for enterprise AI agents? | MCP | T1 |
| 37 | How do you implement a semantic router that scales to 50 specialized agents? | GenAI | T1 |
| 38 | How do you implement a shadow deployment for a new LLM model before production rollout? | LLMOps | T1 |
| 39 | How do you implement human feedback loops to continuously improve LLM quality? | LLMOps | T1 |
| 40 | How do you design a GenAI platform that works with local LLMs (no cloud dependency)? | GenAI | T2 |
| 41 | How do you implement prompt injection prevention for enterprise AI agents? | Security | T1 |
| 42 | How do you implement a data masking service for PHI before LLM processing? | Compliance | T1 |
| 43 | How do you implement auditability for all AI-driven decisions in healthcare? | Compliance | T1 |
| 44 | How do you implement a fine-tuning pipeline for a domain-specific LLM? | GenAI | T2 |
| 45 | How do you implement a reranking service for a production RAG pipeline? | GenAI | T1 |
| 46 | How do you implement context-aware caching for LLM calls at scale? | LLMOps | T1 |
| 47 | How do you design an evaluation framework for multi-step agentic workflows? | LLMOps | T1 |
| 48 | How do you implement a LangGraph workflow that processes FHIR R4 data? | GenAI | T2 |
| 49 | How do you implement a clinical decision support agent using RAG + rules engine? | GenAI | T1 |
| 50 | How do you design a GenAI platform that handles 10K concurrent LLM calls? | GenAI | T1 |
| 51 | How do you implement continuous model monitoring for production LLM applications? | LLMOps | T1 |
| 52 | How do you implement a Python service that orchestrates both LLMs and traditional ML? | GenAI | T1 |
| 53 | How do you implement an LLM gateway with multi-provider load balancing? | LLMOps | T1 |
| 54 | How do you implement AI red-teaming for a healthcare GenAI platform? | Security | T1 |
| 55 | How do you design a knowledge graph-backed RAG for clinical decision making? | GenAI | T2 |
| 56 | How do you implement an agentic ETL pipeline for complex document processing? | GenAI | T1 |
| 57 | How do you implement a Python platform for synthetic data generation using LLMs? | GenAI | T2 |
| 58 | How do you design a compliance monitoring agent for healthcare data? | GenAI | T1 |
| 59 | How do you implement a multi-modal AI pipeline (text + image + structured data)? | GenAI | T2 |
| 60 | How do you implement a Python AI orchestration platform for a Fortune 500 company? | GenAI | T1 |

---

## SECTION 3: Leadership, Strategy & Scenario (Q61–Q100)

| # | Question | Focus | Tier |
|---|----------|-------|------|
| 61 | How do you build a technical roadmap for a Python GenAI platform for the next 18 months? | Strategy | T1 |
| 62 | How do you make a build vs buy decision for an AI capability? | Strategy | T1 |
| 63 | How do you establish Python coding standards across 10+ teams? | Leadership | T1 |
| 64 | How do you manage technical debt while delivering new features at pace? | Leadership | T1 |
| 65 | How do you handle disagreement with engineering leadership on architectural direction? | Leadership | T1 |
| 66 | How do you mentor a mid-level engineer toward a principal role? | Leadership | T1 |
| 67 | How do you run an architecture review for a new Python service? | Leadership | T1 |
| 68 | How do you define and measure engineering excellence for a Python platform team? | Leadership | T1 |
| 69 | How do you handle a critical production outage at 3am? Walk through your process. | Incident | T1 |
| 70 | How do you conduct a blameless post-mortem for a major incident? | Incident | T1 |
| 71 | How do you evaluate whether to adopt a new Python framework (e.g., migrate from Flask to FastAPI)? | Strategy | T1 |
| 72 | How do you align engineering priorities with product and business stakeholders? | Leadership | T1 |
| 73 | How do you implement a platform security review process for Python services? | Security | T1 |
| 74 | How do you design a Python services reliability framework (SLO, error budget, alert)? | Reliability | T1 |
| 75 | Describe the largest Python system you've designed. What would you do differently? | Experience | T1 |
| 76 | How do you implement a data governance policy for a GenAI platform? | Governance | T1 |
| 77 | How do you communicate architectural decisions to non-technical stakeholders? | Leadership | T1 |
| 78 | How do you implement a Python microservices onboarding template for new teams? | Platform | T1 |
| 79 | How do you handle a vendor lock-in risk for an LLM provider? | Strategy | T1 |
| 80 | How do you implement an internal developer platform for Python GenAI teams? | Platform | T1 |
| 81 | How do you evaluate the ROI of a GenAI investment for a business stakeholder? | Strategy | T1 |
| 82 | How do you implement change management for a major Python platform migration? | Leadership | T1 |
| 83 | How do you build a culture of observability and production ownership in a Python team? | Leadership | T1 |
| 84 | How do you implement a Python service catalog for a large engineering organization? | Platform | T1 |
| 85 | How do you hire senior Python engineers? What do you look for? | Leadership | T1 |
| 86 | How do you design an internship program that produces production Python contributions? | Leadership | T2 |
| 87 | How do you implement a Python platform that meets HIPAA, SOC2, and GDPR simultaneously? | Compliance | T1 |
| 88 | How do you implement a cost optimization strategy for a $500K/month cloud Python platform? | FinOps | T1 |
| 89 | How do you implement a disaster recovery plan for a Python GenAI platform? | Reliability | T1 |
| 90 | How do you implement a security incident response plan for an AI platform? | Security | T1 |
| 91 | How do you implement an engineering OKR framework for a Python platform team? | Leadership | T1 |
| 92 | What is your philosophy on microservices vs modular monolith for a new product? | Strategy | T1 |
| 93 | How do you implement a tech radar for a Python engineering organization? | Strategy | T2 |
| 94 | How do you handle a situation where a junior engineer's code would cause a production risk? | Leadership | T1 |
| 95 | How do you implement a GenAI ethics review board for healthcare AI decisions? | Governance | T1 |
| 96 | How do you measure the business impact of a GenAI feature you shipped? | Strategy | T1 |
| 97 | Describe a time you reversed a bad architectural decision. What was the impact? | Experience | T1 |
| 98 | How do you implement a Python platform that supports 200 concurrent agent workflows 24/7? | Architecture | T1 |
| 99 | How do you design a GenAI Center of Excellence for a 5000-person enterprise? | Strategy | T1 |
| 100 | You are the lead architect for a healthcare AI company. Design the entire Python GenAI platform from scratch: microservices, RAG, multi-agent, LLMOps, HIPAA compliance, CI/CD, and team structure. | Architecture | T1 |
