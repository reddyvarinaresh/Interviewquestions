# Most Asked Python Questions — 5 to 8 Years Experience
> Target: Mid-Level Senior | Service Companies, Consulting, Healthcare, Enterprise
> Roles: Senior Python Developer, Backend Engineer, Software Engineer III, Data Engineer
> Sources: Glassdoor, LinkedIn, Reddit, GeeksforGeeks

---

## What Interviewers Expect at 5–8 Years

At this level, interviewers test:
- Deep Python fluency: decorators, generators, context managers, OOP patterns
- Can design and build production-grade REST APIs (FastAPI/Flask)
- SQL proficiency: joins, window functions, query optimization
- Concurrency awareness: threading vs multiprocessing vs asyncio
- Basic system design: service decomposition, caching, queuing
- Experience with cloud (AWS/Azure), Docker, CI/CD
- Code quality: testing (pytest), clean architecture, SOLID basics
- Debugging production issues independently

**NOT expected yet:** full system architecture, deep distributed systems, leading teams on GenAI platforms

---

## SECTION 1: Advanced Python (Q1–Q30)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 1 | How does Python's memory management work? What is reference counting? | Memory | T1 |
| 2 | What is the GIL? How does it affect threading in Python? | GIL | T1 |
| 3 | When do you use threading vs multiprocessing vs asyncio? | Concurrency | T1 |
| 4 | What is a metaclass? Write a simple example. | OOP | T2 |
| 5 | What is a descriptor? How do `__get__`, `__set__`, `__delete__` work? | OOP | T2 |
| 6 | What is `__slots__`? When do you use it? | OOP | T2 |
| 7 | What is the difference between `@property` and a regular attribute? | OOP | T1 |
| 8 | How does `functools.wraps` work? Why is it important in decorators? | Decorators | T1 |
| 9 | Write a class decorator that adds retry logic to all methods. | Decorators | T2 |
| 10 | What is `itertools`? Name 5 functions and their use cases. | Itertools | T1 |
| 11 | What is `functools.lru_cache`? How does it work internally? | Performance | T1 |
| 12 | How does Python's `asyncio` event loop work? | Asyncio | T1 |
| 13 | What is `async def`, `await`, `asyncio.gather()`? | Asyncio | T1 |
| 14 | How do you implement a semaphore to limit concurrent API calls? | Asyncio | T1 |
| 15 | What is `asyncio.Queue`? How do you implement a producer-consumer? | Asyncio | T1 |
| 16 | What is `contextlib.asynccontextmanager`? Write an example. | Context Managers | T1 |
| 17 | What is `dataclasses`? How does it compare to a regular class? | OOP | T1 |
| 18 | What is `typing.Protocol`? How does it enable structural subtyping? | Type Hints | T2 |
| 19 | How does `isinstance()` work with abstract base classes? | OOP | T2 |
| 20 | What is `abc.ABC`? Write an abstract base class for a repository. | OOP | T1 |
| 21 | What is `weakref`? When do you use it? | Memory | T2 |
| 22 | What is the difference between `__new__` and `__init__`? | OOP | T2 |
| 23 | How do you implement operator overloading in Python? | OOP | T2 |
| 24 | What is `collections.namedtuple` vs `typing.NamedTuple` vs `dataclass`? | Collections | T1 |
| 25 | What is `heapq`? When do you use a priority queue? | Data Structures | T2 |
| 26 | How do you implement a thread-safe singleton in Python? | OOP | T2 |
| 27 | What is `concurrent.futures.ThreadPoolExecutor`? Write an example. | Concurrency | T1 |
| 28 | What is `concurrent.futures.ProcessPoolExecutor`? When do you use it? | Concurrency | T1 |
| 29 | What is a memory leak in Python? How do you find and fix it? | Memory | T1 |
| 30 | How do you profile a Python function? (`cProfile`, `line_profiler`) | Performance | T1 |

---

## SECTION 2: Coding Challenges (Q31–Q55)

