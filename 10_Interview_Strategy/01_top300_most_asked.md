# Top 300 Most Asked Python Interview Questions (3–15 Years)
> Non-FAANG Focus | Service Companies, Consulting, Healthcare, Enterprise, GenAI
> Sources: Glassdoor, LinkedIn, Reddit, LeetCode Discuss, Medium, GeeksforGeeks

---

## TIER 1 — Very Frequently Asked (Q1–Q80)

### Q1
- **Question:** What is the difference between a list, tuple, set, and dictionary in Python? When would you use each?
- **Level:** 3–5 Years
- **Company Type:** All service companies, consulting, healthcare
- **Round:** First Technical Round
- **Topic:** Core Python / Data Structures
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Most basic screening question; tests if candidate understands Python collections
- **Expected Answer:**
  - List: ordered, mutable, allows duplicates
  - Tuple: ordered, immutable, hashable
  - Set: unordered, mutable, no duplicates, O(1) lookup
  - Dict: key-value pairs, ordered (3.7+), O(1) lookup
- **Senior Answer:** Discuss memory implications, use cases (tuple as dict key, set for dedup), performance tradeoffs
- **Follow-Ups:** Why can't a list be a dict key? How does set check for duplicates?
- **Real Example:** Using set to deduplicate patient IDs before processing claims batch
- **Difficulty:** Easy

---

### Q2
- **Question:** Explain mutable vs immutable objects in Python. Give examples and explain why it matters in production code.
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Technical Screen
- **Topic:** Python Internals / Core
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Fundamental concept that leads to real bugs (mutable default args, shared state)
- **Expected Answer:**
  - Immutable: int, float, str, tuple, frozenset — cannot change after creation
  - Mutable: list, dict, set, custom objects — can be modified in place
  - Mutable default argument bug is classic interview trap
- **Senior Answer:** Reference semantics, `id()` to verify identity, `copy` vs `deepcopy`, function side effects
- **Follow-Ups:** What happens when you pass a mutable default argument to a function?
- **Real Example:** Bug where shared list default arg accumulated data across calls in a Flask request handler
- **Difficulty:** Easy

---

### Q3
- **Question:** What is the mutable default argument bug in Python? How do you fix it?
- **Level:** 3–5 Years
- **Company Type:** Infosys, Wipro, TCS, Cognizant, Accenture — very common
- **Round:** Technical Screen
- **Topic:** Core Python / Functions
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Classic Python gotcha; separates those who have written real Python from those who only read about it
- **Expected Answer:**
  ```python
  # Bug
  def add_item(item, lst=[]):
      lst.append(item)
      return lst
  # Fix
  def add_item(item, lst=None):
      if lst is None:
          lst = []
      lst.append(item)
      return lst
  ```
- **Senior Answer:** Explain that default args are evaluated once at function definition time, not per call
- **Follow-Ups:** Does this apply to dict and set defaults too? When is a mutable default okay?
- **Difficulty:** Easy

---

### Q4
- **Question:** How does Python's `*args` and `**kwargs` work? When would you use them?
- **Level:** 3–5 Years
- **Company Type:** All service companies
- **Round:** Technical Screen
- **Topic:** Functions
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Core Python feature used in every real codebase; decorator patterns depend on it
- **Expected Answer:** `*args` collects positional args into tuple; `**kwargs` collects keyword args into dict; used for flexible APIs, decorator wrappers, factory functions
- **Senior Answer:** Ordering rules (`*args` before `**kwargs`), `*` to force keyword-only args, unpacking with `*list` and `**dict`
- **Follow-Ups:** What is the difference between `*args` in function definition vs function call?
- **Difficulty:** Easy

---

### Q5
- **Question:** Explain Python list comprehensions. How are they different from `map()` and `filter()`? Which is more Pythonic?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Round
- **Topic:** Comprehensions / Functional
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** List comprehensions are the most Pythonic data transformation tool
- **Expected Answer:** Comprehension is more readable, preferred in PEP 8; `map`/`filter` return iterators (lazy), comprehensions return lists; use generator expression for large data
- **Senior Answer:** Performance comparison; nested comprehensions; when to prefer generator expression over list comprehension
- **Follow-Ups:** Write a dict comprehension, set comprehension, and generator expression for the same problem
- **Difficulty:** Easy

---

### Q6
- **Question:** What is a decorator in Python? Write a decorator that logs the execution time of any function.
- **Level:** 5–8 Years
- **Company Type:** Infosys, HCL, Wipro, Cognizant, Accenture, Deloitte, CGI
- **Round:** Technical Round 2
- **Topic:** Decorators
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Decorators are used everywhere — logging, auth, caching, retrying; tests real Python skills
- **Expected Answer:**
  ```python
  import time, functools
  def timer(func):
      @functools.wraps(func)
      def wrapper(*args, **kwargs):
          start = time.perf_counter()
          result = func(*args, **kwargs)
          elapsed = time.perf_counter() - start
          print(f"{func.__name__} took {elapsed:.4f}s")
          return result
      return wrapper
  ```
- **Senior Answer:** Use `functools.wraps` to preserve metadata; make it configurable with parameters; async-compatible version
- **Architect Answer:** Decorator factory pattern; stacking decorators; observability integration
- **Follow-Ups:** How do you write a decorator that accepts arguments? How do you stack multiple decorators?
- **Difficulty:** Medium

---

### Q7
- **Question:** What is a generator in Python? How is it different from a regular function? Write a generator that yields Fibonacci numbers.
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Technical Round
- **Topic:** Generators
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Generators are the go-to solution for memory-efficient data processing
- **Expected Answer:** Generator uses `yield` instead of `return`; lazy evaluation; doesn't load all data into memory; `__iter__` and `__next__` protocol
- **Senior Answer:** `yield from` for delegation; generator pipelines; `send()` method; difference between generator function and generator expression
- **Follow-Ups:** When would you use a generator over a list? How do you handle exceptions inside a generator?
- **Difficulty:** Medium

---

### Q8
- **Question:** Explain exception handling in Python. What is the difference between `except Exception` and bare `except`? When should you use `finally`?
- **Level:** 3–5 Years
- **Company Type:** All service companies, healthcare
- **Round:** Technical Screen
- **Topic:** Exception Handling
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Poor exception handling is the most common bug in production Python code
- **Expected Answer:** `except Exception` catches all non-system-exiting exceptions; bare `except` also catches `KeyboardInterrupt`, `SystemExit` — avoid it; `finally` always runs for cleanup; `else` runs only if no exception
- **Senior Answer:** Exception hierarchy; custom exceptions; `raise from`; structured logging of exceptions; never swallow exceptions silently
- **Follow-Ups:** What is exception chaining? How do you create a custom exception hierarchy?
- **Difficulty:** Easy

---

### Q9
- **Question:** What is the difference between `deep copy` and `shallow copy`? Write an example where shallow copy causes a bug.
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Technical Round
- **Topic:** Copy / Memory
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Copy bugs are common in data pipelines and config management
- **Expected Answer:** Shallow copy creates new container but references same nested objects; deep copy recursively copies all objects; `copy.copy()` vs `copy.deepcopy()`
- **Senior Answer:** Custom `__copy__`/`__deepcopy__`; performance tradeoffs; immutable data to avoid copy issues
- **Follow-Ups:** When would you prefer shallow copy over deep copy?
- **Difficulty:** Medium

---

