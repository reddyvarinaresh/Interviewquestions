# Top 100 GIL / Threading / Multiprocessing / Asyncio Questions
> Focus: 8–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise, GenAI
> Sources: Glassdoor, LinkedIn, Reddit, Medium Interview Blogs

---

## GIL (Q1–Q15)

### Q1
- **Question:** What is the GIL? Why does CPython have it? What are the real-world consequences?
- **Level:** 8–12 Yrs | **Company:** All Senior | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - GIL = Global Interpreter Lock; mutex on Python interpreter state
  - Needed because CPython's reference counting is not thread-safe
  - One thread executes Python bytecodes at a time
  - Released during I/O (network, disk), `time.sleep()`, C extension calls
  - CPU-bound multi-threading gives NO speedup; I/O-bound threading works fine
- **Follow-Ups:** What types of applications are hurt most by the GIL? Give a real example.
- **Difficulty:** Hard

---

### Q2
- **Question:** The GIL exists — does that mean Python threading is useless? When IS threading useful?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Threading is useful for I/O-bound workloads (network calls, DB queries, file reads)
  - While one thread waits for I/O, GIL released; others can run
  - Not useful for CPU-bound (image processing, number crunching)
  - For CPU-bound: use `multiprocessing` or `asyncio` + executor
- **Real Example:** Healthcare claims system with threading for concurrent API calls to eligibility verification services — 8x throughput improvement

---

### Q3
- **Question:** Does the GIL prevent race conditions? Explain with an example.
- **Level:** 8–12 Yrs | **Company:** All Senior | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - NO — GIL switches threads between bytecodes, not between compound operations
  - `count += 1` is: LOAD_FAST → LOAD_CONST → BINARY_ADD → STORE_FAST (4 bytecodes)
  - Thread can be interrupted between any two bytecodes
  - Always use `threading.Lock` for proper synchronization
- **Code:**
  ```python
  # Race condition despite GIL
  count = 0
  def increment():
      global count
      for _ in range(100000):
          count += 1  # NOT atomic!
  ```
- **Difficulty:** Hard

---

### Q4
- **Question:** What operations ARE thread-safe (atomic) in CPython due to the GIL?
- **Level:** 8–12 Yrs | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `list.append()` — single C-level function, GIL not released mid-operation
  - `dict.__setitem__()` — same
  - `x = y` — simple name binding (single opcode)
  - NOT safe: `x += 1`, `x = x + y`, `list[i] = list[i] + 1`
- **Difficulty:** Hard

---

### Q5–Q15 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 5 | How do you use `multiprocessing` to bypass the GIL for CPU-bound work? | 8–12 Yrs | High | T1 |
| 6 | How does `asyncio` avoid GIL problems for concurrent I/O? | 8–12 Yrs | High | T1 |
| 7 | What is `sys.getswitchinterval()`? How does thread switching work? | 12–15 Yrs | Low | T3 |
| 8 | How does NumPy release the GIL during operations? | 8–12 Yrs | Medium | T2 |
| 9 | What is PEP 703 (no-GIL)? What does it mean for Python's future? | 12–15 Yrs | Low | T3 |
| 10 | How do Cython's `nogil` blocks work to release the GIL? | 12–15 Yrs | Low | T3 |
| 11 | If you have 8 CPU cores, how many Python threads can use them simultaneously? | 8–12 Yrs | High | T1 |
| 12 | How does `ctypes` interact with the GIL for C library calls? | 12–15 Yrs | Low | T3 |
| 13 | Why does `time.sleep()` release the GIL? What does it mean for concurrent threads? | 8–12 Yrs | Medium | T2 |
| 14 | Explain how the GIL affects a Gunicorn multi-worker Python web server. | 8–12 Yrs | High | T1 |
| 15 | What is the impact of the GIL on a FastAPI service handling LLM streaming responses? | 8–12 Yrs | High | T1 |

---

## THREADING (Q16–Q35)

