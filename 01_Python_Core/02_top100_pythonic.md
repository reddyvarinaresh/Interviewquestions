# Top 100 Most Important Pythonic Interview Questions
> Focus: 3–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise, GenAI
> Sources: Glassdoor, LinkedIn, Reddit, GeeksforGeeks Candidate Reports

---

## What "Pythonic" Means in Interviews
Interviewers at service companies (Infosys, TCS, Accenture, Deloitte, Cognizant, Thoughtworks) and healthcare/enterprise companies (Optum, Carelon, ServiceNow) consistently ask candidates to write code "the Python way" — clean, readable, idiomatic. These questions test real Python fluency.

---

## Q1
- **Question:** Rewrite this Java-style loop in a Pythonic way:
  ```python
  result = []
  for i in range(len(data)):
      if data[i] > 0:
          result.append(data[i] * 2)
  ```
- **Level:** 3–5 Years
- **Company Type:** All service companies (extremely common first-round question)
- **Round:** Coding Screen
- **Topic:** List Comprehension
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `result = [x * 2 for x in data if x > 0]`
- **Senior Answer:** Discuss generator expression for large data: `(x * 2 for x in data if x > 0)`
- **Follow-Ups:** When would you NOT use a comprehension?
- **Difficulty:** Easy

---

## Q2
- **Question:** How do you swap two variables in Python without a temp variable?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Screen
- **Topic:** Tuple Unpacking
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `a, b = b, a` — Python evaluates right side first as a tuple, then unpacks
- **Difficulty:** Easy

---

## Q3
- **Question:** How do you iterate over a list and get both the index and value?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** enumerate()
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `for i, val in enumerate(data):` — avoid `range(len(data))` anti-pattern
- **Follow-Ups:** How does `enumerate(data, start=1)` work?
- **Difficulty:** Easy

---

## Q4
- **Question:** How do you merge two dictionaries in Python 3.9+? How did you do it before 3.9?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Screen
- **Topic:** Dict Operations
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** Python 3.9+: `merged = dict1 | dict2`; Pre-3.9: `{**dict1, **dict2}` or `dict1.update(dict2)` (in-place)
- **Difficulty:** Easy

---

## Q5
- **Question:** How do you group a list of dictionaries by a key value in Python?
- **Level:** 3–5 Years
- **Company Type:** Data companies, healthcare
- **Round:** Coding Round
- **Topic:** groupby / collections
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:**
  ```python
  from collections import defaultdict
  grouped = defaultdict(list)
  for item in data:
      grouped[item['department']].append(item)
  ```
  Or using `itertools.groupby` (requires sorted input)
- **Follow-Ups:** How does `itertools.groupby` require the data to be pre-sorted?
- **Difficulty:** Easy

---

## Q6
- **Question:** How do you remove duplicates from a list while preserving order?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** Deduplication
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:**
  ```python
  seen = set()
  result = [x for x in data if not (x in seen or seen.add(x))]
  ```
  Or: `list(dict.fromkeys(data))` — Pythonic 3.7+ approach using ordered dict
- **Difficulty:** Easy

---

## Q7
- **Question:** How do you flatten a list of lists in Python?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** Comprehensions / itertools
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:**
  ```python
  flat = [item for sublist in nested for item in sublist]
  # Or:
  import itertools
  flat = list(itertools.chain.from_iterable(nested))
  ```
- **Follow-Ups:** How would you flatten a deeply nested (unknown depth) list?
- **Difficulty:** Easy

---

## Q8
- **Question:** How do you count the frequency of elements in a list in Python?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** Counter
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:**
  ```python
  from collections import Counter
  freq = Counter(data)
  most_common = freq.most_common(3)
  ```
- **Follow-Ups:** How would you do this without `Counter`?
- **Difficulty:** Easy

---

## Q9
- **Question:** How do you sort a list of dictionaries by a key? What if you want descending order or sorting by multiple keys?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Round
- **Topic:** Sorting
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:**
  ```python
  # By one key ascending
  sorted(people, key=lambda x: x['age'])
  # Descending
  sorted(people, key=lambda x: x['age'], reverse=True)
  # Multiple keys
  sorted(people, key=lambda x: (x['dept'], -x['age']))
  ```
- **Difficulty:** Easy

---

## Q10
- **Question:** How do you safely get a value from a dictionary that might not have the key?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Screen
- **Topic:** Dict / Defensive Coding
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `value = data.get('key', default_value)` — never use `data['key']` without knowing the key exists
- **Follow-Ups:** What is `setdefault()`? How does `defaultdict` help?
- **Difficulty:** Easy

---

