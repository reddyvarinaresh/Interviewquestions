# Top 100 Advanced Python Interview Questions
> Focus: 5–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise, GenAI
> Sources: Glassdoor, LinkedIn, Reddit, Medium Interview Blogs

---

## SECTION 1: Decorators (Q1–Q20)

### Q1
- **Question:** Explain how a decorator works internally. What does Python do when it sees `@decorator` above a function?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `@decorator` is syntactic sugar for `func = decorator(func)`
  - Decorator is a callable that takes a function and returns a new function
  - Executed at function definition time, not call time
  - `functools.wraps` preserves original function metadata
- **Follow-Ups:** When is the decorator body executed vs the wrapper body?
- **Difficulty:** Medium

---

### Q2
- **Question:** Write a decorator that limits how many times a function can be called (call limit decorator).
- **Level:** 5–8 Years | **Company:** All service companies | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  ```python
  def call_limit(max_calls):
      def decorator(func):
          calls = [0]
          @functools.wraps(func)
          def wrapper(*args, **kwargs):
              if calls[0] >= max_calls:
                  raise RuntimeError(f"Max calls ({max_calls}) exceeded")
              calls[0] += 1
              return func(*args, **kwargs)
          return wrapper
      return decorator
  ```
- **Follow-Ups:** How do you reset the counter? Can you make it thread-safe?
- **Difficulty:** Medium

---

### Q3
- **Question:** Write a decorator that caches results with a TTL (time-to-live) expiry.
- **Level:** 8–12 Years | **Company:** Enterprise, GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:** Dict cache with `(args, kwargs_frozen)` key; store `(result, timestamp)`; check `time.time() - timestamp > ttl` on lookup; `functools.wraps`
- **Follow-Ups:** How would you make this thread-safe? How would you store cache in Redis instead?
- **Difficulty:** Hard

---

### Q4
- **Question:** How do you write a class-based decorator? When would you prefer it over a function-based decorator?
- **Level:** 8–12 Years | **Company:** Enterprise | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Implement `__init__` (stores func/config) and `__call__` (wrapper logic); `functools.wraps(func)(self)`; useful when decorator needs complex state or multiple methods
- **Difficulty:** Medium

---

### Q5
- **Question:** How do you make a decorator work for both sync and async functions?
- **Level:** 8–12 Years | **Company:** GenAI, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  ```python
  import asyncio, functools
  def smart_decorator(func):
      @functools.wraps(func)
      def wrapper(*args, **kwargs):
          if asyncio.iscoroutinefunction(func):
              async def async_wrapper():
                  return await func(*args, **kwargs)
              return async_wrapper()
          return func(*args, **kwargs)
      return wrapper
  ```
- **Difficulty:** Hard

---

### Q6
- **Question:** Explain decorator stacking. In what order are decorators applied and executed?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Decorators apply bottom-up (innermost first); wrappers execute top-down when called; `@A @B def f` → `f = A(B(f))`; stacking order matters for auth + logging + retry
- **Difficulty:** Medium

---

### Q7
- **Question:** Write a decorator that validates function argument types against type annotations.
- **Level:** 8–12 Years | **Company:** Enterprise | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `inspect.signature()`, `func.__annotations__`, iterate bound arguments, `isinstance()` check; use `typing.get_type_hints()` to resolve forward refs
- **Difficulty:** Hard

---

### Q8
- **Question:** How does `functools.lru_cache` work internally? What is its thread safety model?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** OrderedDict + lock for thread safety; cache key is `args + sorted(kwargs.items())`; `cache_info()` and `cache_clear()`; arguments must be hashable
- **Difficulty:** Medium

---

### Q9
- **Question:** Write a decorator that adds structured logging (entry/exit + args/return value + exception) to any function.
- **Level:** 8–12 Years | **Company:** All service companies | **Frequency:** High | **Tier:** T1
- **Answer Points:** Log function name, args, kwargs on entry; log return value on success; log exception with traceback on failure; `functools.wraps`; configurable log level
- **Difficulty:** Medium

