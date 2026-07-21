# Top 100 Production Python Debugging Questions
> Focus: 8–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise
> Sources: Glassdoor, LinkedIn, Reddit r/Python, Medium Incident Reports

---

## SECTION 1: Logging & Observability (Q1–Q20)

### Q1
- **Question:** How do you design a logging strategy for a Python microservice from scratch?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Use `logging.getLogger(__name__)` per module — never root logger
  - Structured JSON logging via `structlog` or `python-json-logger`
  - Log levels: DEBUG (dev only), INFO (business events), WARNING (unexpected but recoverable), ERROR (failures), CRITICAL (service down)
  - Always include: timestamp, level, service name, trace_id, user_id, duration
  - Never log PII/PHI; mask sensitive fields
  - Ship logs to centralized system (ELK, Datadog, Splunk)
- **Senior Answer:** Log sampling for high-throughput endpoints; async log shipping to avoid I/O blocking
- **Architect Answer:** Log taxonomy standard across all services; alerting rules on ERROR rate; log retention policy
- **Follow-Ups:** How do you add request trace IDs to every log line in FastAPI?
- **Difficulty:** Medium

---

### Q2
- **Question:** How do you add a request ID to every log line in a FastAPI service without touching every log statement?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import uuid, logging
  from contextvars import ContextVar
  from fastapi import Request

  request_id_var: ContextVar[str] = ContextVar('request_id', default='')

  class RequestIdFilter(logging.Filter):
      def filter(self, record):
          record.request_id = request_id_var.get('')
          return True

  # Middleware
  async def request_id_middleware(request: Request, call_next):
      request_id = request.headers.get('X-Request-ID', str(uuid.uuid4()))
      token = request_id_var.set(request_id)
      try:
          response = await call_next(request)
          response.headers['X-Request-ID'] = request_id
          return response
      finally:
          request_id_var.reset(token)
  ```
- **Follow-Ups:** How does `ContextVar` propagate to async tasks created inside the request?
- **Difficulty:** Hard

---

### Q3
- **Question:** How do you debug a Python service that's producing no logs in production?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Check log level config — may be set to CRITICAL only
  - Check handler — FileHandler path permissions, RotatingFileHandler size limit reached
  - Check if logging is configured before imports (basicConfig timing issues)
  - Check if logs are buffered (unbuffered with `PYTHONUNBUFFERED=1` in Docker)
  - Check if process is crashing before logger init
  - Check stdout vs stderr routing in container logs
- **Real Example:** Docker container had `PYTHONUNBUFFERED` not set — logs only flushed on exit, never seen in CloudWatch for crashed containers
- **Difficulty:** Medium

---

### Q4
- **Question:** How do you implement structured JSON logging in Python that integrates with Datadog?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import logging, json
  from pythonjsonlogger import jsonlogger

  class DatadogFormatter(jsonlogger.JsonFormatter):
      def add_fields(self, log_record, record, message_dict):
          super().add_fields(log_record, record, message_dict)
          log_record['service'] = 'claims-processor'
          log_record['env'] = os.getenv('ENV', 'dev')
          log_record['dd.trace_id'] = get_trace_id()  # from ddtrace

  handler = logging.StreamHandler()
  handler.setFormatter(DatadogFormatter('%(timestamp)s %(level)s %(name)s %(message)s'))
  logging.getLogger().addHandler(handler)
  ```
- **Difficulty:** Medium

---

