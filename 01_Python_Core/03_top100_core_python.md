# Top 100 Core Python Interview Questions
> Focus: 3–12 Years | Non-FAANG | All Company Types
> Sources: Glassdoor, Reddit, GeeksforGeeks, LeetCode Discuss, LinkedIn

---

## SECTION 1: Variables, Types, and Operators (Q1–Q15)

### Q1
- **Question:** What are Python's built-in data types? Group them by mutable and immutable.
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Immutable: int, float, complex, bool, str, tuple, frozenset, bytes
  - Mutable: list, dict, set, bytearray
- **Follow-Ups:** Why is knowing mutability important when passing objects to functions?
- **Difficulty:** Easy

### Q2
- **Question:** What is the difference between `==` and `is`? Give an example where they behave differently.
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `==` compares values; `is` compares identity (memory address); integer cache (-5 to 256) makes `is` work unexpectedly for small ints
- **Difficulty:** Easy

### Q3
- **Question:** What is `None` in Python? How is it different from `False`, `0`, and `""`?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `None` is a singleton object of `NoneType`; all are falsy but not equal; always check `is None`, not `== None`
- **Difficulty:** Easy

### Q4
- **Question:** Explain Python's dynamic typing. What does "everything is an object" mean?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Variables are labels pointing to objects; `type(x)` at runtime; even functions and classes are objects; `int`, `str` are classes
- **Difficulty:** Easy

### Q5
- **Question:** What is the difference between `//`, `/`, `%`, and `**` operators?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `//` floor division; `/` true division (always float); `%` modulo; `**` exponentiation; `-7 // 2 = -4` (floor, not truncate)
- **Difficulty:** Easy

### Q6
- **Question:** What is short-circuit evaluation? How do `and` and `or` use it?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `and` returns first falsy or last value; `or` returns first truthy or last value; used for default values: `name = user.name or "Anonymous"`
- **Difficulty:** Easy

### Q7
- **Question:** What are Python's truthy and falsy values? Give examples.
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Falsy: `None, 0, 0.0, "", [], {}, set(), False`; everything else truthy; custom objects can define `__bool__` or `__len__`
- **Difficulty:** Easy

### Q8
- **Question:** What is the difference between `type()` and `isinstance()`? Which should you use?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `type(x) is int` checks exact type; `isinstance(x, int)` checks inheritance too; prefer `isinstance()` for polymorphism
- **Difficulty:** Easy

### Q9
- **Question:** How does Python handle integer overflow? What is `sys.maxsize`?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Python ints are arbitrary precision (no overflow); `sys.maxsize` is max value of C `ssize_t` (platform-dependent, ~9.2e18 on 64-bit)
- **Difficulty:** Easy

### Q10
- **Question:** What is a Python `bytes` object vs `str`? How do you encode/decode?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `str` is unicode text; `bytes` is raw binary data; `"hello".encode("utf-8")` → bytes; `b"hello".decode("utf-8")` → str
- **Difficulty:** Easy

### Q11
- **Question:** What are Python's comparison operators? What does chaining comparisons mean?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `1 < x < 10` evaluates as `1 < x and x < 10` — Pythonic range check; Java/C don't support this
- **Difficulty:** Easy

### Q12
- **Question:** What is the difference between `del`, `remove()`, and `pop()` for lists?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `del lst[i]` removes by index; `lst.remove(val)` removes first matching value; `lst.pop(i)` removes and returns element at index
- **Difficulty:** Easy

### Q13
- **Question:** How do Python's assignment operators work? What is augmented assignment?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Low | **Tier:** T3
- **Answer Points:** `+=`, `-=`, `*=` etc.; for immutable types creates new object; for mutable types may modify in place
- **Difficulty:** Easy

### Q14
- **Question:** What is `f-string` formatting? What can you put inside the curly braces?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Any valid Python expression; format specs: `f"{x:.2f}"`, `f"{name!r}"`, `f"{obj.attr}"`, `f"{func()}"`, multiline f-strings
- **Difficulty:** Easy

