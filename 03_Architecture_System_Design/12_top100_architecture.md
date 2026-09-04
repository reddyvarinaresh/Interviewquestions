# Top 100 Architecture / System Design Interview Questions (Python Focus)
> Focus: 8–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise, GenAI
> Sources: Glassdoor, LinkedIn, Reddit, Medium, Thoughtworks Blog

---

## SECTION 1: Microservices & Distributed Systems (Q1–Q30)

### Q1
- **Question:** What is microservices architecture? What are its tradeoffs vs a monolith?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Microservices pros:** Independent deployment, tech diversity, fault isolation, team autonomy, independent scaling
  - **Microservices cons:** Network overhead, distributed tracing complexity, data consistency challenges, operational overhead (K8s, CI/CD per service), testing harder
  - **Monolith pros:** Simpler development, easier debugging, no network calls between modules, single deployment
  - **When monolith is better:** early-stage startup, small team, tight latency requirements, simple domain
  - **Sweet spot:** Modular monolith first → extract services when pain is felt
- **Architect Answer:** Conway's Law: system design mirrors org structure; align service boundaries with team boundaries
- **Difficulty:** Medium

---

### Q2
- **Question:** How do you define service boundaries in a microservices architecture?
- **Level:** 8–12 Yrs | **Company:** Thoughtworks, Accenture, Deloitte | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Domain-Driven Design (DDD): bounded contexts
  - Single Responsibility: each service owns one business capability
  - Database per service: no shared databases
  - High cohesion within service; loose coupling between services
  - Avoid chatty inter-service calls — if two services talk constantly, consider merging them
- **Real Example:** Healthcare: separate services for Claims, Eligibility, Provider, Member, Auth — each with own DB and API
- **Difficulty:** Hard

---

### Q3
- **Question:** How do services communicate in microservices? Compare synchronous vs asynchronous.
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Synchronous (REST/gRPC):** immediate response; tight coupling; cascading failures
  - **Asynchronous (Kafka/RabbitMQ):** decoupled; better resilience; eventual consistency; harder to debug
  - **When sync:** user-facing real-time responses, queries
  - **When async:** long-running processes, notifications, event propagation, batch processing
  - **Pattern:** sync for reads, async for writes/events (CQRS)
- **Difficulty:** Medium

---

### Q4
- **Question:** What is the Saga pattern? How do you implement it for distributed transactions?
- **Level:** 12–15 Yrs | **Company:** Enterprise Architect | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Long-lived transaction split into sequence of local transactions
  - Each step publishes event; next step triggered by event
  - **Choreography:** each service listens to events and reacts (decentralized)
  - **Orchestration:** central saga orchestrator coordinates steps (LangGraph is perfect for this)
  - Compensating transactions for rollback: if step 3 fails, run compensate-step-2, compensate-step-1
- **Real Example:** Claim submission: Submit → Validate → Eligibility Check → Adjudicate → Pay
- **Difficulty:** Hard

---

### Q5
- **Question:** What is the strangler fig pattern? How do you use it to migrate from monolith to microservices?
- **Level:** 12–15 Yrs | **Company:** Thoughtworks, Deloitte, Accenture | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Route traffic through a facade/API gateway
  - Gradually extract modules as independent services behind the facade
  - New features go into microservices; old code stays until replaced
  - No big-bang rewrite; safe, incremental migration
  - Test each extracted service before cutting over
- **Difficulty:** Medium

---

### Q6
- **Question:** What is CQRS? How do you implement it in Python?
- **Level:** 12–15 Yrs | **Company:** Enterprise Architect | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Command Query Responsibility Segregation
  - Separate models for reads (queries) and writes (commands)
  - Write model optimized for consistency; read model optimized for query performance
  - Often combined with event sourcing
  - Python: separate `CommandService` and `QueryService` classes; different DB schemas
- **Real Example:** Claims: write to normalized PostgreSQL; read from denormalized MongoDB or Redis projection
- **Difficulty:** Hard

---