### Q5–Q20 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 5 | How do you avoid logging PII/PHI in a healthcare Python service? | 8–12 Yrs | High | T1 |
| 6 | How do you implement log sampling for a high-throughput Python API? | 8–12 Yrs | Medium | T2 |
| 7 | How do you use `logging.Filter` to add dynamic context to all log records? | 8–12 Yrs | Medium | T2 |
| 8 | What is `structlog`? How does it improve over standard `logging`? | 8–12 Yrs | Medium | T2 |
| 9 | How do you log slow database queries automatically in SQLAlchemy? | 8–12 Yrs | High | T1 |
| 10 | How do you implement distributed tracing with OpenTelemetry in Python? | 8–12 Yrs | High | T1 |
| 11 | How do you correlate logs from multiple microservices for a single request? | 8–12 Yrs | High | T1 |
| 12 | How do you add OpenTelemetry spans to a FastAPI endpoint? | 8–12 Yrs | High | T1 |
| 13 | How do you expose Prometheus metrics from a FastAPI service? | 8–12 Yrs | Medium | T2 |
| 14 | How do you alert on ERROR rate spike in a Python service using Grafana? | 8–12 Yrs | Medium | T2 |
| 15 | What is `sentry-sdk`? How do you integrate it with FastAPI? | 5–8 Yrs | High | T1 |
| 16 | How do you log the full request body and response body for debugging (with truncation)? | 8–12 Yrs | Medium | T2 |
| 17 | How does `logging.captureWarnings(True)` work? | 5–8 Yrs | Low | T3 |
| 18 | How do you rotate log files in Python? (`RotatingFileHandler` vs `TimedRotatingFileHandler`) | 5–8 Yrs | Medium | T2 |
| 19 | How do you write audit logs that cannot be tampered with? | 12–15 Yrs | Medium | T2 |
| 20 | How do you implement a "breadcrumb" trail for debugging complex workflows? | 12–15 Yrs | Medium | T2 |

---

## SECTION 2: Memory Leaks & Performance (Q21–Q40)

### Q21
- **Question:** A Python service's memory grows over time and never shrinks. How do you diagnose it?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  1. Monitor RSS memory over time with `psutil.Process().memory_info().rss`
  2. Enable `tracemalloc` at service startup; take snapshots periodically
  3. Compare snapshots to find new allocations: `snapshot2.compare_to(snapshot1, 'lineno')`
  4. Use `objgraph.show_growth()` to find which object types are growing
  5. Check for: global lists accumulating data, cache without eviction, circular refs + `__del__`, event listeners holding strong refs
- **Real Example:** FastAPI service caching Pydantic model instances in a module-level dict that grew unbounded — added `maxsize` limit

---

### Q22
- **Question:** How do you diagnose a Python service that's using 10x more memory than expected?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `sys.getsizeof(obj)` — shallow size
  - `pympler.asizeof(obj)` — deep recursive size
  - `tracemalloc.take_snapshot()` — top allocating lines
  - Check for: loading entire file into memory, large DataFrames kept alive, unintentional references preventing GC
  - Memory vs RSS: Python's `pymalloc` doesn't always return memory to OS; high RSS after large allocation even after del

---

### Q23
- **Question:** A Python batch job is running 3x slower than expected. How do you investigate?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  1. Profile with `cProfile`: `python -m cProfile -s cumtime script.py`
  2. Find hottest functions; drill down with `line_profiler` for the top offenders
  3. Check for: Pandas `.apply()` (use vectorized ops), Python loops over large lists (use NumPy), repeated DB queries (N+1), JSON serialization of large objects
  4. `py-spy record -o profile.svg --pid <PID>` for running processes
- **Real Example:** Claims processing job using `.iterrows()` — replaced with vectorized Pandas; 8x speedup

---

### Q24
- **Question:** How do you use `py-spy` to profile a running Python process without restarting it?
- **Level:** 8–12 Yrs | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```bash
  # Attach to running process (no code changes needed)
  py-spy record -o profile.svg --pid 12345 --duration 30

  # Top-like live view
  py-spy top --pid 12345

  # Dump current stack traces
  py-spy dump --pid 12345
  ```
- **Answer Points:** `py-spy` uses `ptrace` / OS-level inspection; no Python overhead; works in production without restart; generates flame graphs
- **Difficulty:** Medium

---

