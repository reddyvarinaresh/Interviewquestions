# Top 100 Decorator / Generator / Iterator / Context Manager Questions
> Focus: 5–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise
> Sources: Glassdoor, LinkedIn, Reddit, GeeksforGeeks, Medium

---

## DECORATORS (Q1–Q35)

### Q1
- **Question:** What is a decorator? Explain with a simple example without `functools.wraps`.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def my_decorator(func):
      def wrapper(*args, **kwargs):
          print("Before")
          result = func(*args, **kwargs)
          print("After")
          return result
      return wrapper

  @my_decorator
  def greet(name):
      print(f"Hello {name}")
  ```
- **Follow-Ups:** What breaks without `functools.wraps`? Show the metadata issue.

---

### Q2
- **Question:** Why is `functools.wraps` needed? What does it actually copy?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Without `wraps`: `greet.__name__` → "wrapper", `greet.__doc__` → None
  - `@wraps(func)` copies: `__name__`, `__qualname__`, `__doc__`, `__module__`, `__annotations__`, `__dict__`, `__wrapped__`
  - Breaks debugging, introspection, and API doc generation without it
- **Difficulty:** Easy

---

### Q3
- **Question:** Write a decorator that logs function entry, exit, and execution time. Make it production-ready.
- **Level:** 5–8 Yrs | **Company:** All service companies | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import functools, logging, time

  logger = logging.getLogger(__name__)

  def log_execution(func):
      @functools.wraps(func)
      def wrapper(*args, **kwargs):
          logger.info(f"Calling {func.__name__}")
          start = time.perf_counter()
          try:
              result = func(*args, **kwargs)
              elapsed = time.perf_counter() - start
              logger.info(f"{func.__name__} completed in {elapsed:.4f}s")
              return result
          except Exception as e:
              elapsed = time.perf_counter() - start
              logger.error(f"{func.__name__} failed after {elapsed:.4f}s: {e}")
              raise
      return wrapper
  ```
- **Follow-Ups:** How do you make this async-compatible?

---

### Q4
- **Question:** Write a parameterized decorator — `@retry(times=3, delay=1.0, exceptions=(IOError,))`.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import functools, time

  def retry(times=3, delay=1.0, exceptions=(Exception,)):
      def decorator(func):
          @functools.wraps(func)
          def wrapper(*args, **kwargs):
              for attempt in range(1, times + 1):
                  try:
                      return func(*args, **kwargs)
                  except exceptions as e:
                      if attempt == times:
                          raise
                      time.sleep(delay * (2 ** (attempt - 1)))
          return wrapper
      return decorator
  ```
- **Follow-Ups:** How do you add jitter? How do you make it async-compatible?

---

### Q5
- **Question:** How do you write a decorator that can be used with OR without parentheses?
  ```python
  @timer          # no args
  @timer()        # empty args
  @timer(unit="ms")  # with args
  ```
- **Level:** 8–12 Yrs | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  def timer(_func=None, *, unit="s"):
      def decorator(func):
          @functools.wraps(func)
          def wrapper(*args, **kwargs):
              ...
          return wrapper
      if _func is not None:
          return decorator(_func)
      return decorator
  ```
- **Difficulty:** Hard

---

### Q6
- **Question:** Write a class-based decorator that counts function calls.
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  class CallCounter:
      def __init__(self, func):
          functools.update_wrapper(self, func)
          self.func = func
          self.count = 0

      def __call__(self, *args, **kwargs):
          self.count += 1
          return self.func(*args, **kwargs)
  ```
- **Follow-Ups:** How do you make this thread-safe?

---

### Q7
- **Question:** Write an async-compatible retry decorator that works for both `async def` and regular `def`.
- **Level:** 8–12 Yrs | **Company:** GenAI, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import asyncio, functools

  def retry_async(times=3, delay=1.0):
      def decorator(func):
          @functools.wraps(func)
          async def async_wrapper(*args, **kwargs):
              for i in range(times):
                  try:
                      return await func(*args, **kwargs)
                  except Exception:
                      if i == times - 1: raise
                      await asyncio.sleep(delay * 2**i)

          @functools.wraps(func)
          def sync_wrapper(*args, **kwargs):
              for i in range(times):
                  try:
                      return func(*args, **kwargs)
                  except Exception:
                      if i == times - 1: raise
                      time.sleep(delay * 2**i)

          if asyncio.iscoroutinefunction(func):
              return async_wrapper
          return sync_wrapper
      return decorator
  ```

---

### Q8
- **Question:** Explain decorator ordering. What is the execution order when stacking `@A @B @C`?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Application order (wrapping): C → B → A (bottom to top)
  - Execution order (runtime call): A's wrapper → B's wrapper → C's wrapper → func
  - `@A @B def f` → `f = A(B(f))`
  - Matters for auth + logging: `@auth @log` means auth checks first