---

### Q10
- **Question:** How would you implement a singleton decorator?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  ```python
  def singleton(cls):
      instances = {}
      @functools.wraps(cls)
      def get_instance(*args, **kwargs):
          if cls not in instances:
              instances[cls] = cls(*args, **kwargs)
          return instances[cls]
      return get_instance
  ```
- **Difficulty:** Medium

---

### Q11–Q20 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 11 | Write a decorator that retries only on specific exception types | 5–8 Yrs | High | T1 |
| 12 | How do you implement a deprecation warning decorator? | 8–12 Yrs | Medium | T2 |
| 13 | Write a decorator that times out a function using `signal` or threading | 8–12 Yrs | Medium | T2 |
| 14 | How do you use `@property` with inheritance? | 5–8 Yrs | High | T1 |
| 15 | Implement a decorator that adds a method to an existing class (monkey patching via decorator) | 8–12 Yrs | Low | T3 |
| 16 | What is `functools.cache` vs `functools.lru_cache`? | 5–8 Yrs | Medium | T2 |
| 17 | Write a decorator that enforces that a method is only called from the main thread | 8–12 Yrs | Medium | T2 |
| 18 | How do you write a decorator that measures memory usage delta? | 8–12 Yrs | Low | T3 |
| 19 | Explain the difference between `@decorator` and `@decorator()` | 5–8 Yrs | High | T1 |
| 20 | How do you implement `@cached_property` from scratch? | 8–12 Yrs | Medium | T2 |

---

## SECTION 2: Generators and Iterators (Q21–Q40)

### Q21
- **Question:** What is the difference between an iterator and an iterable? Explain the iterator protocol.
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Iterable has `__iter__`; iterator has both `__iter__` (returns self) and `__next__`; `for` loop calls `iter()` then `next()` repeatedly until `StopIteration`
- **Difficulty:** Medium

---

### Q22
- **Question:** How does `yield` work? What happens inside the function when Python encounters `yield`?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `yield` turns function into generator; execution suspends at `yield`, saves frame state; resumes on next `next()` call; `StopIteration` raised when function returns
- **Difficulty:** Medium

---

### Q23
- **Question:** What is `yield from`? Write an example showing how it simplifies sub-generator delegation.
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `yield from iterable` delegates to sub-generator; passes through `send()`, `throw()`, `close()`; cleaner than `for x in sub: yield x`; used in async with `await`
- **Difficulty:** Hard

---

### Q24
- **Question:** How do you use `generator.send(value)`? What is a coroutine-style generator?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `send(val)` resumes generator and provides value as result of `yield` expression; first call must be `send(None)` or `next()`; used for coroutine-style pipelines before `asyncio`
- **Difficulty:** Hard

---

### Q25
- **Question:** Write a generator-based pipeline to process a large log file: read → filter errors → parse fields → write to DB.
- **Level:** 8–12 Years | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer Points:** Each stage is a generator; chain them: `write_db(parse(filter_errors(read_lines(file))))`; none load full file into memory; each processes one record at a time
- **Difficulty:** Medium

---

### Q26
- **Question:** What is the difference between a generator expression and a list comprehension? When do you use each?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `(x for x in data)` vs `[x for x in data]`; generator is lazy (O(1) memory); list evaluates immediately; use generator when iterating once or piping to another function
- **Difficulty:** Easy

---

### Q27
- **Question:** How do you implement an infinite sequence generator? Write one for prime numbers.
- **Level:** 5–8 Years | **Company:** Data | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Generator with `while True` loop; caller controls termination with `islice`, `takewhile`, or `next()`; `itertools.islice(primes(), 100)` for first 100 primes
- **Difficulty:** Medium

---

