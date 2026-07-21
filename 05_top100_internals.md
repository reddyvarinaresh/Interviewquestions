# Top 100 Python Internals Interview Questions
> Focus: 8–15 Years | Non-FAANG | Senior/Lead/Architect Roles
> Sources: Glassdoor Senior Reports, LinkedIn Interview Posts, Reddit r/Python

---

## SECTION 1: Memory Management & Reference Counting (Q1–Q20)

### Q1
- **Question:** How does Python's memory management work? Explain reference counting.
- **Level:** 8–12 Years | **Company:** All Senior roles | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Every object has a reference count (`ob_refcnt`)
  - When count reaches 0, object is immediately deallocated
  - `sys.getrefcount(obj)` returns count (always +1 for the call itself)
  - Assignment increases count; `del`, function exit, rebinding decreases it
- **Senior Answer:** Circular references not handled by refcount; cyclic GC supplements it
- **Follow-Ups:** What is a circular reference? How does Python handle `a.ref = a`?
- **Difficulty:** Medium

---

### Q2
- **Question:** What is Python's cyclic garbage collector? How does it work?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Handles reference cycles that refcount can't detect
  - Three generations (0, 1, 2); gen 0 collected most frequently
  - `gc.collect()`, `gc.disable()`, `gc.get_threshold()`
  - Generational hypothesis: most objects die young
- **Senior Answer:** `gc.freeze()` (3.7+) prevents GC from collecting long-lived objects; use to reduce GC pauses in production
- **Architect Answer:** When to disable GC entirely for CPU-intensive batch jobs; enable only at safe points
- **Follow-Ups:** What is `gc.get_objects()`? How do you diagnose which objects are in each generation?
- **Difficulty:** Hard

---

### Q3
- **Question:** How do you diagnose a memory leak in a long-running Python service?
- **Level:** 8–12 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `tracemalloc` for tracking allocations (take snapshots, compare diffs)
  - `objgraph` to find object counts and growth over time
  - `sys.getsizeof()` for individual object sizes
  - `gc.get_objects()` to inspect live objects
  - Monitor RSS (Resident Set Size) via `psutil`
- **Real Example:** FastAPI service growing memory after each request — found Pydantic model instances held in module-level list accidentally
- **Follow-Ups:** What is the difference between RSS and virtual memory? What is `tracemalloc.take_snapshot()`?
- **Difficulty:** Hard

---

### Q4
- **Question:** What is `sys.getsizeof()`? What does it measure and what doesn't it include?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Returns size of the object itself in bytes (not nested objects)
  - `sys.getsizeof([1,2,3])` doesn't include the integers themselves
  - For deep size, implement recursive `total_size()` or use `pympler.asizeof()`
  - Tuple is smaller than list of same size (no over-allocation)
- **Difficulty:** Medium

---

### Q5
- **Question:** What is the difference between `del x` and setting `x = None`?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `del x` removes the name binding from the namespace; doesn't necessarily free memory
  - `x = None` rebinds name to None; decreases refcount of old object
  - Object freed only when ALL references drop to zero
  - `del` in a list/dict removes the entry
- **Difficulty:** Easy

---

### Q6
- **Question:** What are Python's free lists? Which types use them?
- **Level:** 12–15 Years | **Company:** Principal | **Frequency:** Low | **Tier:** T3
- **Answer Points:**
  - CPython maintains free lists for frequently created/destroyed objects
  - Small integers (-5 to 256), None, True, False — singletons
  - Float, list, dict, tuple, frame objects have free lists
  - Avoids malloc/free overhead for common object sizes
- **Difficulty:** Hard

---

### Q7
- **Question:** What is `tracemalloc`? Write a script that finds the top 5 memory-consuming allocations.
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  ```python
  import tracemalloc
  tracemalloc.start()
  # ... run code ...
  snapshot = tracemalloc.take_snapshot()
  top = snapshot.statistics('lineno')[:5]
  for stat in top:
      print(stat)
  ```