### Q16
- **Question:** What are `threading.Lock`, `RLock`, `Semaphore`, `Event`, `Condition`? When do you use each?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `Lock` — basic mutex; one thread at a time; non-reentrant
  - `RLock` — reentrant; same thread can acquire multiple times (recursive functions)
  - `Semaphore(n)` — allow N threads concurrently (connection pool limit)
  - `Event` — signal between threads (set/wait); graceful shutdown flag
  - `Condition` — complex state-based waiting; producer-consumer with predicate
- **Real Example:** `Semaphore(10)` to limit concurrent calls to an external API to 10 at a time
- **Difficulty:** Medium

---

### Q17
- **Question:** How do you implement a thread-safe producer-consumer using `queue.Queue`?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import threading, queue

  q = queue.Queue(maxsize=100)

  def producer():
      for item in data_source():
          q.put(item)
      q.put(None)  # sentinel

  def consumer():
      while True:
          item = q.get()
          if item is None:
              break
          process(item)
          q.task_done()

  t1 = threading.Thread(target=producer)
  t2 = threading.Thread(target=consumer)
  t1.start(); t2.start()
  q.join()
  ```
- **Follow-Ups:** How do you handle multiple consumers? How do you shut down cleanly?

---

### Q18
- **Question:** What is a deadlock? Write a Python example that deadlocks, then fix it.
- **Level:** 8–12 Yrs | **Company:** Senior | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Deadlock
  lock1, lock2 = threading.Lock(), threading.Lock()

  def thread_a():
      with lock1:
          time.sleep(0.1)
          with lock2:  # waits for lock2
              pass

  def thread_b():
      with lock2:
          time.sleep(0.1)
          with lock1:  # waits for lock1 — DEADLOCK!
              pass

  # Fix: consistent lock ordering
  def thread_b_fixed():
      with lock1:  # same order as thread_a
          with lock2:
              pass
  ```

---

### Q19
- **Question:** How does `threading.local()` work? What problem does it solve?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Thread-local storage: each thread gets its own copy of the variable
  - Common for per-thread DB connections, request context
  - `data = threading.local(); data.db_conn = get_connection()`
  - In async, prefer `contextvars.ContextVar` instead

---