### Q28
- **Question:** How do you close a generator early? What happens when `generator.close()` is called?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `close()` throws `GeneratorExit` into generator; generator's `finally` block runs for cleanup; `yield` inside `except GeneratorExit` re-raises RuntimeError
- **Difficulty:** Hard

---

### Q29
- **Question:** How do you implement pagination of a database query using a generator?
- **Level:** 5–8 Years | **Company:** Data, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  ```python
  def paginate(query, page_size=100):
      offset = 0
      while True:
          batch = query.offset(offset).limit(page_size).all()
          if not batch:
              break
          yield from batch
          offset += page_size
  ```
- **Difficulty:** Medium

---

### Q30
- **Question:** What is `itertools.islice`? How do you take first N items from any iterable?
- **Level:** 5–8 Years | **Company:** Data | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `itertools.islice(gen, n)` takes first n items lazily; works on any iterable including infinite generators; doesn't consume more than needed
- **Difficulty:** Easy

---

### Q31–Q40 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 31 | What is `itertools.chain`? How does it differ from `yield from`? | 5–8 Yrs | Medium | T2 |
| 32 | How does `itertools.product` work? Give a real use case. | 5–8 Yrs | Medium | T2 |
| 33 | How do you implement a round-robin iterator over multiple queues? | 8–12 Yrs | Medium | T2 |
| 34 | What is an async generator (`async def` with `yield`)? How do you consume it with `async for`? | 8–12 Yrs | High | T1 |
| 35 | How does `itertools.tee` work? What is the memory tradeoff? | 8–12 Yrs | Low | T3 |
| 36 | Write a generator that batches items into groups of N from any iterable | 5–8 Yrs | High | T1 |
| 37 | How does `zip()` work as an iterator vs `zip_longest`? | 3–5 Yrs | High | T1 |
| 38 | What is `itertools.cycle`? Give a production use case (round-robin load balancing). | 8–12 Yrs | Medium | T2 |
| 39 | How do you implement a resumable generator that can be saved and restored? | 12–15 Yrs | Low | T3 |
| 40 | What is `itertools.accumulate`? Write an example for running totals. | 5–8 Yrs | Medium | T2 |

---

## SECTION 3: Context Managers (Q41–Q50)

### Q41
- **Question:** How does the context manager protocol work? What does `__exit__` receive?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `__enter__` called on `with` entry, returns value bound to `as` target; `__exit__(exc_type, exc_val, exc_tb)` called on exit; return `True` to suppress exception; return `False`/`None` to propagate
- **Difficulty:** Medium

---

### Q42
- **Question:** Write a context manager for a database transaction that rolls back on exception.
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  ```python
  @contextmanager
  def transaction(session):
      try:
          yield session
          session.commit()
      except Exception:
          session.rollback()
          raise
  ```
- **Difficulty:** Medium

---

### Q43
- **Question:** How do you use `contextlib.ExitStack`? When is it useful?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Dynamic stack of context managers; enter/exit a variable number of CMs; useful when you don't know at write time how many resources to manage; cleanup on exception even for partially entered CMs
- **Difficulty:** Hard

---

### Q44
- **Question:** What is `contextlib.suppress`? Implement a similar helper yourself.
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `with suppress(FileNotFoundError): os.remove(file)` — silently ignores specified exceptions; cleaner than try/except/pass pattern
- **Difficulty:** Easy

---

### Q45
- **Question:** How do you write an async context manager? What protocols does it use?
- **Level:** 8–12 Years | **Company:** GenAI, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:** `__aenter__` and `__aexit__` (both must be coroutines); `@asynccontextmanager` from `contextlib` for generator-based; used with `async with`
- **Difficulty:** Medium

---

### Q46–Q50 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 46 | How does `contextlib.contextmanager` turn a generator into a context manager? | 5–8 Yrs | High | T1 |
| 47 | Write a context manager that temporarily changes a global config value | 5–8 Yrs | Medium | T2 |
| 48 | How do you implement a context manager that measures and enforces a time budget? | 8–12 Yrs | Medium | T2 |
| 49 | What is `contextlib.nullcontext`? When is it useful? | 8–12 Yrs | Medium | T2 |
| 50 | How do you make a class usable as both a context manager and an async context manager? | 8–12 Yrs | Medium | T2 |