### Q15
- **Question:** How do you convert between Python types? When does conversion fail?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `int("123")`, `str(123)`, `list({1,2})`, `float("3.14")`; fails on invalid strings; `int("3.14")` raises ValueError — need `int(float("3.14"))`
- **Difficulty:** Easy

---

## SECTION 2: Strings (Q16–Q25)

### Q16
- **Question:** What are common string methods you use in data processing? (`strip`, `split`, `replace`, `find`, `startswith`, `endswith`)
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Practical use of each; `split(None)` splits on any whitespace; `strip()` vs `lstrip()` vs `rstrip()`; strings are immutable so methods return new strings
- **Difficulty:** Easy

### Q17
- **Question:** How do you check if a substring exists in a string? What is the fastest way?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `if "sub" in string` — most Pythonic and fast; `string.find()` returns index (-1 if not found); `string.index()` raises ValueError if not found
- **Difficulty:** Easy

### Q18
- **Question:** How do you reverse a string in Python? How do you check if a string is a palindrome?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `s[::-1]`; palindrome: `s == s[::-1]`; or `s.lower() == s.lower()[::-1]` for case-insensitive
- **Difficulty:** Easy

### Q19
- **Question:** How do you split a string into words and rejoin with a different separator?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `" ".join(s.split())`; normalizes whitespace; `",".join(words)` to rejoin with comma
- **Difficulty:** Easy

### Q20
- **Question:** How do you count occurrences of a substring? How do you replace all occurrences?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `s.count("x")`; `s.replace("old", "new")`; for complex patterns use `re.sub()`
- **Difficulty:** Easy

### Q21
- **Question:** How do you format numbers in Python strings? (decimal places, thousands separator, percentage)
- **Level:** 3–5 Years | **Company:** Healthcare, Finance | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `f"{3.14159:.2f}"` → "3.14"; `f"{1000000:,}"` → "1,000,000"; `f"{0.853:.1%}"` → "85.3%"
- **Difficulty:** Easy

### Q22
- **Question:** How do you handle multi-line strings in Python? What is a heredoc equivalent?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Triple quotes `"""..."""`; `textwrap.dedent()` to remove indentation; useful for SQL queries, prompts, templates
- **Difficulty:** Easy

### Q23
- **Question:** What is `re` module in Python? Give examples of `re.match`, `re.search`, `re.findall`.
- **Level:** 5–8 Years | **Company:** Data, Healthcare | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `match` anchors to start; `search` anywhere in string; `findall` returns all matches as list; use raw strings `r"pattern"`; compile for reuse
- **Difficulty:** Medium

### Q24
- **Question:** How do you extract structured data from a string using regex groups?
- **Level:** 5–8 Years | **Company:** Data, Healthcare | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Named groups: `(?P<name>...)` then `m.group("name")`; non-capturing groups `(?:...)`; `re.compile()` for performance
- **Difficulty:** Medium

### Q25
- **Question:** What is string encoding? Why does Python 3 separate `str` and `bytes`?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Python 2 mixed bytes/str causing bugs; Python 3 strict separation; UTF-8 default; `chardet` for detecting encoding of unknown files
- **Difficulty:** Easy

---

## SECTION 3: Lists, Tuples, Sets, Dicts (Q26–Q50)

### Q26
- **Question:** What is the time complexity of `list.append()`, `list.insert(0, x)`, and `list.pop()`?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `append`: O(1) amortized; `insert(0, x)`: O(n) shifts all elements; `pop()`: O(1); `pop(0)`: O(n) — use `deque` for O(1) from both ends
- **Difficulty:** Medium

### Q27
- **Question:** How does Python's list implement dynamic resizing? What is over-allocation?
- **Level:** 8–12 Yrs | **Company:** Senior roles | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** List allocates more space than needed to amortize append cost; growth factor ~1.125; `sys.getsizeof([])` vs `sys.getsizeof([1]*100)`
- **Difficulty:** Medium