### Q31
- **Question:** Write an async function that fetches 100 URLs concurrently with a max of 10 at a time.
- **Answer:**
  ```python
  import asyncio, aiohttp

  async def fetch(session: aiohttp.ClientSession, url: str) -> str:
      async with session.get(url) as resp:
          return await resp.text()

  async def fetch_all(urls: list[str], max_concurrent: int = 10) -> list[str]:
      sem = asyncio.Semaphore(max_concurrent)
      async def bounded_fetch(session, url):
          async with sem:
              return await fetch(session, url)
      async with aiohttp.ClientSession() as session:
          return await asyncio.gather(*[bounded_fetch(session, url) for url in urls])
  ```

---

### Q32
- **Question:** Implement a generic `retry` decorator with exponential backoff.
- **Answer:**
  ```python
  import functools, asyncio, time

  def retry(max_attempts=3, base_delay=1.0, exceptions=(Exception,)):
      def decorator(func):
          @functools.wraps(func)
          async def async_wrapper(*args, **kwargs):
              for attempt in range(max_attempts):
                  try:
                      return await func(*args, **kwargs)
                  except exceptions as e:
                      if attempt == max_attempts - 1:
                          raise
                      await asyncio.sleep(base_delay * (2 ** attempt))
          @functools.wraps(func)
          def sync_wrapper(*args, **kwargs):
              for attempt in range(max_attempts):
                  try:
                      return func(*args, **kwargs)
                  except exceptions as e:
                      if attempt == max_attempts - 1:
                          raise
                      time.sleep(base_delay * (2 ** attempt))
          return async_wrapper if asyncio.iscoroutinefunction(func) else sync_wrapper
      return decorator
  ```

---

### Q33
- **Question:** Write a function to process a large CSV file in chunks and compute the sum of a column.
- **Answer:**
  ```python
  import pandas as pd

  def sum_column_chunked(filepath: str, column: str, chunk_size: int = 50_000) -> float:
      total = 0.0
      for chunk in pd.read_csv(filepath, usecols=[column], chunksize=chunk_size):
          total += chunk[column].sum()
      return total
  ```

---

### Q34–Q55 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 34 | Write a class that implements `__enter__` and `__exit__` for DB transaction management | Context Managers | T1 |
| 35 | Implement a thread-safe counter using threading.Lock | Concurrency | T1 |
| 36 | Write a decorator that validates function arguments using type hints | Decorators | T2 |
| 37 | Implement a simple LRU cache from scratch (without functools) | Data Structures | T2 |
| 38 | Write a generator that reads a large JSON-lines file lazily | Generators | T1 |
| 39 | Write a function to flatten an arbitrarily nested list | Recursion | T1 |
| 40 | Implement a rate limiter class using token bucket algorithm | Design | T2 |
| 41 | Write a function to compute running statistics (mean, std) on a stream | Math | T2 |
| 42 | Write a class-based pipeline that applies a list of transformations to data | Design | T1 |
| 43 | Implement a simple event bus (pub/sub) in Python | Design | T2 |
| 44 | Write a recursive function to traverse a nested dict and update leaf values | Recursion | T1 |
| 45 | Implement a connection pool for database connections in Python | Design | T2 |
| 46 | Write an async function to process items from a queue with N workers | Asyncio | T1 |
| 47 | Implement a debounce decorator for async functions | Decorators | T2 |
| 48 | Write a function to diff two complex nested dicts | Algorithms | T2 |
| 49 | Implement a circuit breaker class in Python | Design | T2 |
| 50 | Write an async context manager for timing code blocks | Context Managers | T1 |
| 51 | Implement `__iter__` and `__next__` for a paginated API client | OOP | T2 |
| 52 | Write a function to merge multiple sorted iterators into one sorted stream | Algorithms | T2 |
| 53 | Implement a generic observer pattern in Python | Design | T2 |
| 54 | Write a function to detect cycles in a dependency graph (dict) | Algorithms | T2 |
| 55 | Implement a typed config loader from YAML with Pydantic validation | Design | T1 |

---