### Q10
- **Question:** Explain Python's GIL. Why does it exist? How does it affect multi-threaded programs?
- **Level:** 8–12 Years
- **Company Type:** Infosys Senior, HCL Lead, Cognizant Lead, Accenture Lead
- **Round:** Technical Round 2 / Lead Round
- **Topic:** GIL / Concurrency
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** GIL knowledge separates mid-level from senior Python engineers
- **Expected Answer:** GIL is a mutex in CPython that allows only one thread to execute Python bytecode at a time; exists for memory safety; doesn't prevent all race conditions; released during I/O and C extensions
- **Senior Answer:** CPU-bound → multiprocessing; I/O-bound → threading or asyncio; Cython to release GIL
- **Architect Answer:** Design around GIL: message passing, worker processes, async I/O patterns
- **Follow-Ups:** Does the GIL prevent race conditions? What is the switch interval?
- **Difficulty:** Hard

---

### Q11
- **Question:** What are Python's `__init__`, `__new__`, `__str__`, `__repr__`, and `__len__` methods? When are they called?
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Technical Round
- **Topic:** Dunder Methods / OOP
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Magic methods are the core of Pythonic class design
- **Expected Answer:** `__init__`: initialize after creation; `__new__`: create instance; `__str__`: human-readable string; `__repr__`: developer representation; `__len__`: `len()` protocol
- **Senior Answer:** When `__repr__` is used instead of `__str__`; implementing full container protocol; `__repr__` should be unambiguous
- **Follow-Ups:** What is the difference between `__str__` and `__repr__`? Implement a class with both.
- **Difficulty:** Medium

---

### Q12
- **Question:** What is `@property` in Python? How does it replace getter/setter methods?
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Technical Round
- **Topic:** Property / OOP
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** `@property` is fundamental Pythonic OOP; used in every serious codebase
- **Expected Answer:** `@property` makes method accessible like attribute; `@x.setter` and `@x.deleter`; allows validation in setter; avoids Java-style getters/setters
- **Senior Answer:** Property uses descriptor protocol internally; `cached_property` for expensive computed values
- **Follow-Ups:** How does `functools.cached_property` differ from `@property`?
- **Difficulty:** Medium

---

### Q13
- **Question:** Explain Python's `with` statement and context managers. Write a custom context manager for database connection management.
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Coding Round
- **Topic:** Context Managers
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Context managers are the correct Python pattern for resource management
- **Expected Answer:** `__enter__` and `__exit__` protocol; `@contextmanager` decorator; ensures resource cleanup even on exception; `__exit__` receives exception info
- **Senior Answer:** `contextlib.suppress`, `contextlib.nullcontext`; async context managers; exception suppression
- **Follow-Ups:** What does returning `True` from `__exit__` do?
- **Difficulty:** Medium

---

### Q14
- **Question:** How does Python's `asyncio` work? Explain the event loop, coroutines, and `async/await`. What is the difference between threading and asyncio?
- **Level:** 8–12 Years
- **Company Type:** Deloitte, Accenture Senior, Thoughtworks, Publicis Sapient, GenAI companies
- **Round:** Technical Round 2
- **Topic:** Asyncio / Concurrency
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Async is now standard for Python backends and GenAI services
- **Expected Answer:** Single-threaded event loop; coroutines suspend at `await`; non-blocking I/O; threading uses OS threads (GIL limited); asyncio is cooperative, threading is preemptive
- **Senior Answer:** `asyncio.gather`, `asyncio.TaskGroup`; blocking call in async → freeze event loop; `run_in_executor` for blocking calls
- **Architect Answer:** Uvicorn + FastAPI async stack; design for 10K concurrent connections
- **Follow-Ups:** What happens if you call a blocking function inside an async function?
- **Difficulty:** Hard

---

### Q15
- **Question:** Explain Python's multiple inheritance and MRO. Write an example using mixins.
- **Level:** 8–12 Years
- **Company Type:** Thoughtworks, Publicis Sapient, ServiceNow, Atlassian-type companies
- **Round:** Technical Round 2
- **Topic:** MRO / OOP
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Mixins are used heavily in Django, Flask, and enterprise frameworks
- **Expected Answer:** C3 linearization; `ClassName.__mro__`; `super()` follows MRO; mixins add behavior without state; example with LogMixin + AuthMixin
- **Senior Answer:** Cooperative multiple inheritance; mixin ordering convention; when to prefer composition
- **Follow-Ups:** What happens if MRO is ambiguous? How does `super()` know which method to call?
- **Difficulty:** Hard

---

### Q16
- **Question:** What is the difference between `is` and `==`? When should you use each?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Technical Screen
- **Topic:** Core Python
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Common source of bugs; every interviewer uses this
- **Expected Answer:** `is` checks identity (same object in memory); `==` checks equality (same value); always use `is` for `None`, `True`, `False` checks
- **Senior Answer:** Integer cache (-5 to 256) and string interning cause surprising `is` behavior; never use `is` for string/number comparison
- **Difficulty:** Easy

---

### Q17
- **Question:** What is `lambda` in Python? When is it appropriate and when should you avoid it?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Technical Screen
- **Topic:** Functions / Lambda
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Lambda is overused and misused; tests Python style judgment
- **Expected Answer:** Anonymous one-line function; good for `sorted(key=...)`, `map()`, `filter()` inline use; avoid complex lambdas — use named functions instead
- **Follow-Ups:** Rewrite this lambda as a named function. When does PEP 8 say to avoid lambda?
- **Difficulty:** Easy

---

### Q18
- **Question:** How does Python's `sorted()` and `list.sort()` work? How do you sort a list of objects by multiple fields?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Round
- **Topic:** Sorting / Practical
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Practical Python data manipulation — used in every project
- **Expected Answer:** `sorted()` returns new list; `.sort()` in-place; `key=` argument; `operator.attrgetter` / `operator.itemgetter`; multiple fields with tuple key or `cmp_to_key`
- **Follow-Ups:** Sort a list of dicts by 'age' descending then 'name' ascending
- **Difficulty:** Easy

---

### Q19
- **Question:** Explain Python's `collections` module. Which classes have you used in production and why?
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Technical Round
- **Topic:** Collections / Data Structures
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** `collections` is essential for real Python work; shows practical experience
- **Expected Answer:** `defaultdict` (avoid KeyError), `Counter` (frequency counting), `OrderedDict` (pre-3.7), `deque` (O(1) append/pop from both ends), `namedtuple` (readable tuples), `ChainMap`
- **Senior Answer:** `Counter.most_common()`, `deque(maxlen=N)` for fixed-size sliding window, `ChainMap` for layered config
- **Follow-Ups:** When would you use `deque` instead of a list? Implement a frequency counter without Counter.
- **Difficulty:** Medium

---

### Q20
- **Question:** What is the difference between `classmethod`, `staticmethod`, and an instance method?
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Technical Round
- **Topic:** OOP / Methods
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Fundamental OOP concept tested at every level
- **Expected Answer:** Instance method takes `self`; `@classmethod` takes `cls` — used for alternative constructors; `@staticmethod` takes nothing — utility function that belongs to class namespace
- **Follow-Ups:** Write a `classmethod` that creates an instance from a JSON string.
- **Difficulty:** Easy

---

### Q21
- **Question:** How do you handle logging in a Python application? What is the difference between `print()` and `logging`?
- **Level:** 5–8 Years
- **Company Type:** All service companies, healthcare/insurance
- **Round:** Technical Round / Production Questions
- **Topic:** Logging / Production
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Logging is the #1 production debugging tool; poor logging = blind in production
- **Expected Answer:** `logging` module has levels (DEBUG/INFO/WARNING/ERROR/CRITICAL); configurable handlers (file, stream, syslog); structured logging; `print()` goes to stdout only; use `getLogger(__name__)` per module
- **Senior Answer:** Log rotation, structured JSON logging, correlation IDs, avoid logging sensitive data (PHI/PII)
- **Architect Answer:** Centralized logging with ELK/Splunk; log sampling in high-volume services
- **Follow-Ups:** How do you add a request ID to every log line in a FastAPI service?
- **Difficulty:** Medium