- **Follow-Ups:** How do you compare two snapshots to find new allocations?
- **Difficulty:** Medium

---

### Q8
- **Question:** What is `objgraph`? How do you use it to find what's holding references to an object?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `objgraph.show_most_common_types()` — top growing types
  - `objgraph.show_backrefs(obj)` — who holds references
  - `objgraph.show_refs(obj)` — what the object references
  - Very useful for diagnosing leaks in web services
- **Difficulty:** Medium

---

### Q9
- **Question:** Why does Python's `int` never overflow? How does it store arbitrarily large numbers?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - CPython `PyLongObject` uses array of 30-bit "digits"
  - Arbitrary precision arithmetic on the digit array
  - Small ints (-5 to 256) cached as singletons
  - `sys.int_info` shows internal details
- **Difficulty:** Medium

---

### Q10
- **Question:** What is the integer cache in CPython? Why does `a is b` return True for small ints?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - CPython pre-allocates integers from -5 to 256
  - `a = 100; b = 100; a is b` → True (same cached object)
  - `a = 1000; b = 1000; a is b` → False (different objects)
  - String interning: identifiers, compile-time string constants may be interned
- **Difficulty:** Easy

---

### Q11–Q20 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 11 | What is `weakref`? How does it prevent memory leaks in caches? | 8–12 Yrs | Medium | T2 |
| 12 | Explain `WeakValueDictionary` vs `WeakKeyDictionary`. Use cases? | 8–12 Yrs | Medium | T2 |
| 13 | What is `__del__`? Why is it unreliable for resource cleanup? | 8–12 Yrs | Medium | T2 |
| 14 | How does Python's `pymalloc` differ from system `malloc`? | 12–15 Yrs | Low | T3 |
| 15 | What is memory fragmentation in Python? How do you reduce it? | 12–15 Yrs | Low | T3 |
| 16 | How does `copy.copy()` differ from `copy.deepcopy()` at the implementation level? | 8–12 Yrs | High | T1 |
| 17 | What is the `__copy__` and `__deepcopy__` protocol? | 8–12 Yrs | Medium | T2 |
| 18 | How do you implement a memory-bounded cache in Python? | 8–12 Yrs | High | T1 |
| 19 | What is `gc.freeze()`? How does it reduce GC pauses for web servers? | 12–15 Yrs | Low | T3 |
| 20 | How does Python's GC interact with `__del__` in Python 3.4+? | 12–15 Yrs | Low | T3 |

---

## SECTION 2: GIL (Q21–Q35)

### Q21
- **Question:** What is the GIL? Why does it exist? What are its consequences?
- **Level:** 8–12 Years | **Company:** All Senior | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - GIL = Global Interpreter Lock; mutex protecting CPython interpreter state
  - Exists because CPython's memory management (refcounting) is not thread-safe
  - Only one thread executes Python bytecode at a time
  - Released during I/O operations and C extension calls
  - CPU-bound threading doesn't scale; I/O-bound threading works fine
- **Difficulty:** Hard

---

### Q22
- **Question:** Does the GIL prevent all race conditions? Why or why not?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - NO — GIL is released between bytecodes, not between Python operations
  - `count += 1` is multiple bytecodes (LOAD, BINARY_ADD, STORE) — can be interrupted
  - Thread-safe operations: `list.append()`, `dict.__setitem__` (atomic in CPython)
  - Always use `threading.Lock` for proper synchronization
- **Follow-Ups:** Which Python operations are atomic due to the GIL?
- **Difficulty:** Hard

---

### Q23
- **Question:** What is `sys.getswitchinterval()`? What was the difference between Python 2 and 3 for GIL switching?
- **Level:** 12–15 Years | **Company:** Principal | **Frequency:** Low | **Tier:** T3
- **Answer Points:**
  - Python 2: switched every 100 bytecodes (`sys.getcheckinterval()`)
  - Python 3.2+: time-based, default 5ms (`sys.getswitchinterval()`)
  - Time-based is fairer for threads doing varying work
  - `sys.setswitchinterval(0.001)` to increase thread switching frequency