- **Difficulty:** Medium

---

### Q9–Q35 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 9 | Write a decorator that enforces a function is only called once (idempotent) | 5–8 Yrs | Medium | T2 |
| 10 | Write a decorator that validates that all function args are positive integers | 5–8 Yrs | Medium | T2 |
| 11 | How do you implement `@cached_property` from scratch without threading? | 8–12 Yrs | Medium | T2 |
| 12 | Write a `@deprecated` decorator that logs a warning on use | 5–8 Yrs | Medium | T2 |
| 13 | Write a decorator that wraps a function in a database transaction | 8–12 Yrs | High | T1 |
| 14 | Write a `@require_auth` decorator for a FastAPI route | 8–12 Yrs | High | T1 |
| 15 | How do you chain decorators that each add their own keyword argument? | 8–12 Yrs | Medium | T2 |
| 16 | Write a decorator that rate-limits function calls (N calls per second) | 8–12 Yrs | Medium | T2 |
| 17 | How do you inspect what decorators are applied to a function? | 8–12 Yrs | Low | T3 |
| 18 | What is `__wrapped__`? How does it help with debugging decorated functions? | 8–12 Yrs | Medium | T2 |
| 19 | Write a decorator that profiles memory usage before and after a function call | 8–12 Yrs | Medium | T2 |
| 20 | How do you test a decorated function — test the original vs the wrapped version? | 5–8 Yrs | High | T1 |
| 21 | Write a decorator that converts exceptions into a standard error response dict | 8–12 Yrs | High | T1 |
| 22 | How do you apply a decorator to all methods in a class automatically? | 8–12 Yrs | Medium | T2 |
| 23 | What is `@dataclass` — is it a decorator? How does it work internally? | 5–8 Yrs | High | T1 |
| 24 | What is `@functools.total_ordering`? How does it reduce boilerplate? | 8–12 Yrs | Medium | T2 |
| 25 | Write a decorator that adds OpenTelemetry span tracing to any function | 8–12 Yrs | High | T1 |
| 26 | How do you write a decorator that injects a logger as a parameter? | 8–12 Yrs | Medium | T2 |
| 27 | How does `@contextmanager` work as a decorator? | 5–8 Yrs | High | T1 |
| 28 | Write a decorator that converts a sync function to run in a thread executor | 8–12 Yrs | High | T1 |
| 29 | How do you write a decorator that catches specific exceptions and retries with fallback? | 8–12 Yrs | High | T1 |
| 30 | Write a decorator that serializes function arguments and results to a log file | 8–12 Yrs | Medium | T2 |
| 31 | What is `functools.singledispatch`? How does it implement function overloading? | 8–12 Yrs | Medium | T2 |
| 32 | Write a decorator that ensures a function's return value matches a Pydantic model | 8–12 Yrs | High | T1 |
| 33 | How do you implement a circuit breaker decorator? | 8–12 Yrs | High | T1 |
| 34 | Write a decorator that adds an HTTP request context (trace ID, user ID) to all logs | 8–12 Yrs | High | T1 |
| 35 | How do you mock a decorator in unit tests? | 5–8 Yrs | High | T1 |

---

## GENERATORS & ITERATORS (Q36–Q70)

