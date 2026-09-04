# Most Asked Python Questions — 8 to 12 Years Experience
> Target: Senior / Tech Lead | Service Companies, Consulting, Healthcare, Enterprise, GenAI
> Roles: Senior Engineer, Tech Lead, Principal Engineer, Senior Data Engineer, ML Engineer
> Sources: Glassdoor, LinkedIn, Reddit, GeeksforGeeks, Engineering Blogs

---

## What Interviewers Expect at 8–12 Years

At this level, interviewers test:
- Deep Python internals: GIL, memory model, bytecode, CPython execution
- Production system design: microservices, event-driven, caching, resiliency patterns
- Async Python mastery: asyncio patterns, performance tuning, concurrent workloads
- Database design: schema design, query optimization, connection pooling, transactions
- Observability: logging, tracing, metrics, incident response
- Cloud-native: Kubernetes, CI/CD, Docker, cloud services (AWS/Azure)
- GenAI awareness: LLM APIs, RAG, LangChain/LangGraph basics
- Leadership signals: code review standards, mentoring, design decisions

**Expected:** Own technical decisions, design production systems end-to-end, lead technical discussions

---

## SECTION 1: Python Internals & Performance (Q1–Q25)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 1 | Explain Python's memory management: reference counting + cyclic GC. | Memory | T1 |
| 2 | What is the GIL? Why does Python have it? How does it affect CPU-bound vs I/O-bound? | GIL | T1 |
| 3 | How do you profile a Python service in production? (`py-spy`, `cProfile`, `tracemalloc`) | Profiling | T1 |
| 4 | What is `tracemalloc`? How do you use it to find memory leaks? | Memory | T1 |
| 5 | What is Python bytecode? How does CPython execute it? | Internals | T2 |
| 6 | What are Python free lists? How do they affect small object allocation? | Memory | T2 |
| 7 | How does Python's `dict` work internally? How does it handle hash collisions? | Internals | T2 |
| 8 | What is `__slots__`? How does it reduce memory usage? | OOP | T1 |
| 9 | What is the difference between `multiprocessing.Pool.map` and `ProcessPoolExecutor`? | Concurrency | T1 |
| 10 | How do you implement a producer-consumer pattern with asyncio? | Asyncio | T1 |
| 11 | What is `asyncio.TaskGroup`? How does it differ from `gather`? | Asyncio | T1 |
| 12 | How does `uvloop` speed up asyncio? When would you use it? | Performance | T1 |
| 13 | How do you benchmark Python code correctly? Pitfalls of benchmarking? | Performance | T1 |
| 14 | What is the `__del__` method? Why is it unreliable in Python? | Memory | T2 |
| 15 | How does Python's import system work? What is `sys.modules`? | Internals | T2 |
| 16 | What is a coroutine vs a generator in Python 3.10+? | Asyncio | T1 |
| 17 | How does `asyncio.Lock` differ from `threading.Lock`? | Concurrency | T1 |
| 18 | What is Pydantic v2's performance improvement over v1? How does it use Rust? | Pydantic | T1 |
| 19 | How does `dataclasses` compare to Pydantic for performance? | Design | T1 |
| 20 | What is `__call__`? How do you make a class callable? | OOP | T2 |
| 21 | How do you prevent CPU-bound work from blocking an asyncio event loop? | Asyncio | T1 |
| 22 | How does `functools.cached_property` work? When do you use it? | Performance | T1 |
| 23 | What is `typing.TypeVar`? How do you write generic functions? | Type Hints | T2 |
| 24 | What is structural pattern matching (`match/case`) in Python 3.10+? | Core Python | T2 |
| 25 | How do you implement a plugin system using importlib? | Internals | T2 |

---

## SECTION 2: Production Architecture & Design (Q26–Q55)