### Q28
- **Question:** What is the difference between `list.copy()` and slicing `lst[:]`?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Both create shallow copies; `list.copy()` is more readable; neither deep-copies nested objects
- **Difficulty:** Easy

### Q29
- **Question:** How do sets work in Python? What is the time complexity of membership testing?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Hash table implementation; O(1) average membership test; unordered; elements must be hashable; `frozenset` is hashable (usable as dict key)
- **Difficulty:** Easy

### Q30
- **Question:** How do you find the intersection, union, and difference of two sets?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `a & b` or `a.intersection(b)`; `a | b` or `a.union(b)`; `a - b` or `a.difference(b)`; `a ^ b` symmetric difference
- **Difficulty:** Easy

### Q31
- **Question:** What is a `frozenset`? When would you use it over a regular set?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Immutable, hashable version of set; can be used as dict key or set element; useful for caching sets
- **Difficulty:** Easy

### Q32
- **Question:** How does Python dict handle key collisions? What is the load factor?
- **Level:** 8–12 Years | **Company:** Senior | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Open addressing with probing; load factor ~2/3 triggers resize; hash + equality check; compact dict implementation in 3.6+
- **Difficulty:** Hard

### Q33
- **Question:** What is `dict.items()`, `dict.keys()`, `dict.values()`? Are they views or copies?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** They are live views, not copies; reflect changes to underlying dict; convert to list if you need to modify dict while iterating
- **Difficulty:** Easy

### Q34
- **Question:** How do you iterate over a dict and modify it? What is the correct approach?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Never modify a dict while iterating (RuntimeError); iterate over `list(d.keys())` or use dict comprehension to create new dict
- **Difficulty:** Easy

### Q35
- **Question:** How does `collections.Counter` implement the `most_common()` method?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Uses `heapq.nlargest()` for `k < count/2` else sort; Counter subclasses dict; arithmetic operations (+, -, &, |) supported
- **Difficulty:** Medium

### Q36
- **Question:** What is `collections.deque`? How does it differ from a list for queue operations?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Doubly-linked list; O(1) `appendleft`/`popleft`; list is O(n) for front operations; `maxlen` for fixed-size buffer
- **Difficulty:** Easy

### Q37
- **Question:** How do you implement a stack and queue using Python built-ins?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Stack: `list` with `append`/`pop`; Queue: `collections.deque` with `append`/`popleft` or `queue.Queue` for thread-safety
- **Difficulty:** Easy

### Q38
- **Question:** What is `heapq` in Python? How do you implement a min-heap and max-heap?
- **Level:** 5–8 Years | **Company:** Data | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** `heapq` provides min-heap; max-heap: negate values; `heappush`, `heappop`, `heapify`, `nlargest`, `nsmallest`
- **Difficulty:** Medium

### Q39
- **Question:** How do you use `zip()` to create a dict from two lists? Handle unequal lengths?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `dict(zip(keys, values))`; `zip` stops at shortest; `zip_longest` for equal-length with fill value
- **Difficulty:** Easy

### Q40
- **Question:** What is a `NamedTuple`? How is it different from a regular tuple and a dataclass?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Access by name OR index; immutable; memory-efficient (no `__dict__`); `typing.NamedTuple` for type hints; less flexible than dataclass for defaults/validation
- **Difficulty:** Easy

---

## SECTION 4: Functions and Scope (Q41–Q60)

### Q41
- **Question:** Explain Python's LEGB scope rule.
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Local → Enclosing → Global → Built-in; lookup order for variable names; `global` keyword to write to global; `nonlocal` to write to enclosing scope
- **Difficulty:** Easy

### Q42
- **Question:** What is a closure? Write an example of a counter using a closure.
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Function that captures variables from enclosing scope; `__closure__` attribute; `nonlocal` to mutate captured variable
- **Difficulty:** Medium

### Q43
- **Question:** What is the late binding problem in closures? How do you fix it?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Loop variable captured by reference, not value; all closures see final loop value; fix: `lambda i=i: i` default arg captures current value
- **Difficulty:** Medium