- **Difficulty:** Hard

---

### Q24
- **Question:** What is PEP 703? What is Python's "no-GIL" experimental build?
- **Level:** 12–15 Years | **Company:** Architect | **Frequency:** Low | **Tier:** T3
- **Answer Points:**
  - PEP 703 (Python 3.13): Optional free-threaded CPython build
  - `python3.13t` — GIL disabled; true multi-core threading
  - Major ecosystem compatibility work needed (C extensions must be thread-safe)
  - PEP 684 (Python 3.12): Per-interpreter GIL for subinterpreters
- **Difficulty:** Hard

---

### Q25–Q35 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 25 | How does Cython release the GIL for C extensions? What is `nogil` block? | 12–15 Yrs | Low | T3 |
| 26 | Why does `time.sleep()` release the GIL? | 8–12 Yrs | Medium | T2 |
| 27 | How does `ctypes` interact with the GIL? | 12–15 Yrs | Low | T3 |
| 28 | Is `list.append()` thread-safe in CPython? Why? | 8–12 Yrs | High | T1 |
| 29 | How does `multiprocessing` bypass the GIL? | 8–12 Yrs | High | T1 |
| 30 | How do NumPy operations interact with the GIL? | 8–12 Yrs | Medium | T2 |
| 31 | Design a CPU-bound task system in Python that achieves true parallelism. | 8–12 Yrs | High | T1 |
| 32 | How does the GIL affect a FastAPI service with many concurrent requests? | 8–12 Yrs | High | T1 |
| 33 | What is the GIL's impact on a Python service running in Kubernetes with 4 CPUs? | 12–15 Yrs | Medium | T2 |
| 34 | How do `asyncio` tasks interact with the GIL? | 8–12 Yrs | High | T1 |
| 35 | What is subinterpreter isolation? How does PEP 554 plan to use it? | 12–15 Yrs | Low | T4 |

---

## SECTION 3: Python Execution Model & Bytecode (Q36–Q55)

### Q36
- **Question:** What happens when Python imports a module? Walk through the steps.
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  1. Check `sys.modules` cache (if already imported, return cached)
  2. Find module using `sys.meta_path` finders
  3. Load module source file
  4. Compile to bytecode (cached in `__pycache__/*.pyc`)
  5. Execute module code in new namespace
  6. Add to `sys.modules`
- **Follow-Ups:** What is `sys.modules`? How do you force reimport of a module?
- **Difficulty:** Medium

---

### Q37
- **Question:** What is Python bytecode? How do you inspect it?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Python source compiled to bytecode before execution
  - `dis.dis(func)` shows bytecode instructions
  - Common opcodes: `LOAD_FAST`, `STORE_FAST`, `CALL`, `RETURN_VALUE`
  - Bytecode stored in `__pycache__/*.pyc` files
  - Faster startup (skip parsing), but not machine code
- **Difficulty:** Medium

---

### Q38
- **Question:** What is a Python code object? What attributes does it have?
- **Level:** 12–15 Years | **Company:** Principal | **Frequency:** Low | **Tier:** T3
- **Answer Points:**
  - `func.__code__` is the code object
  - `co_consts`: constants used in bytecode
  - `co_varnames`: local variable names
  - `co_freevars`: variables from enclosing scope (closures)
  - `co_cellvars`: variables referenced by inner functions
  - `co_code`: raw bytecode bytes
- **Difficulty:** Hard

---

### Q39
- **Question:** How does Python handle `global` and `nonlocal` at the bytecode level?
- **Level:** 12–15 Years | **Company:** Principal | **Frequency:** Low | **Tier:** T4
- **Answer Points:**
  - `global x` → `LOAD_GLOBAL`/`STORE_GLOBAL` opcodes instead of `LOAD_FAST`/`STORE_FAST`
  - `nonlocal x` → uses `LOAD_DEREF`/`STORE_DEREF` via cell objects
  - Compiler determines this at parse time, not runtime