## Q11
- **Question:** How do you read a JSON file and transform it in Python?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** JSON / File I/O
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `json.load(f)` for file, `json.loads(s)` for string; use comprehensions to transform; `json.dump()` to write
- **Difficulty:** Easy

---

## Q12
- **Question:** How do you write a Python function that accepts any keyword arguments and passes them to another function?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** **kwargs / Forwarding
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** Use `**kwargs` in function signature and `func(**kwargs)` to forward; common pattern in wrapper functions and decorators
- **Difficulty:** Easy

---

## Q13
- **Question:** How do you write a Pythonic context manager using `@contextmanager` from `contextlib`?
- **Level:** 5–8 Years
- **Company Type:** All companies
- **Round:** Technical Round
- **Topic:** Context Managers
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:**
  ```python
  from contextlib import contextmanager
  @contextmanager
  def managed_resource():
      resource = acquire()
      try:
          yield resource
      finally:
          release(resource)
  ```
- **Difficulty:** Medium

---

## Q14
- **Question:** How do you use `zip()` to iterate over two lists simultaneously? How do you handle lists of different lengths?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** zip()
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `zip(a, b)` stops at shortest; `itertools.zip_longest(a, b, fillvalue=None)` for equal-length traversal
- **Difficulty:** Easy

---

## Q15
- **Question:** How do you use Python's `any()` and `all()` functions? Write a real use case.
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** Built-ins
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `any(x > 0 for x in values)` — lazy short-circuit evaluation; `all()` for validation — `all(field in record for field in required_fields)`
- **Difficulty:** Easy

---

## Q16
- **Question:** What is the Walrus operator (`:=`)? How does it improve readability?
- **Level:** 5–8 Years
- **Company Type:** All companies (common since Python 3.8 is standard)
- **Round:** Technical Screen
- **Topic:** Python 3.8+ Features
- **Frequency:** Medium | **Tier:** Tier 2
- **Expected Answer:**
  ```python
  # Without walrus
  value = compute()
  if value:
      use(value)
  # With walrus
  if value := compute():
      use(value)
  ```
  Also used in `while` loops to avoid reading before checking
- **Difficulty:** Easy

---

## Q17
- **Question:** How do you unpack a list or tuple in Python? What is `*` unpacking?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Screen
- **Topic:** Unpacking
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:**
  ```python
  first, *rest = [1, 2, 3, 4]
  a, b, *middle, last = range(10)
  ```
- **Follow-Ups:** How do you use `*` in function calls to unpack a list as positional args?
- **Difficulty:** Easy

---

## Q18
- **Question:** How do you write a dict comprehension to invert a dictionary (swap keys and values)?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Coding Screen
- **Topic:** Dict Comprehension
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** `inverted = {v: k for k, v in original.items()}` — note: assumes unique values
- **Follow-Ups:** What happens if values are not unique?
- **Difficulty:** Easy

---

## Q19
- **Question:** How do you implement a defaultdict to build a graph (adjacency list)?
- **Level:** 5–8 Years
- **Company Type:** Data/enterprise companies
- **Round:** Coding Round
- **Topic:** defaultdict
- **Frequency:** Medium | **Tier:** Tier 2
- **Expected Answer:**
  ```python
  from collections import defaultdict
  graph = defaultdict(list)
  for u, v in edges:
      graph[u].append(v)
  ```
- **Difficulty:** Easy

---

## Q20
- **Question:** How do you write a Pythonic way to check if a key exists in a dict before accessing it?
- **Level:** 3–5 Years
- **Company Type:** All companies
- **Round:** Screen
- **Topic:** Dict / Defensive Coding
- **Frequency:** High | **Tier:** Tier 1
- **Expected Answer:** Use `.get()` or `if key in d:` — never catch `KeyError` for normal flow control
- **Difficulty:** Easy

---

## Q21–Q100 (Condensed)