### Q25–Q40 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 25 | How do you find which function is using the most CPU in a production service? | 8–12 Yrs | High | T1 |
| 26 | What is `tracemalloc`? Write a minimal leak detector using it. | 8–12 Yrs | High | T1 |
| 27 | A Python service's GC is causing latency spikes. How do you fix it? | 12–15 Yrs | Medium | T2 |
| 28 | How do you detect reference cycles in your Python code? | 8–12 Yrs | Medium | T2 |
| 29 | How do you implement a memory usage health check endpoint? | 8–12 Yrs | Medium | T2 |
| 30 | A Celery worker is consuming excessive memory. How do you debug it? | 8–12 Yrs | High | T1 |
| 31 | How does `weakref.WeakValueDictionary` prevent memory leaks in caches? | 8–12 Yrs | Medium | T2 |
| 32 | A pandas operation is causing OOM. How do you fix it without buying more RAM? | 8–12 Yrs | High | T1 |
| 33 | How do you benchmark Python code correctly? (timeit pitfalls) | 5–8 Yrs | Medium | T2 |
| 34 | A Python Lambda function is timing out. How do you diagnose it? | 8–12 Yrs | High | T1 |
| 35 | How do you detect and fix excessive string concatenation in Python? | 5–8 Yrs | Medium | T2 |
| 36 | A FastAPI endpoint is slow only under concurrent load. How do you debug? | 8–12 Yrs | High | T1 |
| 37 | How do you reduce cold start time for a Python Lambda/Azure Function? | 8–12 Yrs | Medium | T2 |
| 38 | How do you profile async Python code? What tools work for asyncio? | 8–12 Yrs | Medium | T2 |
| 39 | How do you detect and fix a CPU spike caused by excessive GC? | 12–15 Yrs | Medium | T2 |
| 40 | A Python service leaks file descriptors. How do you detect and fix it? | 8–12 Yrs | Medium | T2 |

---

## SECTION 3: Exception Handling & RCA (Q41–Q60)

### Q41
- **Question:** Walk me through how you would do a Root Cause Analysis (RCA) for a Python service outage.
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  1. Establish timeline: when did it start, what changed (deployment, traffic spike, config change)?
  2. Check logs: error rate, exception types, first occurrence
  3. Check metrics: CPU, memory, DB connections, queue depth, response times
  4. Reproduce in staging if possible
  5. Identify root cause vs contributing factors
  6. Write RCA document: timeline, impact, root cause, fix, prevention
- **Real Example:** Service went OOM after load test — identified unbounded in-memory cache; added `maxsize`; wrote post-mortem

---

### Q42
- **Question:** How do you design exception handling strategy for a Python microservice?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Domain exception hierarchy: `AppError` → `ValidationError`, `NotFoundError`, `ExternalServiceError`
  - Catch at boundaries (API layer, queue consumer handler)
  - Always log with `exc_info=True` for full traceback
  - Map internal exceptions to HTTP status codes in FastAPI exception handlers
  - Never swallow exceptions silently; never use bare `except`
  - Re-raise after logging if caller needs to handle

---