- **Difficulty:** Hard

---

### Q40
- **Question:** What is `__pycache__`? When is bytecode recompiled?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `.pyc` files cache compiled bytecode
  - Recompiled when source modification time or size changes
  - Python version encoded in filename: `module.cpython-311.pyc`
  - `python -B` to skip bytecode writing; `PYTHONDONTWRITEBYTECODE=1`
- **Difficulty:** Easy

---

### Q41–Q55 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 41 | What is string interning? When does Python automatically intern strings? | 8–12 Yrs | Medium | T2 |
| 42 | How does `LEGB` scope resolution work at bytecode level? | 12–15 Yrs | Low | T3 |
| 43 | What is a Python frame object? How do debuggers use frame stacks? | 12–15 Yrs | Low | T3 |
| 44 | What is `sys.settrace()`? How do profilers use it? | 12–15 Yrs | Low | T3 |
| 45 | How does `py-spy` profile Python without modifying the process? | 8–12 Yrs | Medium | T2 |
| 46 | What is `PYTHONOPTIMIZE`? What does it disable? | 8–12 Yrs | Low | T3 |
| 47 | How does Python's `eval()` differ from `exec()` in scope handling? | 8–12 Yrs | Medium | T2 |
| 48 | What is `ast.parse()`? Have you used the AST module? | 12–15 Yrs | Low | T3 |
| 49 | How does Python 3.11's specializing adaptive interpreter improve performance? | 12–15 Yrs | Low | T4 |
| 50 | What is `PYTHONHASHSEED`? Why was hash randomization introduced? | 8–12 Yrs | Medium | T2 |
| 51 | How does Python implement `for` loops? What is the iterator protocol at C level? | 12–15 Yrs | Low | T3 |
| 52 | What is the difference between `compile()`, `eval()`, and `exec()`? | 8–12 Yrs | Medium | T2 |
| 53 | How do you write a Python plugin system using `importlib`? | 12–15 Yrs | Low | T3 |
| 54 | What is `sys.path_hooks`? How do you add a custom import location? | 12–15 Yrs | Low | T4 |
| 55 | How does `zipimport` work? How do you import from a ZIP file? | 12–15 Yrs | Low | T4 |

---

## SECTION 4: Mutable/Immutable, Copy, and Object Model (Q56–Q75)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 56 | Explain Python's object model: everything is an object, pass-by-object-reference | 5–8 Yrs | High | T1 |
| 57 | What is pass-by-reference vs pass-by-value? How does Python differ from both? | 5–8 Yrs | High | T1 |
| 58 | Demonstrate what happens when you mutate a list passed to a function | 3–5 Yrs | High | T1 |
| 59 | How does Python handle tuple immutability? Can a tuple ever be "modified"? | 5–8 Yrs | Medium | T2 |
| 60 | What is `id()`? What does it return in CPython? | 5–8 Yrs | Medium | T2 |
| 61 | How does Python dict maintain insertion order (Python 3.7+)? | 8–12 Yrs | High | T1 |
| 62 | How does Python's `hash()` work for custom objects? What is the contract? | 8–12 Yrs | High | T1 |
| 63 | What is `PYTHONHASHSEED=0` and why would you use it? | 8–12 Yrs | Low | T3 |
| 64 | How does Python string interning affect `is` comparisons in web apps? | 5–8 Yrs | Medium | T2 |
| 65 | What is the difference between `==` for lists vs custom objects? | 5–8 Yrs | Medium | T2 |
| 66 | How does `frozenset` achieve hashability while `set` doesn't? | 5–8 Yrs | Medium | T2 |
| 67 | How do you implement `__hash__` correctly when you implement `__eq__`? | 8–12 Yrs | High | T1 |
| 68 | What is an "unhashable type" error? When does it occur? | 5–8 Yrs | High | T1 |
| 69 | How does Python's `bytes` vs `bytearray` mutability work? | 5–8 Yrs | Medium | T2 |
| 70 | What is `memoryview`? How does it avoid copies when slicing large bytes objects? | 8–12 Yrs | Low | T3 |
| 71 | How does Python implement `list.__iadd__` (+=) vs `list.__add__` (+)? | 8–12 Yrs | Medium | T2 |
| 72 | What is the difference between `copy.copy(list)` and `list[:]`? | 5–8 Yrs | Medium | T2 |
| 73 | How do you make a custom class deeply copyable? | 8–12 Yrs | Medium | T2 |
| 74 | What is `__reduce__` for pickling? How does pickle reconstruct objects? | 8–12 Yrs | Medium | T2 |
| 75 | How does `dataclasses.replace()` work? Is it a shallow or deep copy? | 5–8 Yrs | Medium | T2 |