### Q36
- **Question:** What is the difference between `return` and `yield` in a function?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `return` exits function and returns value once; function done
  - `yield` suspends function and returns value; function state preserved
  - Function with `yield` becomes a generator function
  - Calling a generator function returns a generator object (doesn't execute body)

---

### Q37
- **Question:** How do you iterate through a generator that might raise exceptions? Handle them cleanly.
- **Level:** 5–8 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def safe_iter(gen):
      while True:
          try:
              yield next(gen)
          except StopIteration:
              return
          except Exception as e:
              logging.error(f"Generator error: {e}")
              continue  # or break depending on policy
  ```

---

### Q38
- **Question:** Write a generator that reads a large JSON-lines file and yields parsed records.
- **Level:** 5–8 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import json

  def read_jsonl(filepath):
      with open(filepath, 'r') as f:
          for line in f:
              line = line.strip()
              if line:
                  try:
                      yield json.loads(line)
                  except json.JSONDecodeError as e:
                      logging.warning(f"Skipping bad line: {e}")
  ```

---

### Q39
- **Question:** Explain `yield from`. How does it differ from `for x in sub: yield x`?
- **Level:** 8–12 Yrs | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `yield from sub` delegates completely to sub-generator
  - Passes through `send()`, `throw()`, `close()` transparently
  - Return value of sub-generator available via `StopIteration.value`
  - `for x in sub: yield x` loses `send()` support

---

### Q40
- **Question:** Write a generator pipeline: read CSV → filter valid rows → transform → batch 100 rows → write to DB.
- **Level:** 8–12 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def read_rows(path):
      with open(path) as f:
          yield from csv.DictReader(f)

  def filter_valid(rows):
      for row in rows:
          if row.get('status') == 'active':
              yield row

  def transform(rows):
      for row in rows:
          row['amount'] = float(row['amount'])
          yield row

  def batch(rows, size=100):
      chunk = []
      for row in rows:
          chunk.append(row)
          if len(chunk) == size:
              yield chunk
              chunk = []
      if chunk:
          yield chunk

  # Pipeline
  pipeline = batch(transform(filter_valid(read_rows("data.csv"))))
  for chunk in pipeline:
      db.bulk_insert(chunk)
  ```

---

### Q41
- **Question:** How do you implement an infinite generator for IDs? How does the caller stop it?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  def id_generator(start=1):
      n = start
      while True:
          yield n
          n += 1

  gen = id_generator()
  ids = [next(gen) for _ in range(10)]  # caller controls consumption
  ```

---

### Q42
- **Question:** What is `itertools.islice`? How do you take the first N records from a large generator?
- **Level:** 5–8 Yrs | **Company:** Data | **Frequency:** Medium | **Tier:** T2
- **Answer:** `list(itertools.islice(gen, 1000))` — takes first 1000 without loading rest

---

### Q43
- **Question:** How do you check how many items a generator will produce without consuming it?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** You can't (generators are lazy — no `len()`); convert to list first (memory cost); use a counting wrapper generator; design APIs to return size separately

---

### Q44
- **Question:** How do async generators work? Write an async generator for paginated API calls.
- **Level:** 8–12 Yrs | **Company:** GenAI, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  async def paginated_api(url, page_size=50):
      page = 1
      async with aiohttp.ClientSession() as session:
          while True:
              async with session.get(url, params={'page': page, 'size': page_size}) as resp:
                  data = await resp.json()
                  if not data:
                      break
                  for item in data:
                      yield item
                  page += 1
  ```

---

### Q45–Q70 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 45 | What is `StopIteration`? What happens when you call `next()` on an exhausted generator? | 3–5 Yrs | High | T1 |
| 46 | How do you use `next(gen, default)` to avoid `StopIteration`? | 3–5 Yrs | High | T1 |
| 47 | What is `itertools.chain`? How does it combine multiple iterables lazily? | 5–8 Yrs | Medium | T2 |
| 48 | What is `itertools.groupby`? What is the gotcha with unsorted input? | 5–8 Yrs | Medium | T2 |
| 49 | Write a generator that yields Fibonacci numbers up to a limit | 3–5 Yrs | High | T1 |
| 50 | How do you implement a generator-based state machine? | 8–12 Yrs | Medium | T2 |
| 51 | What is `itertools.product`? Give a real use case for parameter grid search | 8–12 Yrs | Medium | T2 |
| 52 | How do you implement lazy evaluation of expensive computations using generators? | 8–12 Yrs | High | T1 |
| 53 | What is `itertools.accumulate`? Write an example for running balance tracking | 5–8 Yrs | Medium | T2 |
| 54 | How do you implement a sliding window over a data stream using a generator? | 8–12 Yrs | Medium | T2 |
| 55 | What is `itertools.cycle`? Implement round-robin load distribution | 8–12 Yrs | Medium | T2 |
| 56 | What is `itertools.takewhile` and `dropwhile`? Give real use cases | 5–8 Yrs | Medium | T2 |
| 57 | How do you convert a recursive tree walk into a generator? | 8–12 Yrs | Medium | T2 |
| 58 | Write a generator that reads records from multiple files in round-robin order | 8–12 Yrs | Medium | T2 |
| 59 | How does Python's `zip()` implement lazy evaluation? | 3–5 Yrs | High | T1 |
| 60 | How do you implement a generator that retries on failure and yields partial results? | 8–12 Yrs | High | T1 |
| 61 | What is `itertools.starmap`? How is it different from `map`? | 5–8 Yrs | Low | T3 |
| 62 | How do you implement a backpressure mechanism in a generator pipeline? | 12–15 Yrs | Medium | T2 |
| 63 | What is `generator.throw()`? How do you send exceptions into a generator? | 8–12 Yrs | Low | T3 |
| 64 | How do you implement a generator that transforms a stream of raw bytes into structured records? | 8–12 Yrs | Medium | T2 |
| 65 | What is `itertools.pairwise` (Python 3.10+)? | 5–8 Yrs | Low | T3 |
| 66 | How do you implement a generator that deduplicates a stream of records by key? | 8–12 Yrs | High | T1 |
| 67 | How do you use `yield from` to flatten a nested structure recursively? | 8–12 Yrs | Medium | T2 |
| 68 | What is `more-itertools` library? Which functions do you use in production? | 8–12 Yrs | Low | T3 |
| 69 | How do you implement a generator-based CSV writer that streams output? | 8–12 Yrs | Medium | T2 |
| 70 | Write a generator that processes insurance claims and yields those requiring manual review | 8–12 Yrs | High | T1 |

---

## CONTEXT MANAGERS (Q71–Q100)

### Q71
- **Question:** What is the context manager protocol? What must a class implement?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `__enter__` (setup, returns resource); `__exit__(exc_type, exc_val, exc_tb)` (cleanup); return `True` to suppress exception; `False`/`None` to propagate

---

### Q72
- **Question:** Write a context manager that acquires a Redis lock and releases it on exit.
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  @contextmanager
  def redis_lock(redis_client, key, timeout=10):
      acquired = redis_client.set(key, "1", ex=timeout, nx=True)
      if not acquired:
          raise RuntimeError(f"Could not acquire lock: {key}")
      try:
          yield
      finally:
          redis_client.delete(key)
  ```

---

### Q73
- **Question:** How do you implement a context manager that temporarily overrides environment variables?
- **Level:** 5–8 Yrs | **Company:** Testing | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  @contextmanager
  def env_override(**kwargs):
      original = {k: os.environ.get(k) for k in kwargs}
      os.environ.update({k: v for k, v in kwargs.items()})
      try:
          yield
      finally:
          for k, v in original.items():
              if v is None:
                  os.environ.pop(k, None)
              else:
                  os.environ[k] = v
  ```

---

### Q74
- **Question:** Write an async context manager for an aiohttp session with retry logic.
- **Level:** 8–12 Yrs | **Company:** GenAI, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from contextlib import asynccontextmanager

  @asynccontextmanager
  async def http_session(timeout=30):
      connector = aiohttp.TCPConnector(limit=100)
      async with aiohttp.ClientSession(
          connector=connector,
          timeout=aiohttp.ClientTimeout(total=timeout)
      ) as session:
          yield session
  ```

---

### Q75–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 75 | How does `contextlib.ExitStack` help manage a dynamic number of context managers? | 8–12 Yrs | Medium | T2 |
| 76 | Write a context manager that measures and enforces a max execution time | 8–12 Yrs | Medium | T2 |
| 77 | How do you suppress specific exceptions inside a `with` block? | 5–8 Yrs | High | T1 |
| 78 | What is `contextlib.nullcontext`? When would you use it? | 8–12 Yrs | Medium | T2 |
| 79 | Write a context manager that manages a DB session and handles rollback | 5–8 Yrs | High | T1 |
| 80 | How do you implement `__enter__` that can fail without calling `__exit__`? | 8–12 Yrs | Medium | T2 |
| 81 | Write a context manager for structured logging that adds request context | 8–12 Yrs | High | T1 |
| 82 | How do you make a context manager reentrant? | 8–12 Yrs | Low | T3 |
| 83 | What is `contextlib.closing`? When would you use it? | 5–8 Yrs | Medium | T2 |
| 84 | Write a context manager that temporarily patches a method on an object | 8–12 Yrs | Medium | T2 |
| 85 | How do you use a context manager to enforce resource limits (memory, CPU time)? | 12–15 Yrs | Low | T3 |
| 86 | Write a context manager that tracks all DB queries executed within a block | 8–12 Yrs | Medium | T2 |
| 87 | How do you implement a context manager that pauses and resumes a queue consumer? | 8–12 Yrs | Medium | T2 |
| 88 | What is the difference between `__exit__` returning `True` vs `False`? | 5–8 Yrs | High | T1 |
| 89 | How do you combine multiple context managers on one `with` line? | 3–5 Yrs | High | T1 |
| 90 | Write a context manager for managing a thread pool executor | 8–12 Yrs | Medium | T2 |
| 91 | How does `pytest.raises()` use context managers? | 5–8 Yrs | High | T1 |
| 92 | Write a context manager that implements a file-based transaction (atomic write) | 8–12 Yrs | Medium | T2 |
| 93 | How does `unittest.mock.patch` work as a context manager? | 5–8 Yrs | High | T1 |
| 94 | Write a context manager that catches, logs, and re-raises all exceptions | 5–8 Yrs | High | T1 |
| 95 | How do you implement a context manager that queues deferred cleanup actions? | 12–15 Yrs | Low | T3 |
| 96 | Write a context manager that enables/disables a feature flag for a code block | 8–12 Yrs | Medium | T2 |
| 97 | How do you use a context manager for distributed tracing (OpenTelemetry spans)? | 8–12 Yrs | High | T1 |
| 98 | How does `async with` differ from `with`? What protocols does it require? | 8–12 Yrs | High | T1 |
| 99 | Write a context manager that implements a semaphore-controlled critical section | 8–12 Yrs | Medium | T2 |
| 100 | Design a production-grade context manager framework for a healthcare data pipeline (transactions, logging, audit trail, rollback) | 12–15 Yrs | High | T1 |