### Q43
- **Question:** How do you use FastAPI's exception handlers? Map custom exceptions to HTTP responses.
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi import Request
  from fastapi.responses import JSONResponse

  class NotFoundError(Exception):
      def __init__(self, resource: str, id: str):
          self.resource = resource
          self.id = id

  @app.exception_handler(NotFoundError)
  async def not_found_handler(request: Request, exc: NotFoundError):
      return JSONResponse(
          status_code=404,
          content={"error": "NOT_FOUND", "message": f"{exc.resource} {exc.id} not found"}
      )
  ```

---

### Q44
- **Question:** A Python service is silently failing — requests return 200 but data is wrong. How do you debug?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Check for silent exception swallowing (`except: pass`)
  - Check for wrong conditional logic returning default values
  - Add request/response logging middleware
  - Check Pydantic model coercion — string "0" coercing to int 0 silently
  - Add integration tests that verify data not just status codes
  - Use `assert` statements in dev; validation in prod

---

### Q45–Q60 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 45 | How do you handle transient failures (network timeouts) vs permanent failures? | 8–12 Yrs | High | T1 |
| 46 | A Celery task is failing silently. How do you track task failures? | 8–12 Yrs | High | T1 |
| 47 | How do you implement circuit breaker in Python? When does it open/close? | 8–12 Yrs | High | T1 |
| 48 | A Python service is returning 500 errors randomly. How do you isolate the cause? | 8–12 Yrs | High | T1 |
| 49 | How do you handle `TimeoutError` from an LLM API call in production? | 8–12 Yrs | High | T1 |
| 50 | How do you implement a dead letter queue for failed messages in Python? | 8–12 Yrs | High | T1 |
| 51 | A database transaction is deadlocking intermittently. How do you debug it? | 8–12 Yrs | Medium | T2 |
| 52 | How do you handle partial failures in a batch processing job? | 8–12 Yrs | High | T1 |
| 53 | How do you implement idempotent retry for an API that might have already succeeded? | 8–12 Yrs | High | T1 |
| 54 | How do you debug a `RecursionError` in production Python code? | 5–8 Yrs | Medium | T2 |
| 55 | What is exception chaining (`raise X from Y`)? When should you use it? | 5–8 Yrs | Medium | T2 |
| 56 | How do you handle `UnicodeDecodeError` when reading files with unknown encoding? | 5–8 Yrs | High | T1 |
| 57 | A Python service is raising `OSError: too many open files`. How do you fix it? | 8–12 Yrs | Medium | T2 |
| 58 | How do you implement health checks that report meaningful failure reasons? | 8–12 Yrs | High | T1 |
| 59 | How do you detect when an async task has "leaked" and is running longer than expected? | 8–12 Yrs | Medium | T2 |
| 60 | How do you handle `ConnectionResetError` from a downstream service gracefully? | 5–8 Yrs | High | T1 |

---

## SECTION 4: API & Service Debugging (Q61–Q80)

### Q61
- **Question:** A FastAPI endpoint is slow. Walk through your investigation process.
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  1. Check p50/p95/p99 latency — is it all requests or some?
  2. Add timing middleware to measure breakdown (validation, handler, DB, external calls)
  3. Check for: blocking sync call in async handler, N+1 queries, missing DB index, no connection pooling
  4. Use OpenTelemetry spans to trace each step
  5. Check external API latency (use separate timeout metric)
  6. Profile with `py-spy` if CPU-bound

---

### Q62
- **Question:** How do you add request/response logging middleware to FastAPI?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import time
  from fastapi import Request

  @app.middleware("http")
  async def log_requests(request: Request, call_next):
      start = time.perf_counter()
      response = await call_next(request)
      duration = time.perf_counter() - start
      logger.info(
          "request",
          method=request.method,
          path=request.url.path,
          status=response.status_code,
          duration_ms=round(duration * 1000, 2)
      )
      return response
  ```

---

### Q63
- **Question:** How do you debug a memory leak in a FastAPI service that grows after each request?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Add `tracemalloc` snapshot endpoint: `GET /debug/memory`
  - Compare snapshots between requests
  - Check: global state accumulation, unclosed DB sessions, response objects not freed
  - Common cause: `background_tasks.add_task()` holding closure with large objects

---