---

## SECTION 4: Advanced OOP — Descriptors, Metaclasses, MRO (Q51–Q70)

### Q51
- **Question:** What is the descriptor protocol? Implement a `TypeEnforced` descriptor.
- **Level:** 8–12 Years | **Company:** Senior/Lead | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `__get__`, `__set__`, `__delete__`; data descriptor (has `__set__`) takes priority over instance `__dict__`; non-data descriptor doesn't; `property` is a data descriptor
- **Difficulty:** Hard

---

### Q52
- **Question:** What is a metaclass? Give a real-world use case where you'd use one.
- **Level:** 8–12 Years | **Company:** Senior/Lead | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Class of a class (`type` by default); controls class creation; use cases: ORMs (SQLAlchemy), plugin registries, enforcing interface contracts, auto-registering handlers; prefer `__init_subclass__` for simpler cases
- **Difficulty:** Hard

---

### Q53
- **Question:** What is `__init_subclass__`? How is it a simpler alternative to metaclasses?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Called on base class when subclass is created; receives new subclass and kwargs; use for registration, validation, injection without metaclass complexity; Python 3.6+
- **Difficulty:** Hard

---

### Q54
- **Question:** Explain Python's MRO in detail. Walk through C3 linearization for a diamond inheritance.
- **Level:** 8–12 Years | **Company:** All Senior | **Frequency:** High | **Tier:** T1
- **Answer Points:** C3 linearization; `ClassName.__mro__` tuple; `super()` follows MRO; diamond: `D.__mro__ = [D, B, C, A, object]`; inconsistent MRO raises `TypeError`
- **Difficulty:** Hard

---

### Q55
- **Question:** What is monkey patching? When is it acceptable in production Python?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Dynamically modifying a class/module at runtime; acceptable in: tests (mocking), hotfix patches, adding behavior to third-party code; dangerous in production — use sparingly, document clearly
- **Difficulty:** Medium

---

### Q56–Q70 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 56 | What is `__slots__`? How does it reduce memory for high-volume objects? | 8–12 Yrs | High | T1 |
| 57 | How do you implement a lazy-loaded property using a descriptor? | 8–12 Yrs | Medium | T2 |
| 58 | What is `__set_name__`? How does it give descriptors access to their attribute name? | 8–12 Yrs | Medium | T2 |
| 59 | How does `abc.ABC` and `@abstractmethod` enforce interface contracts? | 5–8 Yrs | High | T1 |
| 60 | What is the difference between `Protocol` (structural) and `ABC` (nominal) typing? | 8–12 Yrs | High | T1 |
| 61 | How do you implement a registry pattern using `__init_subclass__`? | 8–12 Yrs | Medium | T2 |
| 62 | What is `dataclasses.field(default_factory=...)`? Why is it needed? | 5–8 Yrs | High | T1 |
| 63 | How do you implement `__repr__` that's both readable and reconstructable? | 5–8 Yrs | Medium | T2 |
| 64 | What is `__call__`? How do you make a class instance callable? | 5–8 Yrs | Medium | T2 |
| 65 | Explain `__enter__` and `__exit__` as part of the data model protocol | 5–8 Yrs | High | T1 |
| 66 | What is `__class_getitem__`? How does it support generics like `list[int]`? | 8–12 Yrs | Low | T3 |
| 67 | How do you implement deep equality for complex nested objects? | 8–12 Yrs | Medium | T2 |
| 68 | What are `__getattr__` vs `__getattribute__`? When would you override each? | 8–12 Yrs | Medium | T2 |
| 69 | How do you implement a proxy object that logs all attribute accesses? | 8–12 Yrs | Medium | T2 |
| 70 | What is `__new__` vs `__init__`? When do you override `__new__`? | 8–12 Yrs | Medium | T2 |