### Q20
- **Question:** How do you use `ThreadPoolExecutor`? How do you choose the number of threads?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from concurrent.futures import ThreadPoolExecutor, as_completed

  with ThreadPoolExecutor(max_workers=10) as executor:
      futures = [executor.submit(fetch_url, url) for url in urls]
      for future in as_completed(futures):
          result = future.result()
  ```
- **Answer Points:** I/O-bound: `min(32, os.cpu_count() + 4)` heuristic; CPU-bound: use ProcessPoolExecutor

---

### Q21–Q35 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 21 | What is `threading.Event`? How do you use it for graceful shutdown? | 8–12 Yrs | High | T1 |
| 22 | What is `threading.Barrier`? Give a real use case. | 8–12 Yrs | Low | T3 |
| 23 | How do you implement a read-write lock in Python? | 8–12 Yrs | Medium | T2 |
| 24 | What is `concurrent.futures.as_completed()`? How does it differ from waiting for all? | 8–12 Yrs | High | T1 |
| 25 | How do you handle exceptions raised in threads? (`future.result()` re-raises) | 8–12 Yrs | High | T1 |
| 26 | What is a daemon thread? What happens to daemon threads when the main thread exits? | 8–12 Yrs | Medium | T2 |
| 27 | How do you implement a thread-safe singleton? | 8–12 Yrs | High | T1 |
| 28 | What is livelock? How does it differ from deadlock? | 8–12 Yrs | Medium | T2 |
| 29 | How do you share data between threads? What data structures are thread-safe? | 8–12 Yrs | High | T1 |
| 30 | How do you cancel a running thread in Python? (Hint: you can't directly) | 8–12 Yrs | Medium | T2 |
| 31 | What is thread starvation? How do you prevent it? | 8–12 Yrs | Medium | T2 |
| 32 | How do you implement a thread pool from scratch without `concurrent.futures`? | 12–15 Yrs | Low | T3 |
| 33 | What is `threading.Timer`? How does it differ from `asyncio`'s delayed calls? | 5–8 Yrs | Low | T3 |
| 34 | How do you run a background thread that periodically polls a status endpoint? | 8–12 Yrs | High | T1 |
| 35 | How do you test threaded code reliably? What tools help? | 8–12 Yrs | High | T1 |

---

## MULTIPROCESSING (Q36–Q55)

### Q36
- **Question:** How does Python `multiprocessing` work? How does it differ from threading?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Spawns separate OS processes; each has own Python interpreter + GIL
  - True parallelism for CPU-bound tasks
  - No shared memory by default; communicate via `Queue`, `Pipe`, `shared_memory`
  - Higher overhead: process creation, pickling for data transfer
- **Difficulty:** Medium

---

### Q37
- **Question:** What is `multiprocessing.Pool`? How does `Pool.map()` vs `Pool.imap()` vs `Pool.imap_unordered()` differ?
- **Level:** 8–12 Yrs | **Company:** Data Engineering | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `Pool.map()` — applies function to all items; blocks until all done; results in order
  - `Pool.imap()` — lazy iterator version; yields results in order as they complete
  - `Pool.imap_unordered()` — yields results as they complete (out of order); faster when order doesn't matter

---

### Q38
- **Question:** What is the difference between `fork`, `spawn`, and `forkserver` start methods?
- **Level:** 8–12 Yrs | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `fork` (default on Linux): copies parent process; fast but unsafe with threads/asyncio
  - `spawn` (default on Windows/macOS 3.8+): starts fresh Python interpreter; safe but slow
  - `forkserver`: starts a server that forks on request; compromise
  - Asyncio with multiprocessing: must use `spawn` or `forkserver`

---

### Q39
- **Question:** How do you share large data (e.g., a NumPy array) between processes without copying?
- **Level:** 8–12 Yrs | **Company:** Data, ML | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - `multiprocessing.shared_memory` (Python 3.8+): shared memory block
  - `multiprocessing.Value`, `multiprocessing.Array`: simple shared C types
  - Read-only large data: create before forking (fork copies parent's memory map)
  - `numpy.frombuffer(shm.buf)` to wrap shared memory as numpy array

---

### Q40–Q55 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 40 | How does `ProcessPoolExecutor` differ from `multiprocessing.Pool`? | 8–12 Yrs | High | T1 |
| 41 | What can't be pickled and thus can't be passed to subprocesses? | 8–12 Yrs | High | T1 |
| 42 | How do you use `multiprocessing.Queue` for inter-process communication? | 8–12 Yrs | Medium | T2 |
| 43 | What is `multiprocessing.Pipe`? How does it differ from `Queue`? | 8–12 Yrs | Medium | T2 |
| 44 | How do you handle exceptions in subprocesses? | 8–12 Yrs | High | T1 |
| 45 | How does `Pool.starmap()` work? Give a real use case. | 8–12 Yrs | Medium | T2 |
| 46 | How do you implement a fan-out/fan-in pattern with multiprocessing? | 8–12 Yrs | Medium | T2 |
| 47 | What is `multiprocessing.Manager`? When would you use it? | 8–12 Yrs | Low | T3 |
| 48 | How do you gracefully shut down a multiprocessing pool? | 8–12 Yrs | High | T1 |
| 49 | What is the overhead of multiprocessing vs threading? When does overhead dominate? | 8–12 Yrs | Medium | T2 |
| 50 | How does `Ray` compare to `multiprocessing` for distributed Python? | 12–15 Yrs | Medium | T2 |
| 51 | How do you profile CPU usage in a multiprocessing application? | 8–12 Yrs | Medium | T2 |
| 52 | What is `multiprocessing.cpu_count()`? How do you use it to set optimal workers? | 5–8 Yrs | High | T1 |
| 53 | How do you use multiprocessing for batch ML inference? | 8–12 Yrs | High | T1 |
| 54 | What are zombie processes? How does Python prevent them? | 8–12 Yrs | Low | T3 |
| 55 | How do you use multiprocessing + asyncio together? | 12–15 Yrs | Medium | T2 |

---

## ASYNCIO (Q56–Q100)

### Q56
- **Question:** How does Python's asyncio event loop work? How is it different from threading?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Single-threaded; cooperative multitasking (not preemptive like threading)
  - Event loop runs coroutines; when a coroutine awaits I/O, loop runs other coroutines
  - No race conditions for shared state (single thread); still need care with shared mutable objects
  - Threading: OS preempts threads; asyncio: coroutines voluntarily yield at `await`
- **Difficulty:** Hard

---

### Q57
- **Question:** What happens if you call a blocking function inside an `async def` function?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Blocks the entire event loop — ALL other coroutines freeze
  - Common mistake: `requests.get()`, `time.sleep()`, blocking DB calls inside `async def`
  - Fix: `await asyncio.sleep()` instead of `time.sleep()`
  - Fix: `await loop.run_in_executor(None, blocking_func)` for sync code
- **Real Example:** Calling `requests.get()` inside an async FastAPI endpoint handler — blocks all requests

---

### Q58
- **Question:** What is `asyncio.gather()`? How does it run coroutines concurrently?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  async def main():
      results = await asyncio.gather(
          fetch(url1),
          fetch(url2),
          fetch(url3),
          return_exceptions=True  # don't cancel all on one failure
      )
  ```