### Q44
- **Question:** What is `functools.lru_cache`? How does it work? What are its limitations?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Memoizes function results by argument hash; `maxsize=None` is `@cache` (unbounded); arguments must be hashable; not thread-safe for async; no TTL
- **Difficulty:** Medium

### Q45
- **Question:** What is `functools.partial`? Give a real use case.
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Pre-fill some function arguments; cleaner than lambda; `sorted(data, key=partial(get_field, field="age"))`; currying in Python
- **Difficulty:** Easy

### Q46
- **Question:** How do `*args` and `**kwargs` work in function signatures? What is keyword-only argument?
- **Level:** 3–5 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** `*` separator forces keyword-only; `def f(a, b, *, c)` → `c` must be keyword arg; positional-only with `/` (Python 3.8+)
- **Difficulty:** Easy

### Q47
- **Question:** What is a higher-order function? Give three Python examples.
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Function that takes or returns functions; `map()`, `filter()`, `sorted(key=...)`, decorators, `functools.partial`
- **Difficulty:** Easy

### Q48
- **Question:** What does `functools.wraps` do? Why is it essential in decorators?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:** Copies `__name__`, `__doc__`, `__module__`, `__qualname__`, `__annotations__`, `__dict__`, `__wrapped__` from wrapped to wrapper; without it, all decorated functions appear as "wrapper"
- **Difficulty:** Easy

### Q49
- **Question:** How do you write a decorator that accepts arguments (parameterized decorator)?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  ```python
  def retry(times=3):
      def decorator(func):
          @functools.wraps(func)
          def wrapper(*args, **kwargs):
              for _ in range(times):
                  try: return func(*args, **kwargs)
                  except Exception: pass
          return wrapper
      return decorator
  ```
- **Difficulty:** Medium

### Q50
- **Question:** What is recursion? When do you hit the recursion limit? How do you avoid it?
- **Level:** 5–8 Years | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer Points:** Default limit: `sys.getrecursionlimit()` = 1000; increase with `sys.setrecursionlimit()`; better: convert to iterative with explicit stack
- **Difficulty:** Easy

---

## SECTION 5: OOP (Q51–Q70)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 51 | What are the 4 pillars of OOP? Give Python examples. | 3–5 Yrs | High | T1 |
| 52 | What is the difference between class and instance variables? | 3–5 Yrs | High | T1 |
| 53 | How does inheritance work in Python? Write an example with method overriding. | 3–5 Yrs | High | T1 |
| 54 | What is `super()`? How does it work with multiple inheritance? | 5–8 Yrs | High | T1 |
| 55 | What is encapsulation in Python? How does name mangling work (`__private`)? | 5–8 Yrs | High | T1 |
| 56 | What is polymorphism? How does Python achieve it without interfaces? | 5–8 Yrs | High | T1 |
| 57 | What is an abstract class? How do you define one in Python? | 5–8 Yrs | High | T1 |
| 58 | What is composition vs inheritance? When do you prefer composition? | 8–12 Yrs | High | T1 |
| 59 | How do you implement the strategy pattern using duck typing? | 8–12 Yrs | Medium | T2 |
| 60 | What is `__repr__` vs `__str__`? How do f-strings choose between them? | 5–8 Yrs | High | T1 |
| 61 | How do you implement `__eq__` correctly? What is the contract with `__hash__`? | 5–8 Yrs | High | T1 |
| 62 | What is `@staticmethod` vs module function? When would you choose staticmethod? | 5–8 Yrs | Medium | T2 |
| 63 | How do you implement operator overloading? Give an example with `+` and `*`. | 5–8 Yrs | Medium | T2 |
| 64 | What is `__del__`? Why is it unreliable and when should you avoid it? | 8–12 Yrs | Medium | T2 |
| 65 | How do you implement a mixin in Python? What is the ordering rule for mixins? | 8–12 Yrs | High | T1 |
| 66 | What is `Protocol` in Python typing? How does it enable structural subtyping? | 8–12 Yrs | Medium | T2 |
| 67 | How does `dataclass(frozen=True)` compare to `NamedTuple` for immutable records? | 5–8 Yrs | Medium | T2 |
| 68 | What is `__post_init__` in dataclasses? Write a validation example. | 5–8 Yrs | High | T1 |
| 69 | What is `ABCMeta.__subclasshook__`? How does `isinstance()` work with ABCs? | 8–12 Yrs | Low | T3 |
| 70 | How do you implement a generic class using `TypeVar` and `Generic`? | 8–12 Yrs | Medium | T2 |

