# Most Asked Python Questions — 3 to 5 Years Experience
> Target: Junior-Mid Level | Service Companies, Consulting, Healthcare, Enterprise
> Roles: Python Developer, Software Engineer, Associate Data Engineer, Junior ML Engineer
> Sources: Glassdoor, LinkedIn, Reddit, LeetCode Discuss

---

## What Interviewers Expect at 3–5 Years

At this level, interviewers test:
- Solid Python fundamentals (data types, OOP, exceptions, file I/O)
- Can write clean, working code independently
- Understands basic algorithms and data structures (no hard DSA)
- Exposure to REST APIs (Flask/FastAPI), basic SQL, one cloud platform
- Can debug simple bugs; knows how to write unit tests
- Version control (Git), virtual environments, package management

**NOT expected:** deep internals, distributed systems, async, ML, GenAI

---

## SECTION 1: Python Core (Q1–Q30)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 1 | What is the difference between a list, tuple, set, and dict? | Data Structures | T1 |
| 2 | What is mutable vs immutable? Why does it matter? | Core Python | T1 |
| 3 | How does Python's garbage collection work? | Memory | T2 |
| 4 | What are list comprehensions? When shouldn't you use them? | Pythonic | T1 |
| 5 | What is a generator? How does `yield` differ from `return`? | Generators | T1 |
| 6 | What is the difference between `is` and `==`? | Core Python | T1 |
| 7 | What is `*args` and `**kwargs`? Write an example. | Functions | T1 |
| 8 | What is a decorator? Write a simple timing decorator. | Decorators | T1 |
| 9 | What is LEGB scope? What is a closure? | Scope | T1 |
| 10 | Explain OOP: class, instance, `__init__`, `self`. | OOP | T1 |
| 11 | What is inheritance? What is `super()`? | OOP | T1 |
| 12 | What is `@staticmethod` vs `@classmethod`? | OOP | T1 |
| 13 | What is `__str__` vs `__repr__`? | Dunder methods | T1 |
| 14 | What is a context manager? Write one using `@contextmanager`. | Context Managers | T1 |
| 15 | How do you handle exceptions? Write try/except/finally. | Exceptions | T1 |
| 16 | What is a lambda function? When should you use vs avoid it? | Functions | T1 |
| 17 | What is `map()`, `filter()`, `zip()`? Give examples. | Builtins | T1 |
| 18 | How does `sorted()` work? How do you sort a list of dicts by key? | Sorting | T1 |
| 19 | What is `defaultdict`? What problem does it solve? | Collections | T1 |
| 20 | What is `Counter`? Show a real use case. | Collections | T1 |
| 21 | What is `enumerate()`? Why is it better than range(len())? | Pythonic | T1 |
| 22 | How does string formatting work? f-strings vs format() vs %? | Strings | T1 |
| 23 | How do you read a file line by line without loading it all into memory? | File I/O | T1 |
| 24 | How do you work with JSON in Python? `json.load` vs `json.loads`? | JSON | T1 |
| 25 | What is `os.path` vs `pathlib`? Which is preferred? | File System | T1 |
| 26 | What is a virtual environment? How do you create and use one? | Tooling | T1 |
| 27 | How does Python import work? What is `__init__.py`? | Modules | T1 |
| 28 | What is `if __name__ == '__main__'`? Why do you use it? | Modules | T1 |
| 29 | What is the difference between `copy()` and `deepcopy()`? | Core Python | T1 |
| 30 | What is a mutable default argument bug? How do you fix it? | Common Bugs | T1 |

---

## SECTION 2: Coding Questions (Q31–Q55)

### Q31
- **Question:** Write a function to find the two numbers in a list that sum to a target.
- **Level:** 3–5 Yrs | **Tier:** T1
- **Answer:**
  ```python
  def two_sum(nums: list[int], target: int) -> tuple[int, int] | None:
      seen = {}
      for i, n in enumerate(nums):
          complement = target - n
          if complement in seen:
              return (seen[complement], i)
          seen[n] = i
      return None
  ```

---

### Q32
- **Question:** Write a function to count word frequencies in a sentence.
- **Answer:**
  ```python
  from collections import Counter
  def word_count(sentence: str) -> Counter:
      return Counter(sentence.lower().split())
  ```

---

### Q33
- **Question:** Write a function to flatten a nested list one level deep.
- **Answer:**
  ```python
  def flatten(nested: list) -> list:
      return [item for sublist in nested for item in sublist]
  ```

---

### Q34
- **Question:** Write a function to check if a string is a palindrome.
- **Answer:**
  ```python
  def is_palindrome(s: str) -> bool:
      s = s.lower().replace(" ", "")
      return s == s[::-1]
  ```

---

### Q35
- **Question:** Write a function to remove duplicates from a list while preserving order.
- **Answer:**
  ```python
  def remove_duplicates(lst: list) -> list:
      seen = set()
      return [x for x in lst if not (x in seen or seen.add(x))]
  ```

---

### Q36–Q55 (Condensed)