---

### Q22
- **Question:** What is a Python virtual environment? Why do you use it? What is the difference between `venv`, `virtualenv`, and `conda`?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** HR Technical / First Round
- **Topic:** Environment / Packaging
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Shows real-world Python development experience
- **Expected Answer:** Isolates project dependencies; avoids version conflicts; `venv` is built-in (3.3+); `virtualenv` is third-party, more features; `conda` handles non-Python packages too (used in data science)
- **Follow-Ups:** How do you export and reproduce a virtual environment? What is `pyproject.toml`?
- **Difficulty:** Easy

---

### Q23
- **Question:** Explain Python's `__slots__`. When and why would you use it?
- **Level:** 8–12 Years
- **Company Type:** Thoughtworks, Publicis Sapient, data engineering companies
- **Round:** Technical Round 2
- **Topic:** Python Internals / Memory
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Shows memory optimization awareness for high-volume object creation
- **Expected Answer:** Prevents `__dict__` creation per instance; reduces memory ~40-50% for millions of objects; define allowed attributes; breaks `__dict__` and `__weakref__` by default
- **Senior Answer:** When to use: high-volume event/message objects; `@dataclass(slots=True)` in Python 3.10+
- **Follow-Ups:** What is the tradeoff of using `__slots__`? How does it interact with inheritance?
- **Difficulty:** Medium

---

### Q24
- **Question:** How do you write a retry mechanism in Python? Implement one with exponential backoff.
- **Level:** 5–8 Years
- **Company Type:** All service companies, healthcare/insurance
- **Round:** Coding Round
- **Topic:** Production Patterns / Decorators
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Retry with backoff is in every production codebase; tests decorator + exception handling
- **Expected Answer:** Decorator with `functools.wraps`; loop with `try/except`; `time.sleep(2**attempt)`; add jitter with `random.uniform`; max retries; specific exception types
- **Senior Answer:** Async version; `tenacity` library; circuit breaker pattern on top of retry
- **Follow-Ups:** How do you add jitter? When should you NOT retry?
- **Difficulty:** Medium

---

### Q25
- **Question:** What are Python dataclasses? How do they compare to regular classes and NamedTuples?
- **Level:** 5–8 Years
- **Company Type:** All companies (very common since 2021)
- **Round:** Technical Round
- **Topic:** Dataclasses / Data Modeling
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Dataclasses are now the standard Python data modeling tool
- **Expected Answer:** `@dataclass` auto-generates `__init__`, `__repr__`, `__eq__`; `field()` for defaults; `frozen=True` for immutability; NamedTuple is immutable and tuple-like; regular class has full control
- **Senior Answer:** `__post_init__`; `field(default_factory=list)`; `@dataclass(slots=True)` in 3.10; comparison with Pydantic
- **Follow-Ups:** How do you add validation in a dataclass? When would you choose Pydantic over dataclass?
- **Difficulty:** Medium

---

### Q26
- **Question:** How does FastAPI handle request validation? Explain Pydantic's role in FastAPI.
- **Level:** 5–8 Years
- **Company Type:** GenAI companies, Thoughtworks, Publicis Sapient, modern service companies
- **Round:** Technical Round
- **Topic:** FastAPI / Pydantic
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** FastAPI is the dominant Python API framework; Pydantic v2 is everywhere
- **Expected Answer:** Pydantic models define request/response schema; FastAPI auto-validates and parses; type annotations drive everything; validation errors return 422; `model_validator`, `field_validator`
- **Senior Answer:** Pydantic v2 vs v1 differences; `model_config`; custom validators; nested model validation
- **Follow-Ups:** How do you handle optional fields? How do you return a 400 vs 422?
- **Difficulty:** Medium

---

### Q27
- **Question:** How do you implement dependency injection in FastAPI? Give an example with database session management.
- **Level:** 8–12 Years
- **Company Type:** GenAI companies, modern enterprise companies
- **Round:** Technical Round 2
- **Topic:** FastAPI / Dependency Injection
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** DI is the key architectural pattern in FastAPI; session management bugs are common
- **Expected Answer:** `Depends()` function; generator dependency with `yield` for cleanup; `get_db` pattern; `Security()` for auth; override in tests with `app.dependency_overrides`
- **Senior Answer:** Scoped dependencies; layered DI graph; async dependencies
- **Follow-Ups:** How do you test a FastAPI endpoint that has dependencies?
- **Difficulty:** Medium

---

### Q28
- **Question:** What is RAG (Retrieval-Augmented Generation)? Explain its architecture and components.
- **Level:** 8–12 Years
- **Company Type:** GenAI companies, Deloitte GenAI, Accenture AI, Fractal, Tiger Analytics, ZS
- **Round:** Technical Round / Architecture Round
- **Topic:** GenAI / RAG
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** RAG is the most implemented GenAI pattern in enterprise; every GenAI interview asks this
- **Expected Answer:** RAG = indexing + retrieval + generation; embed documents → vector store; query → embed → retrieve similar chunks → LLM generates answer with context
- **Senior Answer:** Chunking strategies; embedding models; vector DB choice (Pinecone, Chroma, Azure AI Search); reranking; context window management
- **Architect Answer:** Advanced RAG: hybrid search, HyDE, reranking, query rewriting, GraphRAG, agentic RAG
- **Follow-Ups:** What is the difference between naive RAG and advanced RAG? How do you evaluate RAG quality?
- **Difficulty:** Hard

---

### Q29
- **Question:** What is LangChain? How have you used it in a project?
- **Level:** 8–12 Years
- **Company Type:** All GenAI companies, Deloitte, Accenture, EPAM, Brillio
- **Round:** Technical Round
- **Topic:** LangChain / GenAI
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** LangChain is the most used GenAI framework in enterprise projects
- **Expected Answer:** Framework for building LLM applications; chains, prompts, memory, tools, agents; LCEL (LangChain Expression Language); integrations with OpenAI, Azure OpenAI, vector stores
- **Senior Answer:** When NOT to use LangChain (over-abstraction); LCEL runnable protocol; streaming; callbacks and tracing with LangSmith
- **Follow-Ups:** What is LCEL? How do you add streaming to a LangChain chain?
- **Difficulty:** Medium

---

### Q30
- **Question:** How does Python's memory management work? Explain reference counting and garbage collection.
- **Level:** 8–12 Years
- **Company Type:** All senior-level interviews
- **Round:** Technical Round 2
- **Topic:** Python Internals / Memory
- **Frequency:** High | **Tier:** Tier 1
- **Why Asked:** Memory management knowledge is required for senior Python engineers
- **Expected Answer:** Every object has reference count; when refcount → 0, memory freed immediately; cyclic GC handles reference cycles; three generations; `gc` module; `tracemalloc` for leak diagnosis
- **Senior Answer:** `sys.getrefcount()`, `gc.collect()`, `gc.disable()` for performance; `objgraph` for leak diagnosis
- **Follow-Ups:** What is a reference cycle? How do you detect a memory leak in production?
- **Difficulty:** Hard

---

### Q31–Q80 (Condensed — Tier 1 continued)