## SECTION 3: FastAPI, SQL & System Design (Q56–Q80)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 56 | How does FastAPI's `Depends()` work? Implement a DB session dependency. | FastAPI | T1 |
| 57 | How do you implement JWT authentication in FastAPI? | FastAPI | T1 |
| 58 | How do you implement rate limiting in FastAPI with Redis? | FastAPI | T1 |
| 59 | How do you implement pagination in a FastAPI endpoint? | FastAPI | T1 |
| 60 | How do you implement background tasks in FastAPI? | FastAPI | T1 |
| 61 | How do you implement API versioning in FastAPI? | FastAPI | T1 |
| 62 | How do you test a FastAPI endpoint with mocked dependencies? | FastAPI | T1 |
| 63 | What is the N+1 problem? How do you detect and fix it in SQLAlchemy? | SQLAlchemy | T1 |
| 64 | How do you configure connection pooling in SQLAlchemy? | SQLAlchemy | T1 |
| 65 | What is an Alembic migration? How do you run one safely? | Database | T1 |
| 66 | Write a SQL window function query using RANK() and LAG(). | SQL | T1 |
| 67 | How do you bulk insert 1M records efficiently into PostgreSQL from Python? | SQL | T1 |
| 68 | What is Redis? How do you implement a cache-aside pattern? | Redis | T1 |
| 69 | How do you implement a distributed lock with Redis? | Redis | T1 |
| 70 | What is a message queue? Have you used Kafka or RabbitMQ? | Messaging | T1 |
| 71 | How do you implement a health check endpoint in FastAPI? | FastAPI | T1 |
| 72 | What is middleware in FastAPI? Write a request timing middleware. | FastAPI | T1 |
| 73 | How do you configure CORS in a FastAPI service? | FastAPI | T1 |
| 74 | How do you implement RBAC in a FastAPI application? | FastAPI | T1 |
| 75 | What is the difference between REST and GraphQL? | API Design | T2 |
| 76 | How do you design an API for bulk operations? | API Design | T1 |
| 77 | How do you handle long-running operations in a REST API? | API Design | T1 |
| 78 | What is idempotency? How do you implement idempotency keys? | API Design | T1 |
| 79 | What is a microservice? When would you use one vs a monolith? | Architecture | T1 |
| 80 | How do you deploy a FastAPI service to Docker + Kubernetes? | DevOps | T1 |

---

## SECTION 4: Design & Scenario Questions (Q81–Q100)

| # | Question | Focus | Tier |
|---|----------|-------|------|
| 81 | Design a URL shortener REST API. What endpoints, storage, and caching? | System Design | T1 |
| 82 | Design a notification service that sends emails and SMS from events. | System Design | T1 |
| 83 | You notice your FastAPI service is slow. How do you investigate? | Debugging | T1 |
| 84 | Production is getting memory errors. How do you find the memory leak? | Debugging | T1 |
| 85 | Your scheduled job is failing silently. How do you detect and fix it? | Debugging | T1 |
| 86 | How would you refactor a 1000-line Python module into a clean structure? | Refactoring | T1 |
| 87 | Walk me through how you'd implement a file processing pipeline. | Architecture | T1 |
| 88 | How do you ensure data integrity when inserting from multiple concurrent services? | Concurrency | T1 |
| 89 | How would you add observability (logging, metrics, tracing) to a service? | Observability | T1 |
| 90 | How do you handle a downstream service that is intermittently slow? | Resilience | T1 |
| 91 | Explain a production incident you handled. What was the root cause? | Experience | T1 |
| 92 | How do you handle database schema changes without downtime? | DevOps | T1 |
| 93 | How do you implement feature flags in a Python service? | Design | T1 |
| 94 | How do you approach testing a service that depends on external APIs? | Testing | T1 |
| 95 | What design patterns have you used in Python? Give examples. | Design | T1 |
| 96 | How would you implement an audit trail for all data changes? | Design | T1 |
| 97 | How do you handle timezone-aware datetime operations in Python? | Core Python | T1 |
| 98 | How would you implement soft delete across a REST API? | API Design | T1 |
| 99 | You need to process 1M insurance claims overnight. How do you design it? | Architecture | T1 |
| 100 | Design a Python REST API service for a healthcare claims submission system with auth, validation, idempotency, background processing, and audit logging. | System Design | T1 |