### Q7
- **Question:** What is event sourcing? How does it differ from traditional CRUD?
- **Level:** 12–15 Yrs | **Company:** Enterprise Architect | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Instead of storing current state, store ordered sequence of events
  - Current state = replay all events
  - Benefits: full audit trail, temporal queries, easy undo, CQRS-friendly
  - Challenges: event schema evolution, eventual consistency, query complexity
  - Python: use Kafka or EventStoreDB; Pydantic models for event schemas
- **Difficulty:** Hard

---

### Q8
- **Question:** What is the API gateway pattern? What responsibilities does it have?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Single entry point for all clients
  - Responsibilities: routing, auth, rate limiting, SSL termination, request transformation, load balancing, response caching
  - Options: AWS API Gateway, Kong, Nginx, Traefik
  - Python BFF (Backend for Frontend): FastAPI acting as API gateway for specific client type
- **Difficulty:** Medium

---

### Q9
- **Question:** What is a service mesh? What does Istio provide that a Python service can't do itself?
- **Level:** 12–15 Yrs | **Company:** Architect | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Handles: mutual TLS between services, distributed tracing, load balancing, circuit breaking, retries — all at infrastructure level
  - Python service doesn't need to implement these — sidecar proxy (Envoy) handles it
  - Observability: Istio injects trace headers automatically
  - When to use: large service mesh (10+ services); when you don't want each service implementing its own resilience
- **Difficulty:** Hard

---

### Q10
- **Question:** How do you design for high availability in a Python microservice?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Multiple replicas behind load balancer
  - Health checks: liveness (is process alive) + readiness (is service ready to serve)
  - Circuit breakers for downstream dependencies
  - Graceful shutdown: drain in-flight requests before stopping
  - Database connection pooling + retry
  - Stateless services (state in Redis/DB, not in-process)
  - Pod disruption budgets in Kubernetes
- **Difficulty:** Medium

---

### Q11–Q30 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 11 | What is the circuit breaker pattern? How does it differ from retry? | 8–12 Yrs | High | T1 |
| 12 | How do you implement the bulkhead pattern in Python? | 8–12 Yrs | Medium | T2 |
| 13 | What is the outbox pattern? How does it ensure event delivery? | 12–15 Yrs | Medium | T2 |
| 14 | How do you handle eventual consistency in a distributed system? | 8–12 Yrs | High | T1 |
| 15 | What is a distributed lock? When do you need one? | 8–12 Yrs | High | T1 |
| 16 | How do you implement a sidecar pattern in Python microservices? | 12–15 Yrs | Low | T3 |
| 17 | What is the ambassador pattern? Give a Python example. | 12–15 Yrs | Low | T3 |
| 18 | How does service discovery work in Kubernetes for Python services? | 8–12 Yrs | High | T1 |
| 19 | What is a queue-based load leveling pattern? When is it used? | 8–12 Yrs | High | T1 |
| 20 | How do you design a Python service for zero-downtime deployments? | 8–12 Yrs | High | T1 |
| 21 | What is CAP theorem? How does it affect your database choice? | 8–12 Yrs | Medium | T2 |
| 22 | What is BASE vs ACID? When do you accept eventual consistency? | 8–12 Yrs | Medium | T2 |
| 23 | How do you implement leader election among Python service instances? | 12–15 Yrs | Low | T3 |
| 24 | What is consistent hashing? How do you use it in Python for cache routing? | 12–15 Yrs | Medium | T2 |
| 25 | How do you implement a publish-subscribe system in Python? | 8–12 Yrs | High | T1 |
| 26 | How do you design a data pipeline architecture for a healthcare platform? | 8–12 Yrs | High | T1 |
| 27 | What is the difference between orchestration and choreography in microservices? | 12–15 Yrs | High | T1 |
| 28 | How do you implement multi-region deployment for a Python service? | 12–15 Yrs | Medium | T2 |
| 29 | How do you handle schema compatibility between producer and consumer in Kafka? | 8–12 Yrs | High | T1 |
| 30 | What is the two-phase commit protocol? Why is it rarely used in microservices? | 12–15 Yrs | Low | T3 |

---

## SECTION 2: Design Patterns (Q31–Q55)