- **Answer Points:** Runs all coroutines concurrently in the same event loop; waits for all; `return_exceptions=True` returns exceptions instead of raising

---

### Q59
- **Question:** What is `asyncio.TaskGroup` (Python 3.11)? How does it improve error handling over `gather`?
- **Level:** 8–12 Yrs | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  async def main():
      async with asyncio.TaskGroup() as tg:
          task1 = tg.create_task(fetch(url1))
          task2 = tg.create_task(fetch(url2))
      # All tasks complete or all cancelled on any failure
  ```
- **Answer Points:** Structured concurrency; all tasks cancelled if any raises; cleaner than `gather` + error handling

---

### Q60
- **Question:** How do you handle timeouts in asyncio? Compare `asyncio.wait_for` and `asyncio.timeout` (3.11).
- **Level:** 8–12 Yrs | **Company:** GenAI, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # wait_for (pre-3.11)
  try:
      result = await asyncio.wait_for(fetch(url), timeout=5.0)
  except asyncio.TimeoutError:
      ...

  # asyncio.timeout (3.11+)
  async with asyncio.timeout(5.0):
      result = await fetch(url)
  ```

---

### Q61
- **Question:** How do you run blocking code from an async function without blocking the event loop?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import asyncio
  loop = asyncio.get_event_loop()

  # Run sync blocking function in thread pool
  result = await loop.run_in_executor(None, sync_function, arg1, arg2)

  # Or with asyncio.to_thread (Python 3.9+)
  result = await asyncio.to_thread(sync_function, arg1, arg2)
  ```

---

### Q62
- **Question:** How do you implement graceful shutdown of an asyncio application?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import asyncio, signal

  async def main():
      loop = asyncio.get_running_loop()
      stop = asyncio.Event()
      loop.add_signal_handler(signal.SIGTERM, stop.set)
      loop.add_signal_handler(signal.SIGINT, stop.set)
      await stop.wait()

  asyncio.run(main())
  ```

---

### Q63
- **Question:** How does `asyncio.Queue` implement backpressure? What is `maxsize`?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `asyncio.Queue(maxsize=N)`: `put()` blocks (suspends) when full; `get()` blocks when empty
  - Natural backpressure: producer suspends when consumer can't keep up
  - `put_nowait()` raises `QueueFull`; `get_nowait()` raises `QueueEmpty`

---