| # | Question | Level | Topic | Frequency | Tier |
|---|----------|-------|-------|-----------|------|
| 21 | How do you use `collections.namedtuple`? When is it better than a dict or class? | 5–8 Yrs | NamedTuple | High | T1 |
| 22 | How do you implement a generator that lazily reads lines from a large file? | 5–8 Yrs | Generators / File | High | T1 |
| 23 | How do you use `functools.partial` to create pre-configured functions? | 5–8 Yrs | Functional | Medium | T2 |
| 24 | What is the difference between `list.append()` and `list.extend()`? | 3–5 Yrs | List | High | T1 |
| 25 | How do you reverse a list or string in Python? (multiple ways) | 3–5 Yrs | Slicing | High | T1 |
| 26 | How do you use `str.join()` to concatenate a list of strings? Why not use `+=` in a loop? | 3–5 Yrs | Strings | High | T1 |
| 27 | How do you format strings in Python? Compare `%`, `.format()`, and f-strings. | 3–5 Yrs | Strings | High | T1 |
| 28 | How do you split a string by multiple delimiters? | 3–5 Yrs | Strings | Medium | T2 |
| 29 | How do you check if a string starts with any of several prefixes? | 3–5 Yrs | Strings | Medium | T2 |
| 30 | How do you parse a nested JSON and extract all values for a given key? | 5–8 Yrs | JSON / Recursion | High | T1 |
| 31 | How do you convert a list of tuples into a dictionary? | 3–5 Yrs | Dict | High | T1 |
| 32 | How do you write a one-liner to transpose a matrix (list of lists)? | 5–8 Yrs | zip / Comprehension | Medium | T2 |
| 33 | How do you implement a pipeline of functions in Python? | 5–8 Yrs | Functional | Medium | T2 |
| 34 | How do you use `itertools.chain` to iterate over multiple iterables? | 5–8 Yrs | itertools | Medium | T2 |
| 35 | How do you use `itertools.groupby` correctly? What is the gotcha? | 5–8 Yrs | itertools | Medium | T2 |
| 36 | How do you write a Pythonic retry loop? | 5–8 Yrs | Patterns | High | T1 |
| 37 | How do you implement a memoization decorator from scratch? | 5–8 Yrs | Decorators | High | T1 |
| 38 | How do you use `*` and `**` for argument forwarding in decorators? | 5–8 Yrs | Decorators | High | T1 |
| 39 | How do you implement a Python decorator that measures memory usage? | 8–12 Yrs | Decorators / Memory | Medium | T2 |
| 40 | How do you write a class method that acts as an alternative constructor? | 5–8 Yrs | OOP | High | T1 |
| 41 | How do you implement `__eq__` and `__hash__` for a custom class? | 5–8 Yrs | OOP | High | T1 |
| 42 | How do you implement `__iter__` and `__next__` for a custom sequence? | 5–8 Yrs | OOP | High | T1 |
| 43 | How do you use `@dataclass` to replace a verbose `__init__`? | 5–8 Yrs | Dataclasses | High | T1 |
| 44 | How do you implement a Pythonic builder pattern? | 8–12 Yrs | Design Patterns | Medium | T2 |
| 45 | How do you use `typing.Optional` and `Union` in function signatures? | 5–8 Yrs | Type Hints | High | T1 |
| 46 | How do you write type-hinted code for a dict with mixed value types? | 5–8 Yrs | Type Hints | Medium | T2 |
| 47 | How do you process a CSV with missing values in a Pythonic way? | 5–8 Yrs | Data Processing | High | T1 |
| 48 | How do you read an environment variable with a default value in Python? | 3–5 Yrs | Config | High | T1 |
| 49 | How do you use `pathlib` to build cross-platform file paths? | 3–5 Yrs | File I/O | Medium | T2 |
| 50 | How do you write a Python script that accepts command-line arguments? | 3–5 Yrs | argparse | Medium | T2 |
| 51 | How do you log structured data (dict) in Python logging? | 5–8 Yrs | Logging | High | T1 |
| 52 | How do you create a Python package with proper `__init__.py`? | 5–8 Yrs | Packaging | Medium | T2 |
| 53 | How do you write a Pythonic null check for nested dict values? | 3–5 Yrs | None Handling | High | T1 |
| 54 | How do you use `setdefault()` vs `defaultdict`? | 5–8 Yrs | Dict | Medium | T2 |
| 55 | How do you implement a sliding window over a list? | 5–8 Yrs | Algorithms | Medium | T2 |
| 56 | How do you use `zip()` with `dict()` to create a dict from two lists? | 3–5 Yrs | Dict / zip | High | T1 |
| 57 | How do you use `map()` with multiple iterables? | 5–8 Yrs | Functional | Medium | T2 |
| 58 | How do you write a Python function that returns multiple values? | 3–5 Yrs | Functions | High | T1 |
| 59 | How do you implement a Python decorator that restricts access by role? | 8–12 Yrs | Auth / Decorators | High | T1 |
| 60 | How do you use `functools.reduce()` to compute a running total? | 5–8 Yrs | Functional | Medium | T2 |
| 61 | How do you implement a chainable/fluent interface in Python? | 8–12 Yrs | Design Patterns | Medium | T2 |
| 62 | How do you handle optional chaining for deeply nested dict access? | 5–8 Yrs | Dict | High | T1 |
| 63 | How do you use `str.strip()`, `lstrip()`, `rstrip()` effectively? | 3–5 Yrs | Strings | High | T1 |
| 64 | How do you check if all elements in a list satisfy a condition? | 3–5 Yrs | Built-ins | High | T1 |
| 65 | How do you implement a custom sort key for objects with complex ordering? | 5–8 Yrs | Sorting | Medium | T2 |
| 66 | How do you use `heapq.nlargest()` and `nsmallest()` in Python? | 5–8 Yrs | heapq | Medium | T2 |
| 67 | How do you implement a sliding window counter using `deque`? | 5–8 Yrs | deque | Medium | T2 |
| 68 | How do you use `collections.OrderedDict` for LRU cache logic? | 8–12 Yrs | Collections | Medium | T2 |
| 69 | How do you use `dataclasses.asdict()` and `asdict()` for serialization? | 5–8 Yrs | Dataclasses | Medium | T2 |
| 70 | How do you use `@property` with `@setter` for validation? | 5–8 Yrs | OOP | High | T1 |
| 71 | How do you implement a Python function that caches results per argument combination? | 5–8 Yrs | Caching | High | T1 |
| 72 | How do you clean and normalize strings in a Pandas column? | 5–8 Yrs | Pandas | High | T1 |
| 73 | How do you pivot a Pandas DataFrame? | 5–8 Yrs | Pandas | Medium | T2 |
| 74 | How do you chain multiple Pandas operations readably? | 5–8 Yrs | Pandas | Medium | T2 |
| 75 | How do you use `Pydantic` to validate environment config? | 5–8 Yrs | Pydantic | High | T1 |
| 76 | How do you deserialize an LLM's JSON output into a Pydantic model? | 8–12 Yrs | GenAI / Pydantic | High | T1 |
| 77 | How do you write a reusable base class for all your API response models? | 8–12 Yrs | OOP / API | Medium | T2 |
| 78 | How do you implement `__repr__` that is copy-pastable to reproduce the object? | 5–8 Yrs | OOP | Medium | T2 |
| 79 | How do you implement a Pythonic configuration object with dot-notation access? | 8–12 Yrs | Config | Medium | T2 |
| 80 | How do you use `assert` and when should you NOT use it in production? | 5–8 Yrs | Testing / Debugging | Medium | T2 |
| 81 | How do you implement `__contains__` for a custom collection? | 5–8 Yrs | OOP | Medium | T2 |
| 82 | How do you use Python's `operator` module instead of lambdas? | 5–8 Yrs | Functional | Medium | T2 |
| 83 | How do you implement a generator-based pipeline for ETL? | 8–12 Yrs | Data Engineering | High | T1 |
| 84 | How do you use `contextlib.suppress` cleanly? | 5–8 Yrs | Exceptions | Medium | T2 |
| 85 | How do you implement a Python function that accepts either a single item or a list? | 5–8 Yrs | Flexibility | Medium | T2 |
| 86 | How do you build a dict from a list using dict comprehension with a computed key? | 3–5 Yrs | Comprehension | High | T1 |
| 87 | How do you write Pythonic error messages in exceptions? | 5–8 Yrs | Exceptions | Medium | T2 |
| 88 | How do you implement `__len__`, `__getitem__` for a custom container? | 8–12 Yrs | OOP | Medium | T2 |
| 89 | How do you use `dataclasses.field(repr=False)` to hide sensitive fields? | 5–8 Yrs | Dataclasses | Medium | T2 |
| 90 | How do you write a Pythonic function that returns a default list without mutation? | 3–5 Yrs | Functions | High | T1 |
| 91 | How do you chain `filter()` and `map()` vs using a single comprehension? | 5–8 Yrs | Functional | Medium | T2 |
| 92 | How do you use `zip_longest` to merge incomplete records? | 5–8 Yrs | itertools | Medium | T2 |
| 93 | How do you use Python's `textwrap` module for prompt engineering? | 8–12 Yrs | GenAI / Strings | Medium | T2 |
| 94 | How do you write a Pythonic multi-line condition? | 3–5 Yrs | Readability | High | T1 |
| 95 | How do you use `@functools.total_ordering` to implement comparison methods? | 8–12 Yrs | OOP | Medium | T2 |
| 96 | How do you use `__slots__` with `@dataclass` in Python 3.10+? | 8–12 Yrs | Dataclasses | Medium | T2 |
| 97 | How do you implement a "batch" function that splits a list into chunks of N? | 5–8 Yrs | Data Processing | High | T1 |
| 98 | How do you implement conditional dict construction cleanly? | 5–8 Yrs | Dict | Medium | T2 |
| 99 | How do you use `__format__` and f-string format specs for custom classes? | 8–12 Yrs | OOP | Low | T3 |
| 100 | How do you write a Pythonic function that lazy-evaluates and caches on first access? | 8–12 Yrs | Caching | High | T1 |