---

## SECTION 5: Performance and Profiling (Q76–Q100)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 76 | What tools do you use to profile Python performance? (`cProfile`, `py-spy`, `line_profiler`) | 8–12 Yrs | High | T1 |
| 77 | What is the difference between deterministic and statistical profiling? | 8–12 Yrs | Medium | T2 |
| 78 | How do you use `cProfile` and `pstats` to find the slowest functions? | 8–12 Yrs | High | T1 |
| 79 | How does `py-spy` work? Why doesn't it require code modification? | 8–12 Yrs | Medium | T2 |
| 80 | What is `line_profiler`? When would you use it over `cProfile`? | 8–12 Yrs | Medium | T2 |
| 81 | How do you profile memory usage in Python? (`memory_profiler`, `tracemalloc`) | 8–12 Yrs | High | T1 |
| 82 | What is `timeit`? How do you benchmark small Python snippets accurately? | 5–8 Yrs | Medium | T2 |
| 83 | What are common Python performance anti-patterns? | 8–12 Yrs | High | T1 |
| 84 | Why is `"".join(list)` faster than string `+=` in a loop? | 5–8 Yrs | High | T1 |
| 85 | What is `slots` and why does it improve performance? | 8–12 Yrs | High | T1 |
| 86 | How do you speed up JSON serialization in Python? (`orjson`, `ujson`) | 8–12 Yrs | Medium | T2 |
| 87 | When would you use NumPy instead of Python lists? Why is it faster? | 8–12 Yrs | High | T1 |
| 88 | What is `Numba`? How does JIT compilation help? | 12–15 Yrs | Low | T3 |
| 89 | How does `PyPy` differ from CPython? When would you switch? | 12–15 Yrs | Low | T3 |
| 90 | How do you reduce startup time for a Python CLI tool? | 8–12 Yrs | Medium | T2 |
| 91 | What is `PYTHONSTARTUP`? How does Python's startup sequence work? | 12–15 Yrs | Low | T4 |
| 92 | How do you implement a C extension for a CPU-critical Python function? | 12–15 Yrs | Low | T3 |
| 93 | What is `Cython`? How is it different from Python + C extension? | 12–15 Yrs | Low | T3 |
| 94 | How do you use `multiprocessing.shared_memory` for zero-copy data sharing? | 12–15 Yrs | Low | T3 |
| 95 | What is `vectorize()` in NumPy? How does it avoid Python loops? | 8–12 Yrs | Medium | T2 |
| 96 | How do you optimize a Pandas-heavy data pipeline for production? | 8–12 Yrs | High | T1 |
| 97 | What are `list comprehensions` vs `generator expressions` performance tradeoffs? | 5–8 Yrs | High | T1 |
| 98 | How do you reduce GC overhead in a high-throughput Python service? | 12–15 Yrs | Medium | T2 |
| 99 | What is `struct.pack` / `struct.unpack`? How is it faster than JSON for binary data? | 12–15 Yrs | Low | T3 |
| 100 | Design a high-performance Python service that processes 1M records/minute with minimal memory footprint. | 12–15 Yrs | High | T1 |