### Q26
- **Question:** Design a claims processing pipeline that handles 500K claims per day. Walk through architecture.
- **Tier:** T1
- **Answer Points:**
  - Intake: FastAPI REST endpoint + idempotency; or SFTP file drop → watcher
  - Kafka topic for async processing; decouple intake from processing
  - Worker pool (Python ProcessPoolExecutor or Celery) for CPU-bound adjudication
  - PostgreSQL for structured data; Redis for caching eligibility lookups
  - Dead letter queue for failures; retry with exponential backoff
  - Observability: OpenTelemetry traces, Prometheus metrics, structured logging
  - Kubernetes: horizontal pod autoscaling on queue depth

---

### Q27
- **Question:** How do you implement the Repository + Unit of Work pattern in Python?
- **Tier:** T1
- **Answer:**
  ```python
  class UnitOfWork:
      def __init__(self, session_factory):
          self._session_factory = session_factory

      async def __aenter__(self):
          self.session = self._session_factory()
          self.claims = PostgresClaimRepository(self.session)
          self.members = PostgresMemberRepository(self.session)
          return self

      async def __aexit__(self, exc_type, exc, tb):
          if exc_type:
              await self.session.rollback()
          else:
              await self.session.commit()
          await self.session.close()

  async def process_claim(claim_data: dict):
      async with UnitOfWork(session_factory) as uow:
          member = await uow.members.get(claim_data["member_id"])
          claim = Claim.create(member, claim_data)
          await uow.claims.save(claim)
  ```

---

### Q28–Q55 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 28 | What is the strangler fig pattern? Walk through a monolith → microservice migration. | Architecture | T1 |
| 29 | What is CQRS? How do you implement it for a claims service? | Architecture | T2 |
| 30 | What is the saga pattern? Implement it for a distributed transaction. | Architecture | T2 |
| 31 | How do you implement circuit breaker + retry in a Python service? | Resilience | T1 |
| 32 | How do you implement a rate limiter (sliding window) with Redis? | Design | T1 |
| 33 | How do you implement event-driven domain events in a Python service? | Design | T1 |
| 34 | How do you implement multi-tenant isolation in a FastAPI service? | Design | T1 |
| 35 | How do you implement an idempotency layer for a REST API? | Design | T1 |
| 36 | How does SQLAlchemy's session lifecycle work? Explain `expire_on_commit`. | SQLAlchemy | T1 |
| 37 | How do you implement database sharding? What are the tradeoffs? | Database | T2 |
| 38 | How do you implement optimistic locking in SQLAlchemy? | Database | T1 |
| 39 | How do you implement zero-downtime database schema changes? | DevOps | T1 |
| 40 | How do you implement a webhook delivery system with retry and DLQ? | Design | T1 |
| 41 | How do you design a Python service for 99.99% availability? | Architecture | T1 |
| 42 | What is a distributed lock? Implement one with Redis Lua script. | Design | T1 |
| 43 | How do you implement a feature flag system in Python? | Design | T1 |
| 44 | How do you implement a caching strategy for a 10K req/sec API? | Caching | T1 |
| 45 | How do you implement the outbox pattern for reliable event publishing? | Design | T2 |
| 46 | How do you handle database connection exhaustion in production? | Database | T1 |
| 47 | What is hexagonal architecture? How do you apply it to a FastAPI service? | Architecture | T2 |
| 48 | How do you implement graceful degradation when a dependency fails? | Resilience | T1 |
| 49 | How do you implement distributed tracing in Python with OpenTelemetry? | Observability | T1 |
| 50 | What is SOLID? Explain each principle with a Python example. | Design | T1 |
| 51 | How do you implement async task processing with Celery + Redis? | Architecture | T1 |
| 52 | How do you implement an event streaming consumer with Kafka in Python? | Messaging | T1 |
| 53 | How do you implement a health check that validates all dependencies? | DevOps | T1 |
| 54 | How do you implement semantic versioning and API deprecation strategy? | API Design | T1 |
| 55 | How do you design a search service for a large document corpus? | Architecture | T1 |

---