| # | Question | Tier |
|---|----------|------|
| 36 | Write a function to reverse words in a sentence | T1 |
| 37 | Write a function to group anagrams from a list of words | T1 |
| 38 | Write a function to find the most common element in a list | T1 |
| 39 | Write a function to merge two sorted lists into one sorted list | T1 |
| 40 | Write a function to check if two strings are anagrams | T1 |
| 41 | Write a decorator that retries a function up to 3 times on exception | T1 |
| 42 | Write a context manager that times a block of code | T1 |
| 43 | Write a generator that yields prime numbers up to N | T2 |
| 44 | Write a class `Stack` with push, pop, peek, is_empty | T1 |
| 45 | Write a function to chunk a list into batches of size N | T1 |
| 46 | Write a function to deep-merge two dicts | T1 |
| 47 | Write a function to safely get a nested dict value by dot-path | T1 |
| 48 | Write a function to compute running average from a stream | T2 |
| 49 | Write a function to validate an email address with regex | T1 |
| 50 | Write a function to parse a log file and count error types | T1 |
| 51 | Write a function to read a CSV and return a list of dicts | T1 |
| 52 | Write a function to sort a list of Employee objects by salary desc | T1 |
| 53 | Write a function to find all duplicates in a list of IDs | T1 |
| 54 | Write a function to convert a list of tuples to a dict | T1 |
| 55 | Write a function that caches results using a decorator | T1 |

---

## SECTION 3: Web & Databases (Q56–Q75)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 56 | What is a REST API? What HTTP methods do you know? | REST | T1 |
| 57 | How do you make a GET request in Python? (`requests` library) | HTTP | T1 |
| 58 | How do you handle HTTP errors in `requests`? | HTTP | T1 |
| 59 | What is FastAPI? How does it differ from Flask? | FastAPI | T1 |
| 60 | How do you write a basic FastAPI endpoint? Show GET and POST. | FastAPI | T1 |
| 61 | What is Pydantic? How does it validate request body? | Pydantic | T1 |
| 62 | What is a status code? When do you return 200, 201, 400, 404, 500? | REST | T1 |
| 63 | How do you connect Python to a SQL database? (`psycopg2`, `sqlite3`) | SQL | T1 |
| 64 | What is SQL? Write a query to find all active members. | SQL | T1 |
| 65 | What is JOIN? Write an INNER JOIN between members and claims. | SQL | T1 |
| 66 | What is an ORM? Have you used SQLAlchemy? | ORM | T1 |
| 67 | What is a database index? Why do you add one? | SQL | T1 |
| 68 | What is `GROUP BY`? Write a query to count claims per status. | SQL | T1 |
| 69 | What is Redis? What data types does it support? | Redis | T2 |
| 70 | How would you cache a database query result in Python? | Caching | T1 |
| 71 | What is JWT? How does authentication work in a REST API? | Auth | T1 |
| 72 | What is environment variable? How do you manage configs in Python? | Config | T1 |
| 73 | What is Docker? Why is it used in Python services? | Docker | T1 |
| 74 | What is Git? How do you resolve a merge conflict? | Git | T1 |
| 75 | What is unit testing? Write a pytest test for a function. | Testing | T1 |

---

## SECTION 4: Scenario & Behavioral (Q76–Q100)

| # | Question | Focus | Tier |
|---|----------|-------|------|
| 76 | Tell me about a Python project you built. What was the hardest part? | Experience | T1 |
| 77 | How do you debug a Python script that crashes with no useful error message? | Debugging | T1 |
| 78 | Your API is returning 500 errors intermittently. How do you investigate? | Debugging | T1 |
| 79 | How do you optimize a slow Python loop that processes 100K records? | Performance | T1 |
| 80 | You need to process a 5GB file. How do you do it without loading it all into RAM? | Memory | T1 |
| 81 | How do you ensure a shared function is thread-safe? | Threading | T2 |
| 82 | What Python libraries have you used most in your projects? | Experience | T1 |
| 83 | How do you write clean, maintainable Python code? | Best Practices | T1 |
| 84 | How do you handle API rate limits when calling a third-party service? | API | T1 |
| 85 | How do you validate data before inserting into a database? | Validation | T1 |
| 86 | What is the difference between synchronous and asynchronous code? | Async | T2 |
| 87 | How do you structure a Python project? What folders do you create? | Structure | T1 |
| 88 | How do you document your Python code? | Documentation | T1 |
| 89 | How do you manage Python package versions in a project? | Tooling | T1 |
| 90 | What is type hinting? Why do you use it? | Type Hints | T1 |
| 91 | How do you mock an external API call in a pytest test? | Testing | T1 |
| 92 | What is a race condition? Have you encountered one? | Concurrency | T2 |
| 93 | How do you log messages in Python? (`logging` module) | Logging | T1 |
| 94 | What is the difference between `requirements.txt` and `pyproject.toml`? | Tooling | T1 |
| 95 | Have you used any CI/CD tools? What does your pipeline look like? | DevOps | T1 |
| 96 | How do you deploy a Python service? (Docker, cloud, bare metal) | Deployment | T1 |
| 97 | What would you improve in your last Python project? | Reflection | T1 |
| 98 | How do you stay up to date with Python best practices? | Growth | T1 |
| 99 | How would you refactor a 500-line Python function? | Refactoring | T1 |
| 100 | Walk me through designing a REST API for a task management system from scratch. | System Design | T1 |