| # | Question | Level | Company Type | Topic | Frequency | Tier |
|---|----------|-------|-------------|-------|-----------|------|
| 31 | How do you implement caching in Python? Explain `functools.lru_cache` and Redis caching. | 5–8 Yrs | All | Caching | High | T1 |
| 32 | What is the difference between `threading` and `multiprocessing`? When to use each? | 8–12 Yrs | All | Concurrency | High | T1 |
| 33 | Explain closures in Python. What is a free variable? | 5–8 Yrs | All | Closures | High | T1 |
| 34 | How does Python's `zip()` work? How do you unzip? Give a production use case. | 3–5 Yrs | All | Built-ins | High | T1 |
| 35 | What is `enumerate()`? How do you use it and when is it better than `range(len(...))`? | 3–5 Yrs | All | Built-ins | High | T1 |
| 36 | What is duck typing in Python? How does it relate to Protocols? | 5–8 Yrs | All | OOP | High | T1 |
| 37 | Explain Python's `map()`, `filter()`, and `reduce()`. Give real use cases. | 3–5 Yrs | All | Functional | High | T1 |
| 38 | How do you read and write a large CSV file in Python without loading it into memory? | 5–8 Yrs | Data companies, Healthcare | File I/O | High | T1 |
| 39 | What is Pydantic? How do you define a model and validate data? | 5–8 Yrs | GenAI, Enterprise | Pydantic | High | T1 |
| 40 | How do you connect Python to PostgreSQL? What is SQLAlchemy and what problem does it solve? | 5–8 Yrs | All | Database | High | T1 |
| 41 | What is the N+1 query problem? How do you detect and fix it in SQLAlchemy? | 8–12 Yrs | All | ORM / SQL | High | T1 |
| 42 | What is an AI Agent? How is it different from a regular LLM call? | 8–12 Yrs | GenAI companies | Agentic AI | High | T1 |
| 43 | Explain LangGraph. How does it differ from LangChain? When would you use LangGraph? | 8–12 Yrs | GenAI companies | LangGraph | High | T1 |
| 44 | What is vector embedding? How does similarity search work? | 8–12 Yrs | GenAI, Data companies | Embeddings | High | T1 |
| 45 | What is chunking in RAG? What chunking strategies do you know? | 8–12 Yrs | GenAI companies | RAG | High | T1 |
| 46 | How do you handle exceptions in a REST API? What HTTP status codes map to which exceptions? | 5–8 Yrs | All | API / Exceptions | High | T1 |
| 47 | What is Redis? How do you use it for caching in Python? | 5–8 Yrs | All | Redis / Caching | High | T1 |
| 48 | How do you write unit tests in Python? Explain `pytest` fixtures and `unittest.mock`. | 5–8 Yrs | All | Testing | High | T1 |
| 49 | What is Celery? How do you use it for background task processing? | 8–12 Yrs | All service/enterprise | Task Queue | High | T1 |
| 50 | How do you deploy a Python application in Docker? What goes in a Dockerfile? | 5–8 Yrs | All | Docker | High | T1 |
| 51 | What is microservices architecture? How does it differ from monolithic? Tradeoffs? | 8–12 Yrs | All | Architecture | High | T1 |
| 52 | How do you handle authentication in a FastAPI application? JWT vs OAuth2. | 8–12 Yrs | All | Auth | High | T1 |
| 53 | What is a data pipeline? How do you build one in Python? | 8–12 Yrs | Data, Healthcare | Data Engineering | High | T1 |
| 54 | How do you process data with Pandas? What are common performance pitfalls? | 5–8 Yrs | Data/ML/Healthcare | Pandas | High | T1 |
| 55 | Explain Python's `__init__.py`. What is the difference between a package and a module? | 3–5 Yrs | All | Modules | High | T1 |
| 56 | How do you handle configuration management in Python applications? | 5–8 Yrs | All | Config | High | T1 |
| 57 | What is the Repository pattern? How do you implement it in Python? | 8–12 Yrs | Enterprise | Design Patterns | High | T1 |
| 58 | How do you implement pagination in a REST API in Python? | 5–8 Yrs | All | API Design | High | T1 |
| 59 | What is prompt engineering? Explain few-shot, zero-shot, chain-of-thought prompting. | 8–12 Yrs | GenAI companies | Prompt Engineering | High | T1 |
| 60 | What are hallucinations in LLMs? How do you reduce them in production? | 8–12 Yrs | GenAI companies | LLM / GenAI | High | T1 |
| 61 | How do you implement rate limiting in a Python API? | 8–12 Yrs | All | API / Rate Limiting | High | T1 |
| 62 | What is `functools.wraps`? Why must you always use it in decorators? | 5–8 Yrs | All | Decorators | High | T1 |
| 63 | Explain Python's `itertools` module. Which functions do you use most? | 5–8 Yrs | All | Itertools | High | T1 |
| 64 | What is the difference between SQL `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, and `FULL OUTER JOIN`? | 3–5 Yrs | All | SQL | High | T1 |
| 65 | How do you write a SQL query to find duplicate records? | 3–5 Yrs | All | SQL | High | T1 |
| 66 | What is Kafka? How does Python integrate with Kafka? | 8–12 Yrs | Enterprise, Healthcare | Kafka | High | T1 |
| 67 | How do you debug a slow Python API in production? | 8–12 Yrs | All | Production Debugging | High | T1 |
| 68 | What is `asyncio.gather`? How do you run multiple async tasks in parallel? | 8–12 Yrs | GenAI, Service | Asyncio | High | T1 |
| 69 | How do you implement structured logging with correlation IDs in Python? | 8–12 Yrs | All | Logging | High | T1 |
| 70 | What is vector database? Compare Pinecone, Chroma, Weaviate, Azure AI Search, Qdrant. | 8–12 Yrs | GenAI companies | Vector DB | High | T1 |
| 71 | What is tool calling / function calling in LLMs? How do you implement it? | 8–12 Yrs | GenAI companies | Agentic AI | High | T1 |
| 72 | How do you write reusable Python code across projects? Explain packaging best practices. | 8–12 Yrs | All | Packaging | High | T1 |
| 73 | What is the SOLID principle? Give Python examples for each principle. | 8–12 Yrs | Enterprise | Design Principles | High | T1 |
| 74 | How does Python's `typing` module work? What is `Optional`, `Union`, `TypeVar`? | 5–8 Yrs | All | Type System | High | T1 |
| 75 | Explain Python's `abc` module. How do you enforce interface contracts? | 8–12 Yrs | Enterprise | OOP | High | T1 |
| 76 | What is fine-tuning vs RAG? When do you choose each? | 12–15 Yrs | GenAI companies | GenAI Architecture | High | T1 |
| 77 | How do you monitor a Python microservice in production? What metrics do you collect? | 8–12 Yrs | All | Observability | High | T1 |
| 78 | Explain event-driven architecture. How does it differ from request-response? | 12–15 Yrs | All | Architecture | High | T1 |
| 79 | What is LangGraph's `StateGraph`? How do you define nodes and edges? | 8–12 Yrs | GenAI companies | LangGraph | High | T1 |
| 80 | How do you implement multi-agent systems with LangGraph? | 12–15 Yrs | GenAI companies | Agentic AI | High | T1 |

---

## TIER 2 — Frequently Asked (Q81–Q180)

| # | Question | Level | Company Type | Topic | Frequency | Tier |
|---|----------|-------|-------------|-------|-----------|------|
| 81 | What is the difference between `@staticmethod` and module-level function? | 5–8 Yrs | All | OOP | Medium | T2 |
| 82 | How do you handle circular imports in Python? | 5–8 Yrs | All | Imports | Medium | T2 |
| 83 | What is `__all__` in Python? How does it control module exports? | 5–8 Yrs | All | Modules | Medium | T2 |
| 84 | Explain the `Enum` class. How do you use it for status codes? | 5–8 Yrs | All | Data Modeling | Medium | T2 |
| 85 | How do you handle timezone-aware datetimes in Python? | 5–8 Yrs | Healthcare, Insurance | DateTime | Medium | T2 |
| 86 | What is a metaclass? When would you use one? | 8–12 Yrs | Senior/Lead | Metaclasses | Medium | T2 |
| 87 | Explain descriptor protocol in Python. | 8–12 Yrs | Senior/Lead | Descriptors | Medium | T2 |
| 88 | What is monkey patching? When is it acceptable? | 8–12 Yrs | Senior/Lead | Python Internals | Medium | T2 |
| 89 | How do you implement a singleton in Python? What are the alternatives? | 5–8 Yrs | All | Design Patterns | Medium | T2 |
| 90 | What is `weakref`? When do you use weak references? | 8–12 Yrs | Senior | Memory | Medium | T2 |
| 91 | How do you implement a circuit breaker in Python? | 8–12 Yrs | Enterprise | Resilience | Medium | T2 |
| 92 | What is the difference between `Queue.put()` and `Queue.put_nowait()`? | 8–12 Yrs | Senior | Threading | Medium | T2 |
| 93 | How do you use `ThreadPoolExecutor` vs `ProcessPoolExecutor`? | 8–12 Yrs | All | Concurrency | Medium | T2 |
| 94 | What is `contextvars` in Python? How do you use it in async applications? | 8–12 Yrs | Senior | Async | Medium | T2 |
| 95 | How do you profile Python code? Name the tools you have used. | 8–12 Yrs | All | Profiling | Medium | T2 |
| 96 | How do you detect and fix a memory leak in a Python service? | 8–12 Yrs | All | Memory | Medium | T2 |
| 97 | What is `py-spy`? How do you use it in production without restarting? | 8–12 Yrs | Senior | Profiling | Medium | T2 |
| 98 | How does SQLAlchemy's session lifecycle work? What is `expire_on_commit`? | 8–12 Yrs | All | SQLAlchemy | Medium | T2 |
| 99 | What is Alembic? How do you manage database migrations in production? | 5–8 Yrs | All | Database | Medium | T2 |
| 100 | How does connection pooling work in Python with PostgreSQL? | 8–12 Yrs | All | Database | Medium | T2 |
| 101 | Explain hybrid search in RAG. How does it combine dense + sparse retrieval? | 8–12 Yrs | GenAI | RAG | Medium | T2 |
| 102 | What is reranking in RAG? When and why do you add a reranker? | 8–12 Yrs | GenAI | RAG | Medium | T2 |
| 103 | How do you evaluate a RAG pipeline? What metrics do you use? | 8–12 Yrs | GenAI | RAG Evaluation | Medium | T2 |
| 104 | What is LLMOps? How does it differ from MLOps? | 12–15 Yrs | GenAI/Architect | LLMOps | Medium | T2 |
| 105 | How do you implement streaming responses in FastAPI? | 8–12 Yrs | GenAI | FastAPI | Medium | T2 |
| 106 | What is the MCP (Model Context Protocol)? How does it work? | 12–15 Yrs | GenAI Architect | MCP | Medium | T2 |
| 107 | Explain the ReAct agent pattern. How does it combine reasoning and acting? | 8–12 Yrs | GenAI | Agents | Medium | T2 |
| 108 | What is agent memory? Explain short-term, long-term, and episodic memory for agents. | 8–12 Yrs | GenAI | Agents | Medium | T2 |
| 109 | How do you handle Kubernetes health checks for a Python service? | 8–12 Yrs | All | Kubernetes | Medium | T2 |
| 110 | What is a ConfigMap vs Secret in Kubernetes? How do you use them in Python? | 8–12 Yrs | All | Kubernetes | Medium | T2 |
| 111 | How do you implement CI/CD for a Python application? | 8–12 Yrs | All | CI/CD | Medium | T2 |
| 112 | What is OpenTelemetry? How do you add tracing to a Python service? | 8–12 Yrs | Senior | Observability | Medium | T2 |
| 113 | How do you implement idempotency in a Python API? | 8–12 Yrs | Healthcare, Finance | API Design | Medium | T2 |
| 114 | What is event sourcing? How would you implement it in Python? | 12–15 Yrs | Architect | Architecture | Medium | T2 |
| 115 | Explain CQRS. How do you implement it with Python? | 12–15 Yrs | Architect | Architecture | Medium | T2 |
| 116 | What is a Pydantic `model_validator`? How does it differ from `field_validator`? | 5–8 Yrs | GenAI, Enterprise | Pydantic | Medium | T2 |
| 117 | How do you implement background tasks in FastAPI? | 5–8 Yrs | All | FastAPI | Medium | T2 |
| 118 | What is `httpx` vs `requests`? When do you use each? | 5–8 Yrs | All | HTTP | Medium | T2 |
| 119 | How do you implement file upload in FastAPI? | 5–8 Yrs | All | FastAPI | Medium | T2 |
| 120 | What is GraphQL? How do you implement it in Python? | 8–12 Yrs | Enterprise | API | Medium | T2 |
| 121 | How does `pandas.merge()` differ from `pandas.join()` and `pandas.concat()`? | 5–8 Yrs | Data/Healthcare | Pandas | Medium | T2 |
| 122 | What are common Pandas performance mistakes? How do you vectorize operations? | 5–8 Yrs | Data companies | Pandas | Medium | T2 |
| 123 | What is `apply()` in Pandas? When should you avoid it? | 5–8 Yrs | Data/ML | Pandas | Medium | T2 |
| 124 | Explain `groupby()` in Pandas. What is `agg()` and `transform()`? | 5–8 Yrs | Data/Healthcare | Pandas | Medium | T2 |
| 125 | What is MLflow? How do you use it for experiment tracking? | 8–12 Yrs | ML/Data companies | ML Engineering | Medium | T2 |
| 126 | How do you deploy a machine learning model as a REST API in Python? | 8–12 Yrs | ML/Data companies | ML Deployment | Medium | T2 |
| 127 | What is feature drift? How do you monitor it in production? | 8–12 Yrs | ML companies | ML Monitoring | Medium | T2 |
| 128 | How does LangChain's LCEL (LangChain Expression Language) work? | 8–12 Yrs | GenAI | LangChain | Medium | T2 |
| 129 | What is a LangChain `Runnable`? How does piping work with `|` operator? | 8–12 Yrs | GenAI | LangChain | Medium | T2 |
| 130 | What is LangSmith? How do you use it for debugging LangChain applications? | 8–12 Yrs | GenAI | LangChain | Medium | T2 |
| 131 | How do you implement conditional routing in LangGraph? | 8–12 Yrs | GenAI | LangGraph | Medium | T2 |
| 132 | What is human-in-the-loop in agentic workflows? How do you implement it in LangGraph? | 12–15 Yrs | GenAI | Agents | Medium | T2 |
| 133 | What is CrewAI? How does it differ from LangGraph for multi-agent systems? | 8–12 Yrs | GenAI | Agents | Medium | T2 |
| 134 | What is AutoGen? When would you use AutoGen over LangGraph? | 12–15 Yrs | GenAI Architect | Agents | Medium | T2 |
| 135 | What is LoRA fine-tuning? Explain its concept and when to use it. | 12–15 Yrs | GenAI | Fine-tuning | Medium | T2 |
| 136 | How do you evaluate LLM output quality? What frameworks exist? | 8–12 Yrs | GenAI | LLMOps | Medium | T2 |
| 137 | What are guardrails for LLMs? How do you implement them? | 8–12 Yrs | GenAI | Safety | Medium | T2 |
| 138 | How do you handle PII/PHI in a GenAI application? | 12–15 Yrs | Healthcare GenAI | Compliance | Medium | T2 |
| 139 | What is GraphRAG? How does it differ from standard RAG? | 12–15 Yrs | GenAI Architect | RAG | Medium | T2 |
| 140 | Explain agentic RAG. How does an agent decide when to retrieve? | 12–15 Yrs | GenAI Architect | Agents + RAG | Medium | T2 |
| 141 | How do you implement secrets management in Python? | 8–12 Yrs | All | Security | Medium | T2 |
| 142 | What is the difference between synchronous and asynchronous message processing? | 8–12 Yrs | All | Architecture | Medium | T2 |
| 143 | How does RabbitMQ differ from Kafka for Python workloads? | 8–12 Yrs | Enterprise | Messaging | Medium | T2 |
| 144 | Explain dead letter queue. How do you handle it in Python? | 8–12 Yrs | All | Messaging | Medium | T2 |
| 145 | What is saga pattern for distributed transactions? | 12–15 Yrs | Architect | Architecture | Medium | T2 |
| 146 | How do you implement health checks and graceful shutdown in Python? | 8–12 Yrs | All | Production | Medium | T2 |
| 147 | What is `Prometheus` + `Grafana`? How do you expose metrics from a Python service? | 8–12 Yrs | All | Observability | Medium | T2 |
| 148 | What is blue-green deployment? How does it apply to Python microservices? | 8–12 Yrs | All | Deployment | Medium | T2 |
| 149 | How do you handle schema versioning in APIs? | 8–12 Yrs | Enterprise | API Design | Medium | T2 |
| 150 | What is a Python `Protocol`? How does it enable structural typing? | 8–12 Yrs | Senior | Type System | Medium | T2 |
| 151 | How do you implement a state machine in Python? | 8–12 Yrs | Healthcare, Insurance | Design Patterns | Medium | T2 |
| 152 | What is the `__enter__` and `__exit__` protocol? How does exception flow through `__exit__`? | 5–8 Yrs | All | Context Managers | Medium | T2 |
| 153 | How do you implement API versioning in FastAPI? | 8–12 Yrs | All | FastAPI | Medium | T2 |
| 154 | What is RBAC? How do you implement role-based access control in FastAPI? | 8–12 Yrs | All | Auth / Security | Medium | T2 |
| 155 | How do you write an async Kafka consumer in Python? | 8–12 Yrs | Enterprise | Kafka | Medium | T2 |
| 156 | What is `structlog`? How does it improve over the standard `logging` module? | 8–12 Yrs | Senior | Logging | Medium | T2 |
| 157 | How do you implement multi-tenancy in a Python SaaS API? | 12–15 Yrs | Architect | Architecture | Medium | T2 |
| 158 | What is the actor model? How does Python implement it (e.g., Ray actors)? | 12–15 Yrs | Architect | Distributed | Medium | T2 |
| 159 | Explain Python's `sys.exc_info()`. How do you re-raise exceptions? | 5–8 Yrs | All | Exceptions | Medium | T2 |
| 160 | How do you read JSON, XML, and CSV files in Python? Which libraries do you use? | 3–5 Yrs | All | File I/O | Medium | T2 |
| 161 | What is `orjson`? Why is it faster than the standard `json` module? | 8–12 Yrs | Performance | Serialization | Medium | T2 |
| 162 | How do you parse unstructured data from LLM output? (JSON mode, structured output) | 8–12 Yrs | GenAI | LLM | Medium | T2 |
| 163 | What is the difference between `os.path` and `pathlib`? | 3–5 Yrs | All | File Handling | Medium | T2 |
| 164 | How do you batch API calls to an LLM to reduce latency and cost? | 8–12 Yrs | GenAI | LLM | Medium | T2 |
| 165 | What is Airflow? How does Python integrate with Airflow? | 8–12 Yrs | Data Engineering | Airflow | Medium | T2 |
| 166 | What is PySpark? How does it differ from Pandas? | 8–12 Yrs | Data Engineering | PySpark | Medium | T2 |
| 167 | How do you handle NULL/NaN values in Pandas? | 5–8 Yrs | Data/ML | Pandas | Medium | T2 |
| 168 | What is `scikit-learn` pipeline? How does it prevent data leakage? | 8–12 Yrs | ML companies | ML | Medium | T2 |
| 169 | How do you implement model versioning? | 8–12 Yrs | ML companies | MLOps | Medium | T2 |
| 170 | What is A/B testing in ML? How do you implement it? | 8–12 Yrs | ML/Data | ML | Medium | T2 |
| 171 | What is semantic caching for LLM calls? How does it work? | 8–12 Yrs | GenAI | LLM Optimization | Medium | T2 |
| 172 | How do you implement conversation memory in a chatbot with LangChain? | 8–12 Yrs | GenAI | LangChain | Medium | T2 |
| 173 | What is Azure OpenAI vs OpenAI API? Key differences for enterprise? | 8–12 Yrs | Healthcare/Enterprise GenAI | Azure | Medium | T2 |
| 174 | What is the difference between `gpt-4o`, `gpt-4-turbo`, and embedding models? | 8–12 Yrs | GenAI | LLM | Medium | T2 |
| 175 | How do you manage prompt versions in production? | 12–15 Yrs | GenAI Architect | LLMOps | Medium | T2 |
| 176 | What is `yield from` in Python? How does it delegate to sub-generators? | 8–12 Yrs | Senior | Generators | Medium | T2 |
| 177 | How does Python's `@dataclass(frozen=True)` compare to NamedTuple? | 5–8 Yrs | All | Data Modeling | Medium | T2 |
| 178 | What is `__post_init__` in dataclasses? Give a use case. | 5–8 Yrs | All | Dataclasses | Medium | T2 |
| 179 | How do you implement a custom exception with extra context fields? | 5–8 Yrs | All | Exceptions | Medium | T2 |
| 180 | What is `typing.TypedDict`? How does it differ from a Pydantic model? | 8–12 Yrs | All | Type System | Medium | T2 |

---

## TIER 3 — Sometimes Asked (Q181–Q250)

| # | Question | Level | Company Type | Topic | Frequency | Tier |
|---|----------|-------|-------------|-------|-----------|------|
| 181 | How does CPython's bytecode execution model work? | 8–12 Yrs | Senior | Internals | Low | T3 |
| 182 | What is `__reduce__` for custom pickling? | 8–12 Yrs | Senior | Serialization | Low | T3 |
| 183 | What is `sys.intern()` and string interning? | 8–12 Yrs | Senior | Internals | Low | T3 |
| 184 | Explain Python's buffer protocol and memoryview. | 12–15 Yrs | Architect | Performance | Low | T3 |
| 185 | How does Python's `selectors` module work? | 8–12 Yrs | Senior | Asyncio | Low | T3 |
| 186 | What is `uvloop`? When should you use it? | 8–12 Yrs | Senior | Asyncio | Low | T3 |
| 187 | How do you implement the observer pattern in Python? | 8–12 Yrs | Enterprise | Design Patterns | Low | T3 |
| 188 | What is the `__prepare__` method in metaclasses? | 12–15 Yrs | Principal | Metaclasses | Low | T3 |
| 189 | Explain `co_consts`, `co_varnames` in Python code objects. | 12–15 Yrs | Principal | Internals | Low | T3 |
| 190 | What is `hypothesis` testing? How does property-based testing work? | 8–12 Yrs | Senior | Testing | Low | T3 |
| 191 | What is consistent hashing? How is it used in distributed systems? | 12–15 Yrs | Architect | Distributed | Low | T3 |
| 192 | How do you implement a bloom filter in Python? | 12–15 Yrs | Architect | Data Structures | Low | T3 |
| 193 | What is `trio`? How does it compare to asyncio? | 12–15 Yrs | Principal | Async | Low | T3 |
| 194 | Explain semantic versioning for Python packages. | 5–8 Yrs | All | Packaging | Low | T3 |
| 195 | What is `pyproject.toml`? How does it replace `setup.py`? | 5–8 Yrs | All | Packaging | Low | T3 |
| 196 | How do you implement A2A (Agent to Agent) communication? | 12–15 Yrs | GenAI Architect | Agents | Low | T3 |
| 197 | What is Pydantic's `TypeAdapter`? When do you use it? | 8–12 Yrs | GenAI | Pydantic | Low | T3 |
| 198 | How do you implement document parsing for RAG? (PDF, DOCX, HTML) | 8–12 Yrs | GenAI/Healthcare | RAG | Low | T3 |
| 199 | What is HyDE (Hypothetical Document Embeddings)? How does it improve retrieval? | 12–15 Yrs | GenAI Architect | RAG | Low | T3 |
| 200 | How do you implement query rewriting for RAG? | 8–12 Yrs | GenAI | RAG | Low | T3 |
| 201 | What is `RAGAS`? How do you use it to evaluate RAG? | 8–12 Yrs | GenAI | RAG Evaluation | Low | T3 |
| 202 | What is a sparse encoder? How does BM25 work in hybrid search? | 8–12 Yrs | GenAI | RAG | Low | T3 |
| 203 | How does cross-encoder reranking work? When do you use it? | 8–12 Yrs | GenAI | RAG | Low | T3 |
| 204 | What is `tiktoken`? How do you count tokens in Python? | 5–8 Yrs | GenAI | LLM | Low | T3 |
| 205 | How do you implement retry with LLM API calls (rate limits, timeouts)? | 8–12 Yrs | GenAI | LLM | Low | T3 |
| 206 | What is structured output in OpenAI API? How is it different from JSON mode? | 8–12 Yrs | GenAI | LLM | Low | T3 |
| 207 | How do you implement multi-step reasoning with an LLM agent? | 8–12 Yrs | GenAI | Agents | Low | T3 |
| 208 | What is LangGraph's `interrupt`? How does it enable human-in-the-loop? | 8–12 Yrs | GenAI | LangGraph | Low | T3 |
| 209 | What is `checkpointing` in LangGraph? Why is it important for agents? | 8–12 Yrs | GenAI | LangGraph | Low | T3 |
| 210 | How do you implement agent observability with LangSmith or Langfuse? | 8–12 Yrs | GenAI | LLMOps | Low | T3 |
| 211 | What is QLoRA? How does it reduce memory for fine-tuning? | 12–15 Yrs | GenAI Architect | Fine-tuning | Low | T3 |
| 212 | What is PEFT? How does adapter tuning work? | 12–15 Yrs | GenAI Architect | Fine-tuning | Low | T3 |
| 213 | How do you implement token streaming in a Python LLM service? | 8–12 Yrs | GenAI | LLM | Low | T3 |
| 214 | What is Weights & Biases? How do you use it for LLM experiment tracking? | 8–12 Yrs | GenAI/ML | LLMOps | Low | T3 |
| 215 | How do you implement an AI governance framework? | 12–15 Yrs | GenAI Architect | AI Governance | Low | T3 |
| 216 | What is `asyncio.timeout` (Python 3.11)? How does it improve timeout handling? | 8–12 Yrs | Senior | Asyncio | Low | T3 |
| 217 | Explain `asyncio.TaskGroup` (Python 3.11). Why is it better than gather? | 8–12 Yrs | Senior | Asyncio | Low | T3 |
| 218 | How does `multiprocessing.shared_memory` work? | 8–12 Yrs | Senior | Concurrency | Low | T3 |
| 219 | What is `gc.freeze()` and how does it help with GC pauses? | 12–15 Yrs | Principal | GC | Low | T3 |
| 220 | Implement a Python decorator that validates function arguments against type hints. | 8–12 Yrs | Senior | Decorators | Low | T3 |
| 221 | What is `__class_getitem__`? How do generic aliases work? | 8–12 Yrs | Senior | Generics | Low | T3 |
| 222 | How do you implement a plugin architecture in Python? | 12–15 Yrs | Architect | Architecture | Low | T3 |
| 223 | What is `__init_subclass__`? How does it replace simple metaclasses? | 8–12 Yrs | Senior | OOP | Low | T3 |
| 224 | How do you implement a DSL (Domain Specific Language) in Python? | 12–15 Yrs | Architect | Architecture | Low | T3 |
| 225 | What is Python's `ast` module? Have you used it? | 12–15 Yrs | Principal | Tooling | Low | T3 |
| 226 | How do you handle EHR/EMR data in Python for healthcare projects? | 8–12 Yrs | Healthcare | Domain | Low | T3 |
| 227 | How do you process insurance claims data with Python? | 8–12 Yrs | Healthcare/Insurance | Domain | Low | T3 |
| 228 | What is HL7/FHIR? How do you integrate with it in Python? | 8–12 Yrs | Healthcare | Domain | Low | T3 |
| 229 | How do you implement document intelligence for insurance forms? | 8–12 Yrs | Healthcare GenAI | Domain + GenAI | Low | T3 |
| 230 | What is Azure Document Intelligence? How do you use it in Python? | 8–12 Yrs | Healthcare/Insurance | Azure | Low | T3 |
| 231 | How do you implement prior authorization automation with GenAI? | 12–15 Yrs | Healthcare GenAI Architect | Domain + GenAI | Low | T3 |
| 232 | What is `Pydantic Settings`? How do you manage environment config? | 5–8 Yrs | All | Config | Low | T3 |
| 233 | What is `tenacity`? How does it simplify retry logic in Python? | 5–8 Yrs | All | Resilience | Low | T3 |
| 234 | How do you implement API key rotation in Python? | 8–12 Yrs | All | Security | Low | T3 |
| 235 | What is `Connexion`? How does it generate APIs from OpenAPI specs? | 8–12 Yrs | Enterprise | API | Low | T3 |
| 236 | How do you implement request coalescing in Python? | 12–15 Yrs | Principal | Performance | Low | T3 |
| 237 | What is `msgpack` vs `json` vs `protobuf` for Python serialization? | 8–12 Yrs | Senior | Serialization | Low | T3 |
| 238 | How do you implement a Python-based ETL pipeline with error recovery? | 8–12 Yrs | Data Engineering | ETL | Low | T3 |
| 239 | What is `dbt`? How does Python integrate with dbt? | 8–12 Yrs | Data Engineering | dbt | Low | T3 |
| 240 | What is Delta Lake? How does Python interact with it? | 8–12 Yrs | Data Engineering | Delta Lake | Low | T3 |
| 241 | How does Python interact with Azure Blob Storage / AWS S3? | 5–8 Yrs | Cloud | Cloud Storage | Low | T3 |
| 242 | What is `boto3`? How do you use it for AWS services in Python? | 5–8 Yrs | AWS | AWS SDK | Low | T3 |
| 243 | What is `azure-sdk-for-python`? How does authentication work? | 5–8 Yrs | Azure | Azure SDK | Low | T3 |
| 244 | How do you implement Python Lambda functions on AWS? | 5–8 Yrs | Cloud | Serverless | Low | T3 |
| 245 | What is Azure Functions with Python? How does the trigger model work? | 5–8 Yrs | Azure | Serverless | Low | T3 |
| 246 | What is `locust`? How do you load test a Python API? | 8–12 Yrs | Senior | Testing | Low | T3 |
| 247 | How do you implement chaos engineering for a Python service? | 12–15 Yrs | Architect | Resilience | Low | T3 |
| 248 | What is `sentry`? How do you integrate it with a Python service? | 5–8 Yrs | All | Observability | Low | T3 |
| 249 | How do you implement distributed tracing with OpenTelemetry in Python? | 8–12 Yrs | Senior | Observability | Low | T3 |
| 250 | What is `Datadog APM`? How do you instrument a Python service? | 8–12 Yrs | Senior | Observability | Low | T3 |

---

## TIER 4 — Rare but Important (Q251–Q300)

| # | Question | Level | Company Type | Topic | Tier |
|---|----------|-------|-------------|-------|------|
| 251 | How does Python 3.12 subinterpreter support change concurrency design? | 12–15 Yrs | Principal | Python Future | T4 |
| 252 | What is Python 3.13's no-GIL build (PEP 703)? Design implications? | 12–15 Yrs | Principal/Architect | Python Future | T4 |
| 253 | How do you build a Python C extension? When is it worth it? | 12–15 Yrs | Principal | C Extensions | T4 |
| 254 | How does Cython differ from regular Python? How do you use `nogil`? | 12–15 Yrs | Principal | Performance | T4 |
| 255 | Implement a consistent hashing ring for a distributed cache in Python. | 12–15 Yrs | Architect | Distributed | T4 |
| 256 | What is `__mro_entries__`? How does it affect generic class creation? | 12–15 Yrs | Principal | Internals | T4 |
| 257 | How does `objgraph` help diagnose memory leaks? Walk through a real investigation. | 12–15 Yrs | Principal | Memory | T4 |
| 258 | Implement a Python-based distributed ID generator (Snowflake algorithm). | 12–15 Yrs | Architect | Distributed | T4 |
| 259 | What is PyPy? When should you switch from CPython to PyPy? | 12–15 Yrs | Architect | Performance | T4 |
| 260 | How do you build a domain-specific agent with memory and tools for healthcare? | 12–15 Yrs | Healthcare GenAI Architect | Agents | T4 |
| 261 | What is multimodal RAG? How do you handle images + text in retrieval? | 12–15 Yrs | GenAI Architect | RAG | T4 |
| 262 | How do you implement self-correcting agents with reflection loops? | 12–15 Yrs | GenAI Architect | Agents | T4 |
| 263 | What is agent observability? How do you trace agent decisions in production? | 12–15 Yrs | GenAI Architect | LLMOps | T4 |
| 264 | How do you implement AI governance with audit trails for regulated industries? | 12–15 Yrs | Architect/Compliance | AI Governance | T4 |
| 265 | Design a Python platform for a 10-agent multi-agent enterprise system. | 12–15 Yrs | GenAI Architect | Architecture | T4 |
| 266 | What is `Pydantic AI`? How does it compare to LangChain for agent building? | 12–15 Yrs | GenAI | Agents | T4 |
| 267 | How do you implement federated learning in Python? | 12–15 Yrs | Architect/ML | ML | T4 |
| 268 | What is the A2A protocol? How does it enable agent interoperability? | 12–15 Yrs | GenAI Architect | Agents | T4 |
| 269 | How do you implement knowledge graph-augmented RAG (GraphRAG)? | 12–15 Yrs | GenAI Architect | RAG | T4 |
| 270 | What is `Semantic Kernel`? How does it compare to LangChain? | 12–15 Yrs | GenAI Architect | Agents | T4 |
| 271 | How do you build a Python-based enterprise AI platform with multi-tenant RAG? | 12–15 Yrs | AI Architect | Architecture | T4 |
| 272 | What is `Haystack`? How does it compare to LangChain for RAG? | 12–15 Yrs | GenAI Architect | RAG Framework | T4 |
| 273 | How do you implement agentic document processing for claims automation? | 12–15 Yrs | Healthcare GenAI Architect | Domain | T4 |
| 274 | What is `Instructor` library? How does it enforce structured output from LLMs? | 8–12 Yrs | GenAI | LLM | T4 |
| 275 | How do you implement real-time RAG with streaming retrieval and generation? | 12–15 Yrs | GenAI Architect | RAG | T4 |
| 276 | What is `Langfuse`? How do you use it for LLM observability? | 8–12 Yrs | GenAI | LLMOps | T4 |
| 277 | How do you implement prompt injection prevention? | 12–15 Yrs | GenAI Architect | Security | T4 |
| 278 | What is RLHF? How does it work at a conceptual level? | 12–15 Yrs | GenAI Architect | Fine-tuning | T4 |
| 279 | How do you design a Python-based AI pipeline for prior authorization in healthcare? | 12–15 Yrs | Healthcare AI Architect | Domain | T4 |
| 280 | What is `Weights & Biases Weave`? How does it differ from W&B for LLM use? | 12–15 Yrs | GenAI | LLMOps | T4 |
| 281 | How do you implement long-context compression for LLM inputs? | 12–15 Yrs | GenAI Architect | LLM | T4 |
| 282 | What is model distillation? How would you distill a GPT-4 student? | 12–15 Yrs | GenAI Architect | Fine-tuning | T4 |
| 283 | Implement a Python-based feature store with Redis. | 12–15 Yrs | ML Architect | ML Engineering | T4 |
| 284 | How do you build a HIPAA-compliant GenAI system? | 12–15 Yrs | Healthcare AI Architect | Compliance | T4 |
| 285 | What is the MCP server spec? How do you write an MCP server in Python? | 12–15 Yrs | GenAI Architect | MCP | T4 |
| 286 | How do you implement real-time agent state persistence with LangGraph checkpointing? | 12–15 Yrs | GenAI Architect | LangGraph | T4 |
| 287 | What is `FastEmbed`? How does it differ from OpenAI embeddings? | 8–12 Yrs | GenAI | Embeddings | T4 |
| 288 | How do you implement cost optimization for LLM API calls in production? | 12–15 Yrs | GenAI Architect | LLMOps | T4 |
| 289 | What is `Unstructured.io`? How does it help with document parsing for RAG? | 8–12 Yrs | GenAI | RAG | T4 |
| 290 | How do you implement Python-based batch inference for LLMs at scale? | 12–15 Yrs | GenAI Architect | LLM | T4 |
| 291 | What is `vLLM`? How does it enable efficient LLM serving in Python? | 12–15 Yrs | GenAI Architect | LLM Serving | T4 |
| 292 | How do you implement multi-modal agents that handle images + documents? | 12–15 Yrs | GenAI Architect | Agents | T4 |
| 293 | What is context window management in long-running agent conversations? | 12–15 Yrs | GenAI Architect | Agents | T4 |
| 294 | How do you implement agent failure recovery and replanning? | 12–15 Yrs | GenAI Architect | Agents | T4 |
| 295 | What is `Agentops`? How does it monitor agents in production? | 12–15 Yrs | GenAI | LLMOps | T4 |
| 296 | How do you build a Python SDK for internal GenAI services? | 12–15 Yrs | Architect | SDK Design | T4 |
| 297 | What is `Outlines` library? How does it enforce grammar-constrained generation? | 12–15 Yrs | GenAI Architect | LLM | T4 |
| 298 | How do you implement Python-based canary deployments for AI models? | 12–15 Yrs | Architect | Deployment | T4 |
| 299 | What is knowledge distillation vs RAG? When to use each for domain adaptation? | 12–15 Yrs | GenAI Architect | GenAI | T4 |
| 300 | Design a complete Python-based GenAI platform for healthcare claim processing with agents, RAG, and compliance guardrails. | 12–15 Yrs | Healthcare AI Architect | Architecture | T4 |