## SECTION 3: GenAI, Testing & Cloud (Q56–Q80)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 56 | What is RAG? Explain the full pipeline from document to answer. | GenAI | T1 |
| 57 | How do you implement a FastAPI endpoint that streams LLM output? | GenAI | T1 |
| 58 | What is function calling in LLMs? How do you implement it? | GenAI | T1 |
| 59 | What is a vector database? When do you use pgvector vs Qdrant? | GenAI | T1 |
| 60 | How do you implement hybrid search (dense + sparse) in RAG? | GenAI | T1 |
| 61 | How do you implement LLM cost tracking and control? | LLMOps | T1 |
| 62 | How do you implement structured output from an LLM using Pydantic? | GenAI | T1 |
| 63 | How do you evaluate a RAG pipeline? What is RAGAS? | GenAI | T1 |
| 64 | What is LangChain LCEL? Build a simple RAG chain. | LangChain | T1 |
| 65 | What is LangGraph? How does it differ from LangChain chains? | LangGraph | T1 |
| 66 | How do you write pytest fixtures for a FastAPI service with DB? | Testing | T1 |
| 67 | What is `pytest.mark.parametrize`? Write a parameterized test. | Testing | T1 |
| 68 | What is contract testing? How do you implement it between microservices? | Testing | T2 |
| 69 | How do you write integration tests that use a real database in CI? | Testing | T1 |
| 70 | How do you implement property-based testing with `hypothesis`? | Testing | T2 |
| 71 | How do you write a production Dockerfile for a Python FastAPI service? | Docker | T1 |
| 72 | How do you implement K8s liveness vs readiness probes in FastAPI? | Kubernetes | T1 |
| 73 | How do you implement horizontal pod autoscaling for a Python service? | Kubernetes | T1 |
| 74 | How do you handle secrets in Kubernetes for a Python service? | Kubernetes | T1 |
| 75 | How do you use `boto3` to read from S3 and write to DynamoDB? | AWS | T1 |
| 76 | How do you process SQS messages with error handling in Lambda? | AWS | T1 |
| 77 | How do you implement a CI/CD pipeline for a Python microservice? | DevOps | T1 |
| 78 | How do you implement rolling updates with zero downtime in K8s? | Kubernetes | T1 |
| 79 | How do you implement database migrations in a K8s deployment? | Kubernetes | T1 |
| 80 | How do you implement canary deployments for a Python service? | DevOps | T1 |

---

## SECTION 4: Leadership & Architecture Design (Q81–Q100)

| # | Question | Focus | Tier |
|---|----------|-------|------|
| 81 | Design a HIPAA-compliant healthcare claims API in Python. | System Design | T1 |
| 82 | Design a real-time notification service for 1M users. | System Design | T1 |
| 83 | Design a Python ETL pipeline that processes 10M records daily. | System Design | T1 |
| 84 | Design a multi-tenant SaaS API with tenant isolation. | System Design | T1 |
| 85 | Your service is throwing 500 errors in production. Walk me through your RCA process. | Incident | T1 |
| 86 | How do you onboard a junior developer to a complex Python codebase? | Leadership | T1 |
| 87 | How do you conduct a code review? What do you look for? | Leadership | T1 |
| 88 | How do you decide between adding a new microservice vs extending the monolith? | Architecture | T1 |
| 89 | How do you handle technical debt in a production system? | Leadership | T1 |
| 90 | How do you define and enforce code quality standards in a team? | Leadership | T1 |
| 91 | How do you estimate the effort for a complex Python feature? | Leadership | T1 |
| 92 | How do you handle a failing CI pipeline blocking multiple teams? | DevOps | T1 |
| 93 | What metrics do you track for a Python microservice? | Observability | T1 |
| 94 | How do you implement SLOs for a Python API? | Observability | T1 |
| 95 | How do you handle a production memory spike? | Debugging | T1 |
| 96 | How do you reduce P99 latency for a FastAPI service? | Performance | T1 |
| 97 | How do you implement a Python service that must process data in strict order? | Architecture | T1 |
| 98 | Describe the most complex Python system you've built. What were the key decisions? | Experience | T1 |
| 99 | How do you stay current with Python ecosystem changes? | Growth | T1 |
| 100 | Design a complete Python GenAI platform for enterprise claims processing: RAG + agents + LangGraph + LLMOps + HIPAA compliance + observability + K8s deployment. | System Design | T1 |