### Q31
- **Question:** What is the Repository pattern? Implement it for a claims service in Python.
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from abc import ABC, abstractmethod
  from typing import Optional

  class ClaimRepository(ABC):
      @abstractmethod
      async def get(self, claim_id: str) -> Optional[Claim]: ...

      @abstractmethod
      async def save(self, claim: Claim) -> Claim: ...

      @abstractmethod
      async def find_by_member(self, member_id: str) -> list[Claim]: ...

  class PostgresClaimRepository(ClaimRepository):
      def __init__(self, session: AsyncSession):
          self.session = session

      async def get(self, claim_id: str) -> Optional[Claim]:
          result = await self.session.execute(
              select(ClaimModel).where(ClaimModel.claim_id == claim_id)
          )
          model = result.scalar_one_or_none()
          return Claim.from_orm(model) if model else None

      async def save(self, claim: Claim) -> Claim:
          model = ClaimModel(**claim.dict())
          self.session.add(model)
          await self.session.flush()
          return claim
  ```
- **Follow-Ups:** How do you test with a mock repository? How does this help unit testing?
- **Difficulty:** Medium

---

### Q32
- **Question:** What is the Unit of Work pattern? How does it combine with the Repository pattern?
- **Level:** 12–15 Yrs | **Company:** Architect | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Groups multiple repository operations into a single transaction
  - Manages the session/transaction lifecycle
  - `async with UnitOfWork() as uow: uow.claims.save(claim); uow.audit.log(event)` — both in same transaction
  - Prevents partial updates across repositories
- **Difficulty:** Hard

---

### Q33
- **Question:** What is the Factory pattern? Implement one for creating different claim processors.
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  class ClaimProcessor(ABC):
      @abstractmethod
      async def process(self, claim: Claim) -> ClaimResult: ...

  class MedicalClaimProcessor(ClaimProcessor):
      async def process(self, claim: Claim) -> ClaimResult: ...

  class DentalClaimProcessor(ClaimProcessor):
      async def process(self, claim: Claim) -> ClaimResult: ...

  class ClaimProcessorFactory:
      _processors = {
          "medical": MedicalClaimProcessor,
          "dental": DentalClaimProcessor,
          "vision": VisionClaimProcessor,
      }

      @classmethod
      def create(cls, claim_type: str) -> ClaimProcessor:
          processor_class = cls._processors.get(claim_type)
          if not processor_class:
              raise ValueError(f"Unknown claim type: {claim_type}")
          return processor_class()
  ```
- **Difficulty:** Easy

---

### Q34
- **Question:** What are the SOLID principles? Give a Python example for each.
- **Level:** 8–12 Yrs | **Company:** All enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **S** (Single Responsibility): `ClaimValidator` only validates; `ClaimPersister` only saves
  - **O** (Open/Closed): abstract `ClaimProcessor` extended for each type; never modify base
  - **L** (Liskov Substitution): any subclass of `ClaimRepository` can replace parent in tests
  - **I** (Interface Segregation): `ReadableRepository` and `WritableRepository` separate from one fat interface
  - **D** (Dependency Inversion): `ClaimService` depends on `ClaimRepository` interface, not PostgreSQL class
- **Difficulty:** Medium

---