### Q64–Q80 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 64 | How do you debug a FastAPI service that hangs on shutdown? | 8–12 Yrs | Medium | T2 |
| 65 | How do you track down which endpoint is causing DB connection pool exhaustion? | 8–12 Yrs | High | T1 |
| 66 | A Python service is rate-limited by a downstream API. How do you handle it gracefully? | 8–12 Yrs | High | T1 |
| 67 | How do you debug a 503 from a Python service under load? | 8–12 Yrs | High | T1 |
| 68 | How do you implement request deduplication to prevent double-processing? | 8–12 Yrs | High | T1 |
| 69 | How do you debug a Python service that works locally but fails in Docker? | 5–8 Yrs | High | T1 |
| 70 | How do you debug a Python service that randomly disconnects from the database? | 8–12 Yrs | High | T1 |
| 71 | How do you trace a request through multiple Python microservices? | 8–12 Yrs | High | T1 |
| 72 | How do you debug a Celery task that never starts? | 8–12 Yrs | High | T1 |
| 73 | How do you handle a stuck Kafka consumer in Python? | 8–12 Yrs | Medium | T2 |
| 74 | How do you diagnose a Python service that's crashing without any log output? | 8–12 Yrs | High | T1 |
| 75 | How do you debug a Python service where `asyncio.sleep()` is not yielding properly? | 8–12 Yrs | Medium | T2 |
| 76 | How do you handle `SSL` errors from external API calls in Python? | 5–8 Yrs | Medium | T2 |
| 77 | How do you debug JSON serialization errors in a FastAPI response? | 5–8 Yrs | High | T1 |
| 78 | A Python service's health check passes but it's not processing requests. Why? | 8–12 Yrs | Medium | T2 |
| 79 | How do you implement dark mode / shadow logging to compare old and new behavior? | 12–15 Yrs | Low | T3 |
| 80 | How do you debug a Python service deployed as an Azure Function that times out? | 8–12 Yrs | High | T1 |

---

## SECTION 5: Incident Management & Optimization (Q81–Q100)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 81 | How do you write a post-mortem for a Python service outage? What does it include? | 8–12 Yrs | High | T1 |
| 82 | How do you implement canary deployments for a Python microservice? | 12–15 Yrs | Medium | T2 |
| 83 | How do you roll back a bad deployment of a Python service? | 8–12 Yrs | High | T1 |
| 84 | How do you optimize a Python API that serves the same slow query 1000 times/min? | 8–12 Yrs | High | T1 |
| 85 | How do you implement a caching layer that doesn't serve stale data? | 8–12 Yrs | High | T1 |
| 86 | How do you prevent cache stampede (thundering herd) in a Python service? | 8–12 Yrs | Medium | T2 |
| 87 | How do you implement graceful degradation when a downstream service is down? | 8–12 Yrs | High | T1 |
| 88 | A Python batch job is taking 6 hours. How do you parallelize it? | 8–12 Yrs | High | T1 |
| 89 | How do you profile and optimize a Pandas ETL pipeline? | 8–12 Yrs | High | T1 |
| 90 | How do you reduce JSON serialization overhead in a high-throughput Python API? | 8–12 Yrs | Medium | T2 |
| 91 | How do you tune SQLAlchemy connection pool settings for a high-concurrency service? | 8–12 Yrs | Medium | T2 |
| 92 | How do you implement request coalescing for expensive computations? | 12–15 Yrs | Low | T3 |
| 93 | How do you optimize Python startup time for a serverless function? | 8–12 Yrs | Medium | T2 |
| 94 | How do you reduce memory usage of a Python service running in a 512MB container? | 8–12 Yrs | High | T1 |
| 95 | How do you implement tiered caching (in-process L1 + Redis L2) in Python? | 12–15 Yrs | Medium | T2 |
| 96 | How do you debug LLM API latency issues in a GenAI Python service? | 8–12 Yrs | High | T1 |
| 97 | How do you handle a Kafka consumer falling behind (consumer lag growing)? | 8–12 Yrs | Medium | T2 |
| 98 | How do you implement SLO-based alerting for a Python microservice? | 12–15 Yrs | Medium | T2 |
| 99 | How do you do a live performance optimization without downtime on a Python service? | 12–15 Yrs | Medium | T2 |
| 100 | Design a complete observability stack for a Python GenAI service (logs, metrics, traces, alerts). | 12–15 Yrs | High | T1 |