---

## SECTION 5: Python Type System (Q71–Q85)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 71 | What is `typing.Optional[X]`? How does it differ from `Union[X, None]`? | 5–8 Yrs | High | T1 |
| 72 | What is `typing.TypeVar`? How do you use it for generic functions? | 8–12 Yrs | Medium | T2 |
| 73 | What is `typing.TypedDict`? When would you use it over a Pydantic model? | 8–12 Yrs | Medium | T2 |
| 74 | What is `typing.Literal`? Give a real use case. | 5–8 Yrs | Medium | T2 |
| 75 | What is `typing.Annotated`? How is it used in FastAPI and Pydantic? | 8–12 Yrs | High | T1 |
| 76 | How does `mypy` work? How do you configure it in a project? | 8–12 Yrs | Medium | T2 |
| 77 | What is `pyright`? How does it compare to `mypy`? | 8–12 Yrs | Low | T3 |
| 78 | How do you type a decorator that preserves the decorated function's signature? | 8–12 Yrs | Medium | T2 |
| 79 | What is `typing.overload`? When do you need function overloads? | 8–12 Yrs | Low | T3 |
| 80 | How do you type a function that returns different types based on input? | 8–12 Yrs | Medium | T2 |
| 81 | What is `typing.Protocol` and runtime_checkable? | 8–12 Yrs | Medium | T2 |
| 82 | What is `typing.ParamSpec`? How does it type decorators properly? | 12–15 Yrs | Low | T3 |
| 83 | What is `from __future__ import annotations`? How does it affect type evaluation? | 8–12 Yrs | Medium | T2 |
| 84 | How do you type a function that accepts variadic keyword arguments of a specific type? | 8–12 Yrs | Medium | T2 |
| 85 | What is `typing.get_type_hints()`? How is it used in frameworks? | 8–12 Yrs | Low | T3 |

---

## SECTION 6: Dataclasses, NamedTuples, Enums (Q86–Q100)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 86 | What is the difference between `@dataclass`, `NamedTuple`, `TypedDict`, and `Pydantic BaseModel`? | 5–8 Yrs | High | T1 |
| 87 | How does `@dataclass(order=True)` work? How is sort order determined? | 5–8 Yrs | Medium | T2 |
| 88 | How do you exclude a field from `__init__` in a dataclass? | 5–8 Yrs | Medium | T2 |
| 89 | What is `dataclasses.fields()`? How do you iterate over a dataclass's fields? | 5–8 Yrs | Medium | T2 |
| 90 | How do you convert a dataclass to a dict? To JSON? | 5–8 Yrs | High | T1 |
| 91 | What is `@dataclass(frozen=True, slots=True)` in Python 3.10+? | 8–12 Yrs | Medium | T2 |
| 92 | How does `Enum` differ from a class with class variables? | 5–8 Yrs | High | T1 |
| 93 | What is `IntEnum`? How does it differ from regular `Enum`? | 5–8 Yrs | Medium | T2 |
| 94 | How do you serialize an `Enum` value to JSON? | 5–8 Yrs | High | T1 |
| 95 | What is `auto()` in Python Enum? How does it generate values? | 5–8 Yrs | Medium | T2 |
| 96 | How do you implement a `StrEnum` (Python 3.11+)? What is the older alternative? | 8–12 Yrs | Medium | T2 |
| 97 | What is `typing.NamedTuple` vs `collections.namedtuple`? | 5–8 Yrs | Medium | T2 |
| 98 | How do you add methods to a NamedTuple? | 5–8 Yrs | Medium | T2 |
| 99 | What is `__post_init__` in dataclasses? How do you use `field(init=False)`? | 5–8 Yrs | High | T1 |
| 100 | How do you implement a versioned data model (supporting both v1 and v2 schemas) in Python? | 12–15 Yrs | High | T1 |