### Q35–Q55 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 35 | What is the Observer pattern? Implement a domain event system in Python. | 8–12 Yrs | High | T1 |
| 36 | What is the Strategy pattern? Implement different pricing strategies. | 8–12 Yrs | High | T1 |
| 37 | What is the Adapter pattern? When do you use it in Python? | 5–8 Yrs | High | T1 |
| 38 | What is the Decorator pattern (GoF)? How does it differ from Python `@decorator`? | 8–12 Yrs | Medium | T2 |
| 39 | What is the Command pattern? How does it relate to undo/redo? | 8–12 Yrs | Medium | T2 |
| 40 | What is the Mediator pattern? How does it reduce coupling? | 8–12 Yrs | Medium | T2 |
| 41 | What is the Template Method pattern? Give a Python pipeline example. | 8–12 Yrs | Medium | T2 |
| 42 | What is the Proxy pattern? Implement a caching proxy in Python. | 8–12 Yrs | Medium | T2 |
| 43 | What is the Chain of Responsibility pattern? Implement a validation pipeline. | 8–12 Yrs | High | T1 |
| 44 | What is dependency injection? How do you implement a DI container in Python? | 8–12 Yrs | High | T1 |
| 45 | What is domain-driven design (DDD)? What are entities, value objects, aggregates? | 12–15 Yrs | High | T1 |
| 46 | What is a bounded context in DDD? How do you define one? | 12–15 Yrs | Medium | T2 |
| 47 | What is an aggregate root in DDD? Give a healthcare example. | 12–15 Yrs | Medium | T2 |
| 48 | What is the specification pattern? Implement it for claim filtering rules. | 12–15 Yrs | Medium | T2 |
| 49 | What is the outbox pattern? How does it guarantee event delivery? | 12–15 Yrs | Medium | T2 |
| 50 | What is the Facade pattern? Implement it for a healthcare API gateway. | 8–12 Yrs | Medium | T2 |
| 51 | What is a plugin architecture? How do you implement one in Python? | 12–15 Yrs | Medium | T2 |
| 52 | What is feature toggles/flags? How do you implement them in Python? | 8–12 Yrs | High | T1 |
| 53 | How do you implement event-driven domain events in a Python service? | 8–12 Yrs | High | T1 |
| 54 | What is the difference between domain model and data model? | 8–12 Yrs | High | T1 |
| 55 | What is a hexagonal (ports and adapters) architecture? How do you implement it? | 12–15 Yrs | High | T1 |

---

## SECTION 3: System Design Scenarios (Q56–Q100)

### Q56
- **Question:** Design a healthcare claims processing system in Python. Walk through the architecture.
- **Level:** 12–15 Yrs | **Company:** Healthcare, Optum, Carelon, Cigna | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Ingestion:** FastAPI REST API for claim submission + Kafka for async processing
  - **Validation:** Python service validates schema (Pydantic), business rules (rule engine)
  - **Eligibility:** async call to eligibility service; circuit breaker for failures
  - **Adjudication:** domain logic service (claim rules, fee schedule lookup)
  - **Storage:** PostgreSQL for normalized claims; MongoDB for documents; Redis for caching
  - **Events:** Kafka topics for each stage; downstream consumers for notifications, analytics
  - **Observability:** OpenTelemetry traces + Prometheus metrics + structured logging
  - **Compliance:** HIPAA PHI encryption at rest + in transit; audit logs
- **Difficulty:** Hard

---

### Q57
- **Question:** Design a Python-based notification service that sends emails, SMS, and push notifications.
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Receive events from Kafka or HTTP
  - Strategy pattern for channel (email/SMS/push)
  - Template engine for message content
  - Retry with exponential backoff + dead letter queue
  - Idempotency: don't send duplicate notifications
  - Rate limiting per recipient
  - Delivery tracking in DB
- **Difficulty:** Medium

---

### Q58
- **Question:** Design a GenAI document processing platform for healthcare prior authorization.
- **Level:** 12–15 Yrs | **Company:** Healthcare GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Document ingestion: PDF → Azure Document Intelligence → structured data
  - RAG pipeline: chunk → embed → store in vector DB → retrieve relevant docs
  - LLM extraction: extract diagnosis, procedure codes, clinical notes
  - Rule engine: check plan coverage rules
  - Agentic workflow: LangGraph with human-in-the-loop for uncertain cases
  - Audit trail: all LLM decisions logged with reasoning; HIPAA compliant
  - Monitoring: Langfuse for LLM observability; latency/accuracy metrics
- **Difficulty:** Hard

---