---

## SECTION 6: Exceptions, File Handling, Modules (Q71–Q100)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 71 | How do you define a custom exception hierarchy? Best practices? | 5–8 Yrs | High | T1 |
| 72 | What is `try/except/else/finally`? When does `else` run? | 3–5 Yrs | High | T1 |
| 73 | How do you raise an exception with context (`raise X from Y`)? | 5–8 Yrs | Medium | T2 |
| 74 | How do you log an exception with full traceback? | 5–8 Yrs | High | T1 |
| 75 | What is `contextlib.suppress`? Give a use case. | 5–8 Yrs | Medium | T2 |
| 76 | How do you re-raise an exception after logging it? | 5–8 Yrs | High | T1 |
| 77 | What is `sys.exc_info()`? How does it work? | 8–12 Yrs | Medium | T2 |
| 78 | How do you read a large file line by line without loading it all into memory? | 3–5 Yrs | High | T1 |
| 79 | How do you write to a file safely, ensuring it's closed even on error? | 3–5 Yrs | High | T1 |
| 80 | How do you use `json.load` vs `json.loads`? And `json.dump` vs `json.dumps`? | 3–5 Yrs | High | T1 |
| 81 | What is `pickle`? Why is it dangerous to load untrusted pickle data? | 5–8 Yrs | Medium | T2 |
| 82 | How do you parse CSV with Python? `csv` module vs Pandas for different scenarios? | 3–5 Yrs | High | T1 |
| 83 | How do you work with temporary files in Python? (`tempfile` module) | 5–8 Yrs | Medium | T2 |
| 84 | How do you list files in a directory with filtering using `pathlib`? | 3–5 Yrs | High | T1 |
| 85 | What is `__init__.py`? What is the difference between a module and a package? | 3–5 Yrs | High | T1 |
| 86 | How do Python imports work? What is the `sys.path`? | 5–8 Yrs | High | T1 |
| 87 | What is `if __name__ == "__main__"`? Why do you use it? | 3–5 Yrs | High | T1 |
| 88 | How do you handle relative vs absolute imports? | 5–8 Yrs | Medium | T2 |
| 89 | How do you lazy-import a module to improve startup time? | 8–12 Yrs | Medium | T2 |
| 90 | What is `logging.getLogger(__name__)` and why is `__name__` used as logger name? | 5–8 Yrs | High | T1 |
| 91 | How do you configure Python logging with a config file or dict? | 8–12 Yrs | Medium | T2 |
| 92 | What is a log handler? Give examples of handlers used in production. | 5–8 Yrs | High | T1 |
| 93 | How do you avoid logging sensitive data (PII/PHI) in Python? | 8–12 Yrs | High | T1 |
| 94 | How do you use `logging.Filter` to add contextual data to all log records? | 8–12 Yrs | Medium | T2 |
| 95 | What is `__all__` in Python? How does it control `from module import *`? | 5–8 Yrs | Medium | T2 |
| 96 | How do you use `importlib.import_module()` for dynamic imports? | 8–12 Yrs | Medium | T2 |
| 97 | What is a Python namespace package (PEP 420)? | 8–12 Yrs | Low | T3 |
| 98 | How do you serialize Python objects to JSON that include datetime, Enum, Decimal? | 5–8 Yrs | High | T1 |
| 99 | How do you use `os.environ`, `dotenv`, and `Pydantic Settings` for config? | 5–8 Yrs | High | T1 |
| 100 | What is `__name__`, `__file__`, `__package__`, `__spec__`? | 5–8 Yrs | Medium | T2 |