### Q64
- **Question:** How do you implement an async LLM call with streaming responses in FastAPI?
- **Level:** 8–12 Yrs | **Company:** GenAI companies | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi.responses import StreamingResponse

  @app.post("/chat")
  async def chat(request: ChatRequest):
      async def generate():
          async for chunk in openai_client.chat.completions.create(
              model="gpt-4o", messages=..., stream=True
          ):
              if chunk.choices[0].delta.content:
                  yield f"data: {chunk.choices[0].delta.content}\n\n"
      return StreamingResponse(generate(), media_type="text/event-stream")
  ```

---

### Q65–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 65 | What is `asyncio.create_task()` vs `await coroutine()`? | 8–12 Yrs | High | T1 |
| 66 | How do `asyncio.Lock`, `asyncio.Semaphore`, `asyncio.Event` work? | 8–12 Yrs | High | T1 |
| 67 | How do you limit concurrent async calls to N at a time using Semaphore? | 8–12 Yrs | High | T1 |
| 68 | What is `contextvars.ContextVar`? How is it better than `threading.local()` in async? | 8–12 Yrs | High | T1 |
| 69 | How do you propagate request trace IDs across async tasks? | 8–12 Yrs | High | T1 |
| 70 | What is `asyncio.shield()`? When do you use it? | 8–12 Yrs | Medium | T2 |
| 71 | How do you test asyncio code? (`pytest-asyncio`, mocking coroutines) | 8–12 Yrs | High | T1 |
| 72 | What is `uvloop`? How much faster is it than the default asyncio event loop? | 8–12 Yrs | Medium | T2 |
| 73 | How does `aiohttp` differ from `httpx` for async HTTP? | 8–12 Yrs | Medium | T2 |
| 74 | What is `asyncio.run()` vs `loop.run_until_complete()`? | 8–12 Yrs | Medium | T2 |
| 75 | How do you use `async for` with an async generator? | 8–12 Yrs | High | T1 |
| 76 | What is `asyncio.ensure_future()` vs `asyncio.create_task()`? | 8–12 Yrs | Medium | T2 |
| 77 | How do you handle `CancelledError` in asyncio correctly? | 8–12 Yrs | High | T1 |
| 78 | Implement a semaphore-limited async downloader for 1000 URLs | 8–12 Yrs | High | T1 |
| 79 | What is `anyio`? How does it abstract over asyncio and trio? | 12–15 Yrs | Low | T3 |
| 80 | How does `asyncio.wait()` differ from `asyncio.gather()`? | 8–12 Yrs | Medium | T2 |
| 81 | How do you implement an async retry decorator? | 8–12 Yrs | High | T1 |
| 82 | How do you implement a background task in FastAPI using asyncio? | 8–12 Yrs | High | T1 |
| 83 | How do you use `asyncio.sleep(0)` as a yield point for cooperative scheduling? | 8–12 Yrs | Medium | T2 |
| 84 | How does async Python integrate with Kafka consumers? (`aiokafka`) | 8–12 Yrs | High | T1 |
| 85 | What is structured concurrency? How does it improve error handling? | 12–15 Yrs | Medium | T2 |
| 86 | How do you implement an async circuit breaker? | 8–12 Yrs | High | T1 |
| 87 | What is the difference between `await f()` and `asyncio.run(f())`? | 5–8 Yrs | High | T1 |
| 88 | How do you detect and fix "blocking call in async" issues in production? | 8–12 Yrs | High | T1 |
| 89 | What is `asyncio.get_event_loop()` deprecation? What should you use instead? | 8–12 Yrs | Medium | T2 |
| 90 | How do you implement an async batch processor with size/time-based flushing? | 8–12 Yrs | High | T1 |
| 91 | What is `asyncio.to_thread()` (Python 3.9)? How does it improve sync/async interop? | 8–12 Yrs | High | T1 |
| 92 | How does FastAPI handle concurrent async requests? Is it truly parallel? | 8–12 Yrs | High | T1 |
| 93 | How do you implement async connection pooling for PostgreSQL? (`asyncpg`, `databases`) | 8–12 Yrs | High | T1 |
| 94 | How do you handle `asyncio.TimeoutError` from nested async calls? | 8–12 Yrs | High | T1 |
| 95 | What is `asyncio.PriorityQueue`? Give a real use case. | 8–12 Yrs | Low | T3 |
| 96 | How do you implement a health check endpoint that tests all async dependencies? | 8–12 Yrs | High | T1 |
| 97 | How does `SIGTERM` handling work in an asyncio FastAPI service on Kubernetes? | 8–12 Yrs | High | T1 |
| 98 | What is cooperative vs preemptive concurrency? Which is Python asyncio? | 8–12 Yrs | Medium | T2 |
| 99 | How do you implement an async task queue worker with dead letter handling? | 8–12 Yrs | High | T1 |
| 100 | Design a production async Python service for processing 10K concurrent LLM requests with streaming, rate limiting, and graceful shutdown. | 12–15 Yrs | High | T1 |