### Q59–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 59 | Design a multi-tenant SaaS API in Python. How do you isolate tenants? | 12–15 Yrs | High | T1 |
| 60 | Design a real-time Python dashboard for medical claims analytics. | 12–15 Yrs | High | T1 |
| 61 | Design a rate-limiting service that handles 1M requests/minute. | 12–15 Yrs | Medium | T2 |
| 62 | Design an audit logging system for a HIPAA-compliant Python service. | 12–15 Yrs | High | T1 |
| 63 | Design a Python-based ETL pipeline that processes 1M records daily. | 8–12 Yrs | High | T1 |
| 64 | How would you migrate a monolithic Python app to microservices? | 12–15 Yrs | High | T1 |
| 65 | Design a search service for a large medical document corpus. | 8–12 Yrs | High | T1 |
| 66 | Design a Python-based recommendation engine for insurance plans. | 12–15 Yrs | Medium | T2 |
| 67 | Design an event-driven claims fraud detection system. | 12–15 Yrs | High | T1 |
| 68 | How do you design for observability from day one in a Python service? | 8–12 Yrs | High | T1 |
| 69 | Design a file processing pipeline (SFTP → validate → transform → DB). | 8–12 Yrs | High | T1 |
| 70 | How do you design a caching strategy for a Python API with 10K req/sec? | 8–12 Yrs | High | T1 |
| 71 | Design a Python worker system for async job processing with priority queues. | 8–12 Yrs | High | T1 |
| 72 | How do you handle database migrations in a microservices environment? | 8–12 Yrs | High | T1 |
| 73 | Design a webhook delivery system with retry and failure tracking. | 8–12 Yrs | Medium | T2 |
| 74 | How do you design an idempotent API for insurance claim submission? | 8–12 Yrs | High | T1 |
| 75 | Design a Python service for real-time provider eligibility verification. | 8–12 Yrs | High | T1 |
| 76 | How would you handle a massive traffic spike (10x) on a Python service? | 12–15 Yrs | High | T1 |
| 77 | Design a HIPAA-compliant GenAI chatbot for member support. | 12–15 Yrs | High | T1 |
| 78 | Design a multi-agent orchestration platform for enterprise AI workflows. | 12–15 Yrs | High | T1 |
| 79 | How do you design for testability in a Python microservices system? | 8–12 Yrs | High | T1 |
| 80 | Design a Python service that processes HL7 FHIR messages in real time. | 8–12 Yrs | Medium | T2 |
| 81 | How do you implement a soft delete strategy across multiple services? | 8–12 Yrs | Medium | T2 |
| 82 | Design a platform for A/B testing LLM prompts in production. | 12–15 Yrs | High | T1 |
| 83 | How do you implement cross-service authorization in a Python microservices system? | 12–15 Yrs | High | T1 |
| 84 | Design a Python-based data masking service for PHI before sending to LLMs. | 12–15 Yrs | High | T1 |
| 85 | How do you design a Python service for high-throughput event ingestion? | 8–12 Yrs | High | T1 |
| 86 | Design a contract testing framework for Python microservices (Pact). | 12–15 Yrs | Medium | T2 |
| 87 | How do you implement distributed configuration management in Python? | 8–12 Yrs | Medium | T2 |
| 88 | Design a Python-based document intelligence pipeline for insurance forms. | 12–15 Yrs | High | T1 |
| 89 | How do you implement eventual consistency for member eligibility updates? | 12–15 Yrs | Medium | T2 |
| 90 | Design an AI-powered claims triage system using Python agents. | 12–15 Yrs | High | T1 |
| 91 | How do you implement a feature flag service in Python? | 8–12 Yrs | High | T1 |
| 92 | Design a Python platform for managing LLM prompt templates and versions. | 12–15 Yrs | High | T1 |
| 93 | How do you handle cross-cutting concerns (auth, logging, tracing) in Python microservices? | 8–12 Yrs | High | T1 |
| 94 | Design a Python-based compliance monitoring service for healthcare data. | 12–15 Yrs | High | T1 |
| 95 | How do you implement request hedging for low-latency Python services? | 12–15 Yrs | Low | T3 |
| 96 | Design a real-time bidding system for digital advertising using Python. | 12–15 Yrs | Low | T3 |
| 97 | How do you design an AI governance framework for a Python GenAI platform? | 12–15 Yrs | High | T1 |
| 98 | How do you implement a self-healing Python microservice? | 12–15 Yrs | Medium | T2 |
| 99 | Design a Python service mesh with Python-based sidecars. | 12–15 Yrs | Low | T3 |
| 100 | Design a complete enterprise GenAI platform: RAG + agents + LLMOps + compliance for a healthcare insurance company. | 12–15 Yrs | High | T1 |
