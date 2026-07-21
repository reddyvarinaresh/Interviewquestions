# Top 150 Python Coding Questions — Actually Asked in Company Interviews
> Pure coding problems. No theory. Write the function. These are the ACTUAL coding rounds.
> Companies: TCS, Wipro, Infosys, Accenture, Deloitte, Optum, Carelon, EPAM, Cognizant, Capgemini, HCL

---

## SECTION 1 — Strings (Most Asked) 

### Q1. Reverse a string
```python
def reverse_string(s: str) -> str:
    return s[::-1]

# "hello" → "olleh"
```

---

### Q2. Check if a string is a palindrome
```python
def is_palindrome(s: str) -> bool:
    s = s.lower().replace(" ", "")
    return s == s[::-1]

# "racecar" → True, "hello" → False
```

---

### Q3. Count vowels and consonants in a string
```python
def count_vowels_consonants(s: str) -> dict:
    vowels = set("aeiouAEIOU")
    v = sum(1 for c in s if c in vowels)
    c = sum(1 for c in s if c.isalpha() and c not in vowels)
    return {"vowels": v, "consonants": c}

# "Hello World" → {"vowels": 3, "consonants": 7}
```

---

### Q4. Find the first non-repeating character
```python
from collections import Counter

def first_non_repeating(s: str) -> str:
    count = Counter(s)
    for ch in s:
        if count[ch] == 1:
            return ch
    return ""

# "aabbcde" → "c"
```

---

### Q5. Check if two strings are anagrams
```python
from collections import Counter

def are_anagrams(s1: str, s2: str) -> bool:
    return Counter(s1.lower()) == Counter(s2.lower())

# "listen", "silent" → True
```

---

### Q6. Remove duplicate characters from a string
```python
def remove_duplicates(s: str) -> str:
    seen = set()
    return "".join(c for c in s if not (c in seen or seen.add(c)))

# "programming" → "progamin"
```

---

### Q7. Count occurrences of each character
```python
from collections import Counter

def char_count(s: str) -> dict:
    return dict(Counter(s))

# "hello" → {'h':1, 'e':1, 'l':2, 'o':1}
```

---

### Q8. Reverse words in a sentence
```python
def reverse_words(s: str) -> str:
    return " ".join(s.split()[::-1])

# "Hello World Python" → "Python World Hello"
```

---

### Q9. Check if a string is a rotation of another
```python
def is_rotation(s1: str, s2: str) -> bool:
    if len(s1) != len(s2):
        return False
    return s2 in (s1 + s1)

# "abcde", "cdeab" → True
```

---

### Q10. Find all permutations of a string
```python
from itertools import permutations

def get_permutations(s: str) -> list:
    return ["".join(p) for p in permutations(s)]

# "abc" → ["abc","acb","bac","bca","cab","cba"]
```

---

### Q11. Find the longest common prefix
```python
def longest_common_prefix(words: list[str]) -> str:
    if not words:
        return ""
    prefix = words[0]
    for word in words[1:]:
        while not word.startswith(prefix):
            prefix = prefix[:-1]
        if not prefix:
            return ""
    return prefix

# ["flower","flow","flight"] → "fl"
```

---

### Q12. Compress a string (run-length encoding)
```python
def compress(s: str) -> str:
    if not s:
        return ""
    result, count = [], 1
    for i in range(1, len(s)):
        if s[i] == s[i-1]:
            count += 1
        else:
            result.append(s[i-1] + (str(count) if count > 1 else ""))
            count = 1
    result.append(s[-1] + (str(count) if count > 1 else ""))
    return "".join(result)

# "aaabbbccddddee" → "a3b3c2d4e2"
```

---

### Q13. Find all substrings of a string
```python
def all_substrings(s: str) -> list:
    return [s[i:j] for i in range(len(s)) for j in range(i+1, len(s)+1)]

# "abc" → ["a","ab","abc","b","bc","c"]
```

---

### Q14. Count words in a sentence
```python
def word_count(sentence: str) -> dict:
    from collections import Counter
    return dict(Counter(sentence.lower().split()))

# "the cat sat on the mat" → {'the':2,'cat':1,'sat':1,'on':1,'mat':1}
```

---

### Q15. Check if string has balanced brackets
```python
def is_balanced(s: str) -> bool:
    stack = []
    pairs = {")": "(", "]": "[", "}": "{"}
    for ch in s:
        if ch in "([{":
            stack.append(ch)
        elif ch in ")]}":
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()
    return len(stack) == 0

# "({[]})" → True, "([)]" → False
```

---

## SECTION 2 — Numbers & Math

### Q16. Check if a number is prime
```python
def is_prime(n: int) -> bool:
    if n < 2:
        return False
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            return False
    return True

# 7 → True, 9 → False
```

---

### Q17. Fibonacci sequence (iterative)
```python
def fibonacci(n: int) -> list:
    if n <= 0: return []
    if n == 1: return [0]
    seq = [0, 1]
    for _ in range(2, n):
        seq.append(seq[-1] + seq[-2])
    return seq

# fibonacci(7) → [0,1,1,2,3,5,8]
```

---

### Q18. Fibonacci — nth term without sequence
```python
def fib(n: int) -> int:
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
    return a

# fib(7) → 13
```

---

### Q19. Factorial (iterative and recursive)
```python
def factorial_iter(n: int) -> int:
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

def factorial_rec(n: int) -> int:
    return 1 if n <= 1 else n * factorial_rec(n - 1)

# factorial(5) → 120
```

---

### Q20. Find all prime numbers up to N (Sieve of Eratosthenes)
```python
def sieve(n: int) -> list:
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n+1, i):
                is_prime[j] = False
    return [i for i, v in enumerate(is_prime) if v]

# sieve(20) → [2,3,5,7,11,13,17,19]
```

---

### Q21. Check Armstrong number
```python
def is_armstrong(n: int) -> bool:
    digits = str(n)
    power = len(digits)
    return sum(int(d) ** power for d in digits) == n

# 153 → True (1³+5³+3³=153), 123 → False
```

---

### Q22. Reverse digits of a number
```python
def reverse_number(n: int) -> int:
    sign = -1 if n < 0 else 1
    return sign * int(str(abs(n))[::-1])

# 12345 → 54321, -678 → -876
```

---

### Q23. Find GCD and LCM
```python
import math

def gcd(a: int, b: int) -> int:
    return math.gcd(a, b)

def lcm(a: int, b: int) -> int:
    return abs(a * b) // math.gcd(a, b)

# gcd(12,8)→4, lcm(4,6)→12
```

---

### Q24. Count digits, sum of digits
```python
def digit_stats(n: int) -> dict:
    digits = [int(d) for d in str(abs(n))]
    return {"count": len(digits), "sum": sum(digits), "product": __import__("math").prod(digits)}

# 1234 → {"count":4,"sum":10,"product":24}
```

---

### Q25. Power without using ** operator
```python
def power(base: int, exp: int) -> int:
    if exp == 0: return 1
    if exp < 0: return 1 / power(base, -exp)
    result = 1
    while exp > 0:
        if exp % 2 == 1:
            result *= base
        base *= base
        exp //= 2
    return result

# power(2, 10) → 1024
```

---

### Q26. Check if number is perfect
```python
def is_perfect(n: int) -> bool:
    if n < 2: return False
    return sum(i for i in range(1, n) if n % i == 0) == n

# 6 → True (1+2+3=6), 28 → True
```

---

### Q27. Binary to decimal and decimal to binary
```python
def decimal_to_binary(n: int) -> str:
    return bin(n)[2:]

def binary_to_decimal(b: str) -> int:
    return int(b, 2)

# decimal_to_binary(10) → "1010"
# binary_to_decimal("1010") → 10
```

---

### Q28. Sum of digits until single digit (digital root)
```python
def digital_root(n: int) -> int:
    while n >= 10:
        n = sum(int(d) for d in str(n))
    return n

# 9875 → 9+8+7+5=29 → 2+9=11 → 1+1=2
```

---

## SECTION 3 — Lists & Arrays

### Q29. Find second largest element
```python
def second_largest(lst: list) -> int:
    unique = list(set(lst))
    if len(unique) < 2:
        return None
    unique.sort()
    return unique[-2]

# [3,1,4,1,5,9,2,6] → 6
```

---

### Q30. Remove duplicates from a list preserving order
```python
def remove_duplicates(lst: list) -> list:
    seen = set()
    return [x for x in lst if not (x in seen or seen.add(x))]

# [1,2,2,3,4,4,5] → [1,2,3,4,5]
```

---

### Q31. Rotate a list by k positions
```python
def rotate(lst: list, k: int) -> list:
    if not lst: return lst
    k = k % len(lst)
    return lst[k:] + lst[:k]

# rotate([1,2,3,4,5], 2) → [3,4,5,1,2]
```

---

### Q32. Find missing number in 1 to N
```python
def find_missing(lst: list) -> int:
    n = len(lst) + 1
    return n * (n + 1) // 2 - sum(lst)

# [1,2,4,5] → 3
```

---

### Q33. Two Sum — find pair that adds to target
```python
def two_sum(nums: list, target: int) -> tuple:
    seen = {}
    for i, n in enumerate(nums):
        complement = target - n
        if complement in seen:
            return (seen[complement], i)
        seen[n] = i
    return ()

# two_sum([2,7,11,15], 9) → (0, 1)
```

---

### Q34. Move all zeros to end
```python
def move_zeros(lst: list) -> list:
    non_zero = [x for x in lst if x != 0]
    return non_zero + [0] * (len(lst) - len(non_zero))

# [0,1,0,3,12] → [1,3,12,0,0]
```

---

### Q35. Find duplicates in a list
```python
from collections import Counter

def find_duplicates(lst: list) -> list:
    return [item for item, count in Counter(lst).items() if count > 1]

# [1,2,2,3,4,4,5] → [2,4]
```

---

### Q36. Flatten a nested list (one level)
```python
def flatten_one(nested: list) -> list:
    return [item for sublist in nested for item in sublist]

# [[1,2],[3,4],[5]] → [1,2,3,4,5]
```

---

### Q37. Flatten deeply nested list
```python
def flatten(nested):
    for item in nested:
        if isinstance(item, list):
            yield from flatten(item)
        else:
            yield item

# list(flatten([1,[2,[3,[4]]],5])) → [1,2,3,4,5]
```

---

### Q38. Find intersection of two lists
```python
def intersection(a: list, b: list) -> list:
    return list(set(a) & set(b))

# [1,2,3,4], [3,4,5,6] → [3,4]
```

---

### Q39. Chunk a list into batches of size n
```python
def chunk(lst: list, n: int) -> list:
    return [lst[i:i+n] for i in range(0, len(lst), n)]

# chunk([1,2,3,4,5,6,7], 3) → [[1,2,3],[4,5,6],[7]]
```

---

### Q40. Sort a list of dicts by a key
```python
def sort_by_key(lst: list[dict], key: str, reverse: bool = False) -> list:
    return sorted(lst, key=lambda x: x[key], reverse=reverse)

employees = [{"name":"Alice","salary":90000},{"name":"Bob","salary":75000}]
sort_by_key(employees, "salary", reverse=True)
# → [{"name":"Alice",...},{"name":"Bob",...}]
```

---

### Q41. Find max product of two numbers in a list
```python
def max_product(lst: list) -> int:
    lst.sort()
    return max(lst[0]*lst[1], lst[-1]*lst[-2])

# [-10,-3,1,4,5] → 30 (from -10 * -3)
```

---

### Q42. Merge two sorted lists
```python
def merge_sorted(a: list, b: list) -> list:
    result, i, j = [], 0, 0
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            result.append(a[i]); i += 1
        else:
            result.append(b[j]); j += 1
    return result + a[i:] + b[j:]

# [1,3,5],[2,4,6] → [1,2,3,4,5,6]
```

---

### Q43. Count frequency of each element
```python
from collections import Counter

def frequency(lst: list) -> dict:
    return dict(Counter(lst))

# [1,2,2,3,3,3] → {1:1, 2:2, 3:3}
```

---

### Q44. Find leaders in an array (greater than all elements to the right)
```python
def find_leaders(lst: list) -> list:
    leaders = [lst[-1]]
    max_right = lst[-1]
    for i in range(len(lst)-2, -1, -1):
        if lst[i] > max_right:
            leaders.append(lst[i])
            max_right = lst[i]
    return leaders[::-1]

# [16,17,4,3,5,2] → [17,5,2]
```

---

### Q45. Subarray with maximum sum (Kadane's Algorithm)
```python
def max_subarray_sum(lst: list) -> int:
    max_sum = current = lst[0]
    for n in lst[1:]:
        current = max(n, current + n)
        max_sum = max(max_sum, current)
    return max_sum

# [-2,1,-3,4,-1,2,1,-5,4] → 6  (subarray [4,-1,2,1])
```

---

## SECTION 4 — Dictionaries

### Q46. Word frequency counter
```python
def word_frequency(text: str) -> dict:
    from collections import Counter
    return dict(Counter(text.lower().split()))

# "the cat sat on the mat" → {'the':2,'cat':1,'sat':1,'on':1,'mat':1}
```

---

### Q47. Invert a dictionary
```python
def invert_dict(d: dict) -> dict:
    return {v: k for k, v in d.items()}

# {"a":1,"b":2} → {1:"a",2:"b"}
```

---

### Q48. Merge two dicts (Python 3.9+)
```python
def merge_dicts(d1: dict, d2: dict) -> dict:
    return d1 | d2  # d2 values win on conflict

# also: {**d1, **d2}
# {"a":1,"b":2} | {"b":3,"c":4} → {"a":1,"b":3,"c":4}
```

---

### Q49. Group a list of dicts by a key
```python
from collections import defaultdict

def group_by(lst: list[dict], key: str) -> dict:
    result = defaultdict(list)
    for item in lst:
        result[item[key]].append(item)
    return dict(result)

orders = [{"dept":"IT","emp":"Alice"},{"dept":"HR","emp":"Bob"},{"dept":"IT","emp":"Carol"}]
group_by(orders, "dept")
# → {"IT":[Alice,Carol], "HR":[Bob]}
```

---

### Q50. Find common keys between two dicts
```python
def common_keys(d1: dict, d2: dict) -> set:
    return d1.keys() & d2.keys()

# {"a":1,"b":2},{"b":3,"c":4} → {"b"}
```

---

### Q51. Sort a dictionary by value
```python
def sort_by_value(d: dict, reverse: bool = False) -> dict:
    return dict(sorted(d.items(), key=lambda x: x[1], reverse=reverse))

# {"b":3,"a":1,"c":2} → {"a":1,"c":2,"b":3}
```

---

### Q52. Find max/min key and value in a dict
```python
def dict_extremes(d: dict) -> dict:
    return {
        "max_key": max(d, key=d.get),
        "min_key": min(d, key=d.get),
        "max_val": max(d.values()),
        "min_val": min(d.values()),
    }
```

---

### Q53. Flatten a nested dict
```python
def flatten_dict(d: dict, parent_key: str = "", sep: str = ".") -> dict:
    items = {}
    for k, v in d.items():
        new_key = f"{parent_key}{sep}{k}" if parent_key else k
        if isinstance(v, dict):
            items.update(flatten_dict(v, new_key, sep))
        else:
            items[new_key] = v
    return items

# {"a":{"b":{"c":1}}} → {"a.b.c":1}
```

---

### Q54. Count character frequency using dict (no Counter)
```python
def char_freq(s: str) -> dict:
    freq = {}
    for c in s:
        freq[c] = freq.get(c, 0) + 1
    return freq
```

---

### Q55. Find top N most common elements
```python
from collections import Counter

def top_n(lst: list, n: int) -> list:
    return Counter(lst).most_common(n)

# top_n([1,1,2,3,3,3,4], 2) → [(3,3),(1,2)]
```

---

## SECTION 5 — OOP (Practical Coding)

### Q56. Create a Stack class
```python
class Stack:
    def __init__(self):
        self._data = []

    def push(self, item):
        self._data.append(item)

    def pop(self):
        if self.is_empty():
            raise IndexError("Stack is empty")
        return self._data.pop()

    def peek(self):
        return self._data[-1] if self._data else None

    def is_empty(self) -> bool:
        return len(self._data) == 0

    def __len__(self):
        return len(self._data)
```

---

### Q57. Create a Queue class
```python
from collections import deque

class Queue:
    def __init__(self):
        self._data = deque()

    def enqueue(self, item):
        self._data.append(item)

    def dequeue(self):
        if self.is_empty():
            raise IndexError("Queue is empty")
        return self._data.popleft()

    def peek(self):
        return self._data[0] if self._data else None

    def is_empty(self) -> bool:
        return len(self._data) == 0

    def __len__(self):
        return len(self._data)
```

---

### Q58. Implement a Singleton class
```python
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

s1 = Singleton()
s2 = Singleton()
print(s1 is s2)  # True
```

---

### Q59. Implement a simple linked list
```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def append(self, data):
        node = Node(data)
        if not self.head:
            self.head = node
            return
        cur = self.head
        while cur.next:
            cur = cur.next
        cur.next = node

    def to_list(self) -> list:
        result, cur = [], self.head
        while cur:
            result.append(cur.data)
            cur = cur.next
        return result

    def reverse(self):
        prev, cur = None, self.head
        while cur:
            nxt = cur.next
            cur.next = prev
            prev = cur
            cur = nxt
        self.head = prev
```

---

### Q60. Implement a simple calculator class
```python
class Calculator:
    def __init__(self):
        self.history = []

    def calculate(self, a: float, op: str, b: float) -> float:
        ops = {"+": a+b, "-": a-b, "*": a*b, "/": a/b if b != 0 else None}
        if op not in ops:
            raise ValueError(f"Unknown operator: {op}")
        result = ops[op]
        self.history.append(f"{a} {op} {b} = {result}")
        return result

calc = Calculator()
calc.calculate(10, "+", 5)   # 15
calc.calculate(10, "/", 2)   # 5.0
```

---

### Q61. Implement `__str__`, `__repr__`, `__eq__` for a class
```python
class Employee:
    def __init__(self, name: str, salary: float):
        self.name = name
        self.salary = salary

    def __str__(self):
        return f"Employee({self.name}, ${self.salary:,.0f})"

    def __repr__(self):
        return f"Employee(name={self.name!r}, salary={self.salary!r})"

    def __eq__(self, other):
        return isinstance(other, Employee) and self.name == other.name

    def __lt__(self, other):
        return self.salary < other.salary
```

---

### Q62. Implement inheritance — Shape → Circle, Rectangle
```python
import math

class Shape:
    def area(self) -> float:
        raise NotImplementedError

    def perimeter(self) -> float:
        raise NotImplementedError

class Circle(Shape):
    def __init__(self, radius: float):
        self.radius = radius

    def area(self) -> float:
        return round(math.pi * self.radius ** 2, 2)

    def perimeter(self) -> float:
        return round(2 * math.pi * self.radius, 2)

class Rectangle(Shape):
    def __init__(self, w: float, h: float):
        self.w, self.h = w, h

    def area(self) -> float:
        return self.w * self.h

    def perimeter(self) -> float:
        return 2 * (self.w + self.h)
```

---

### Q63. Implement a Bank Account with deposit/withdraw
```python
class BankAccount:
    def __init__(self, owner: str, balance: float = 0):
        self.owner = owner
        self._balance = balance
        self._transactions = []

    def deposit(self, amount: float):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self._balance += amount
        self._transactions.append(("deposit", amount))

    def withdraw(self, amount: float):
        if amount > self._balance:
            raise ValueError("Insufficient funds")
        self._balance -= amount
        self._transactions.append(("withdraw", amount))

    @property
    def balance(self) -> float:
        return self._balance

    def statement(self) -> list:
        return self._transactions
```

---

### Q64. Implement a simple LRU Cache class
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key: int, value: int):
        self.cache[key] = value
        self.cache.move_to_end(key)
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

cache = LRUCache(2)
cache.put(1, 1)
cache.put(2, 2)
cache.get(1)    # 1 (moves 1 to end)
cache.put(3, 3) # evicts key 2
cache.get(2)    # -1 (not found)
```

---

### Q65. Implement iterator protocol (`__iter__`, `__next__`)
```python
class CountDown:
    def __init__(self, start: int):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current < 0:
            raise StopIteration
        val = self.current
        self.current -= 1
        return val

for n in CountDown(5):
    print(n)  # 5 4 3 2 1 0
```

---

## SECTION 6 — Decorators & Generators

### Q66. Write a timer decorator
```python
import time, functools

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {(time.perf_counter()-start)*1000:.2f}ms")
        return result
    return wrapper

@timer
def slow_fn():
    time.sleep(0.1)
    return "done"
```

---

### Q67. Write a memoize decorator (caching)
```python
import functools

def memoize(func):
    cache = {}
    @functools.wraps(func)
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper

@memoize
def fib(n):
    if n < 2: return n
    return fib(n-1) + fib(n-2)

# fib(50) is now instant
```

---

### Q68. Write a logger decorator
```python
import functools

def logger(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}({args}, {kwargs})")
        result = func(*args, **kwargs)
        print(f"{func.__name__} returned {result}")
        return result
    return wrapper

@logger
def add(a, b):
    return a + b
```

---

### Q69. Write a retry decorator
```python
import functools, time

def retry(times=3, delay=1):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(times):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == times - 1:
                        raise
                    time.sleep(delay)
        return wrapper
    return decorator

@retry(times=3, delay=2)
def unstable_api_call():
    import random
    if random.random() < 0.7:
        raise ConnectionError("API down")
    return "Success"
```

---

### Q70. Generator for infinite Fibonacci
```python
def fib_gen():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

gen = fib_gen()
first_10 = [next(gen) for _ in range(10)]
# [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

---

### Q71. Generator for reading large files line by line
```python
def read_large_file(filepath: str):
    with open(filepath, "r") as f:
        for line in f:
            yield line.strip()

for line in read_large_file("bigfile.txt"):
    process(line)  # memory-efficient
```

---

### Q72. Generator pipeline (chain generators)
```python
def read_lines(path):
    with open(path) as f:
        for line in f: yield line.strip()

def filter_empty(lines):
    for line in lines:
        if line: yield line

def to_uppercase(lines):
    for line in lines: yield line.upper()

# Chain:
pipeline = to_uppercase(filter_empty(read_lines("data.txt")))
for line in pipeline:
    print(line)
```

---

### Q73. Context manager using `__enter__` and `__exit__`
```python
class FileManager:
    def __init__(self, filename: str, mode: str = "r"):
        self.filename = filename
        self.mode = mode
        self.file = None

    def __enter__(self):
        self.file = open(self.filename, self.mode)
        return self.file

    def __exit__(self, exc_type, exc_val, exc_tb):
        if self.file:
            self.file.close()
        return False  # don't suppress exceptions

with FileManager("test.txt", "w") as f:
    f.write("Hello")
```

---

### Q74. Context manager using `@contextmanager`
```python
from contextlib import contextmanager
import time

@contextmanager
def timer_block(label: str):
    start = time.perf_counter()
    try:
        yield
    finally:
        elapsed = (time.perf_counter() - start) * 1000
        print(f"{label}: {elapsed:.2f}ms")

with timer_block("data processing"):
    result = [x**2 for x in range(1_000_000)]
```

---

### Q75. Write a decorator with arguments
```python
import functools

def repeat(n: int):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(n):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")  # prints 3 times
```

---

## SECTION 7 — File & Data Handling

### Q76. Read a CSV and compute column statistics
```python
import csv
from collections import defaultdict

def csv_stats(filepath: str, col: str) -> dict:
    values = []
    with open(filepath, newline="") as f:
        for row in csv.DictReader(f):
            try:
                values.append(float(row[col]))
            except (ValueError, KeyError):
                pass
    if not values:
        return {}
    return {
        "count": len(values),
        "sum": sum(values),
        "mean": sum(values) / len(values),
        "min": min(values),
        "max": max(values),
    }
```

---

### Q77. Write and read JSON data
```python
import json

def save_json(data: dict, filepath: str):
    with open(filepath, "w") as f:
        json.dump(data, f, indent=2)

def load_json(filepath: str) -> dict:
    with open(filepath) as f:
        return json.load(f)

config = {"model": "gpt-4o", "temperature": 0.7, "max_tokens": 1000}
save_json(config, "config.json")
loaded = load_json("config.json")
```

---

### Q78. Parse JSON from an API response string
```python
import json

def safe_parse_json(raw: str) -> dict | None:
    try:
        return json.loads(raw)
    except json.JSONDecodeError as e:
        print(f"Parse error: {e}")
        return None

# Also extract JSON embedded in text:
import re
def extract_json(text: str) -> dict | None:
    match = re.search(r"\{.*\}", text, re.DOTALL)
    if match:
        return safe_parse_json(match.group())
    return None
```

---

### Q79. Read environment variables safely
```python
import os

def get_env(key: str, default=None, required: bool = False):
    value = os.environ.get(key, default)
    if required and value is None:
        raise EnvironmentError(f"Required env var '{key}' is not set")
    return value

DB_URL  = get_env("DATABASE_URL", required=True)
LOG_LVL = get_env("LOG_LEVEL", default="INFO")
```

---

### Q80. Recursively list all files in a directory
```python
from pathlib import Path

def list_files(directory: str, extension: str = None) -> list[str]:
    path = Path(directory)
    if extension:
        return [str(f) for f in path.rglob(f"*.{extension}")]
    return [str(f) for f in path.rglob("*") if f.is_file()]

# list_files("/data", "csv") → all .csv files recursively
```

---

## SECTION 8 — Sorting & Searching

### Q81. Implement bubble sort
```python
def bubble_sort(lst: list) -> list:
    arr = lst[:]
    n = len(arr)
    for i in range(n):
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
    return arr
```

---

### Q82. Implement merge sort
```python
def merge_sort(lst: list) -> list:
    if len(lst) <= 1:
        return lst
    mid = len(lst) // 2
    left = merge_sort(lst[:mid])
    right = merge_sort(lst[mid:])
    result, i, j = [], 0, 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
    return result + left[i:] + right[j:]
```

---

### Q83. Binary search
```python
def binary_search(arr: list, target: int) -> int:
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1  # not found

# binary_search([1,3,5,7,9,11], 7) → 3
```

---

### Q84. Sort by multiple keys
```python
employees = [
    {"name": "Alice", "dept": "IT", "salary": 90000},
    {"name": "Bob",   "dept": "HR", "salary": 75000},
    {"name": "Carol", "dept": "IT", "salary": 85000},
]

# Sort by dept ascending, then salary descending
sorted_emp = sorted(employees, key=lambda x: (x["dept"], -x["salary"]))
```

---

### Q85. Find index of first element greater than target
```python
import bisect

def first_greater(arr: list, target: int) -> int:
    idx = bisect.bisect_right(arr, target)
    return idx if idx < len(arr) else -1

# first_greater([1,3,5,7,9], 5) → 3 (index of 7)
```

---

## SECTION 9 — Functional Python

### Q86. Use `map`, `filter`, `reduce`
```python
from functools import reduce

nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

squares_of_evens = list(map(lambda x: x**2, filter(lambda x: x%2==0, nums)))
# [4, 16, 36, 64, 100]

total = reduce(lambda a, b: a + b, nums)
# 55
```

---

### Q87. List, dict, set comprehensions
```python
# List comprehension — squares of even numbers
squares = [x**2 for x in range(10) if x % 2 == 0]

# Dict comprehension — square mapping
sq_map = {x: x**2 for x in range(1, 6)}
# {1:1, 2:4, 3:9, 4:16, 5:25}

# Set comprehension — unique lengths of words
words = ["hello", "world", "hi", "hey"]
lengths = {len(w) for w in words}
# {2, 3, 5}
```

---

### Q88. zip and enumerate tricks
```python
names = ["Alice", "Bob", "Carol"]
scores = [95, 82, 91]

# Pair names with scores
paired = list(zip(names, scores))
# [("Alice",95), ("Bob",82), ("Carol",91)]

# Enumerate with index
for i, name in enumerate(names, start=1):
    print(f"{i}. {name}")

# Transpose a matrix
matrix = [[1,2,3],[4,5,6],[7,8,9]]
transposed = list(map(list, zip(*matrix)))
# [[1,4,7],[2,5,8],[3,6,9]]
```

---

### Q89. `any()` and `all()` usage
```python
nums = [2, 4, 6, 8]

all_even = all(n % 2 == 0 for n in nums)  # True
has_negative = any(n < 0 for n in nums)   # False

# Check if all required keys exist in a dict
required = ["name", "email", "age"]
data = {"name": "Alice", "email": "a@b.com", "age": 30}
valid = all(k in data for k in required)   # True
```

---

### Q90. `sorted` with custom comparator using `functools.cmp_to_key`
```python
from functools import cmp_to_key

def compare(a, b):
    # Sort strings by length, then alphabetically
    if len(a) != len(b):
        return len(a) - len(b)
    return (a > b) - (a < b)

words = ["banana", "fig", "apple", "kiwi", "date"]
sorted_words = sorted(words, key=cmp_to_key(compare))
# ['fig', 'date', 'kiwi', 'apple', 'banana']
```

---

## SECTION 10 — Regex & Text Processing

### Q91. Extract emails from text
```python
import re

def extract_emails(text: str) -> list:
    pattern = r"[a-zA-Z0-9._%+\-]+@[a-zA-Z0-9.\-]+\.[a-zA-Z]{2,}"
    return re.findall(pattern, text)

text = "Contact alice@example.com or bob.smith@corp.org for info"
# → ['alice@example.com', 'bob.smith@corp.org']
```

---

### Q92. Extract phone numbers from text
```python
import re

def extract_phones(text: str) -> list:
    pattern = r"(\+?\d{1,3}[\s\-]?)?(\(?\d{3}\)?[\s\-]?)(\d{3}[\s\-]?\d{4})"
    return [m.group() for m in re.finditer(pattern, text)]
```

---

### Q93. Validate email format
```python
import re

def is_valid_email(email: str) -> bool:
    pattern = r"^[a-zA-Z0-9._%+\-]+@[a-zA-Z0-9.\-]+\.[a-zA-Z]{2,}$"
    return bool(re.match(pattern, email))
```

---

### Q94. Replace multiple spaces with one
```python
import re

def normalize_spaces(text: str) -> str:
    return re.sub(r"\s+", " ", text).strip()

# "Hello   World  " → "Hello World"
```

---

### Q95. Split a CSV line handling quoted fields
```python
import csv, io

def parse_csv_line(line: str) -> list:
    reader = csv.reader(io.StringIO(line))
    return next(reader)

# parse_csv_line('Alice,"30,000","New York"') → ['Alice','30,000','New York']
```

---

## SECTION 11 — Concurrency & Async

### Q96. Run multiple tasks concurrently with `asyncio.gather`
```python
import asyncio

async def fetch_data(id: int) -> dict:
    await asyncio.sleep(0.1)  # simulate API call
    return {"id": id, "data": f"result_{id}"}

async def main():
    results = await asyncio.gather(
        fetch_data(1),
        fetch_data(2),
        fetch_data(3)
    )
    return results

results = asyncio.run(main())
```

---

### Q97. ThreadPoolExecutor for parallel I/O
```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import requests

def fetch_url(url: str) -> str:
    return requests.get(url, timeout=10).text[:100]

urls = ["https://example.com", "https://httpbin.org/get"]

with ThreadPoolExecutor(max_workers=5) as executor:
    futures = {executor.submit(fetch_url, url): url for url in urls}
    for future in as_completed(futures):
        url = futures[future]
        try:
            result = future.result()
            print(f"{url}: {len(result)} chars")
        except Exception as e:
            print(f"{url} failed: {e}")
```

---

### Q98. ProcessPoolExecutor for CPU-bound tasks
```python
from concurrent.futures import ProcessPoolExecutor
import math

def is_prime(n: int) -> bool:
    if n < 2: return False
    return all(n % i != 0 for i in range(2, int(math.sqrt(n)) + 1))

numbers = list(range(10_000, 11_000))

with ProcessPoolExecutor(max_workers=4) as executor:
    primes = list(executor.map(is_prime, numbers))

prime_numbers = [n for n, is_p in zip(numbers, primes) if is_p]
```

---

### Q99. Thread-safe counter with Lock
```python
import threading

class SafeCounter:
    def __init__(self):
        self._count = 0
        self._lock = threading.Lock()

    def increment(self):
        with self._lock:
            self._count += 1

    @property
    def value(self) -> int:
        return self._count

counter = SafeCounter()
threads = [threading.Thread(target=counter.increment) for _ in range(1000)]
for t in threads: t.start()
for t in threads: t.join()
print(counter.value)  # exactly 1000
```

---

### Q100. Async context manager
```python
import asyncio

class AsyncDB:
    async def __aenter__(self):
        print("Connecting to DB...")
        await asyncio.sleep(0.01)  # simulate connect
        return self

    async def __aexit__(self, *args):
        print("Closing DB connection")

    async def query(self, sql: str) -> list:
        await asyncio.sleep(0.01)
        return [{"id": 1}]

async def main():
    async with AsyncDB() as db:
        result = await db.query("SELECT * FROM claims")
```

---

## SECTION 12 — Practical Real-World Patterns

### Q101. Pagination helper
```python
def paginate(items: list, page: int, page_size: int) -> dict:
    total = len(items)
    start = (page - 1) * page_size
    end = start + page_size
    return {
        "data": items[start:end],
        "page": page,
        "page_size": page_size,
        "total": total,
        "total_pages": (total + page_size - 1) // page_size,
        "has_next": end < total,
        "has_prev": page > 1,
    }

result = paginate(list(range(1, 101)), page=2, page_size=10)
# data=[11..20], has_next=True, total_pages=10
```

---

### Q102. Flatten and count API response items
```python
def count_by_status(claims: list[dict]) -> dict:
    from collections import Counter
    return dict(Counter(c["status"] for c in claims))

claims = [
    {"id":1,"status":"approved"},
    {"id":2,"status":"denied"},
    {"id":3,"status":"approved"},
    {"id":4,"status":"pending"},
]
# → {"approved":2,"denied":1,"pending":1}
```

---

### Q103. Safe divide avoiding ZeroDivisionError
```python
def safe_divide(a: float, b: float, default: float = 0.0) -> float:
    return a / b if b != 0 else default

# Use in pandas:
df["rate"] = df.apply(lambda r: safe_divide(r["paid"], r["billed"]), axis=1)
```

---

### Q104. Validate and parse a date string
```python
from datetime import datetime

def parse_date(date_str: str, fmt: str = "%Y-%m-%d"):
    for format in [fmt, "%m/%d/%Y", "%d-%m-%Y", "%Y%m%d"]:
        try:
            return datetime.strptime(date_str, format).date()
        except ValueError:
            continue
    raise ValueError(f"Cannot parse date: {date_str}")

parse_date("2024-01-15")    # date(2024, 1, 15)
parse_date("01/15/2024")    # date(2024, 1, 15)
```

---

### Q105. Deep copy vs shallow copy — demonstrate the difference
```python
import copy

original = {"name": "Alice", "scores": [90, 85, 92]}

shallow = original.copy()
shallow["scores"].append(100)
print(original["scores"])  # [90, 85, 92, 100] — MUTATED!

deep = copy.deepcopy(original)
deep["scores"].append(200)
print(original["scores"])  # [90, 85, 92, 100] — NOT mutated
```

---

### Q106. Mutable default argument bug — fix it
```python
# WRONG — list is shared across all calls:
def append_to(item, lst=[]):
    lst.append(item)
    return lst

# CORRECT — use None as default:
def append_to(item, lst=None):
    if lst is None:
        lst = []
    lst.append(item)
    return lst
```

---

### Q107. Swap two variables without temp
```python
a, b = 10, 20
a, b = b, a
print(a, b)  # 20 10
```

---

### Q108. Check if a list is sorted
```python
def is_sorted(lst: list, reverse: bool = False) -> bool:
    return lst == sorted(lst, reverse=reverse)

# or efficiently (no full sort):
def is_sorted_fast(lst: list) -> bool:
    return all(lst[i] <= lst[i+1] for i in range(len(lst)-1))
```

---

### Q109. Find all pairs with a given difference
```python
def find_pairs_with_diff(nums: list, diff: int) -> list:
    seen = set(nums)
    return [(n, n+diff) for n in nums if (n+diff) in seen and n != n+diff]

# find_pairs_with_diff([1,5,3,4,2], 2) → [(1,3),(3,5),(2,4)]
```

---

### Q110. Implement a simple cache with TTL (no Redis)
```python
import time

class TTLCache:
    def __init__(self, ttl_seconds: int):
        self.ttl = ttl_seconds
        self._store = {}

    def set(self, key, value):
        self._store[key] = (value, time.monotonic() + self.ttl)

    def get(self, key):
        if key in self._store:
            value, expires = self._store[key]
            if time.monotonic() < expires:
                return value
            del self._store[key]
        return None

cache = TTLCache(ttl_seconds=60)
cache.set("user_123", {"name": "Alice"})
cache.get("user_123")  # {"name": "Alice"}
```

---

## SECTION 13 — Tricky / Brain Teasers Asked in Rounds

### Q111. What does this print?
```python
x = [1, 2, 3]
y = x
y.append(4)
print(x)  # [1, 2, 3, 4] — same list object!

# Fix with copy:
y = x.copy()
y.append(4)
print(x)  # [1, 2, 3]
```

---

### Q112. What's the output?
```python
def f(x=[]):
    x.append(1)
    return x

print(f())  # [1]
print(f())  # [1, 1]  ← mutable default!
print(f())  # [1, 1, 1]
```

---

### Q113. Closure — late binding gotcha
```python
# WRONG — all functions capture same i:
funcs = [lambda: i for i in range(5)]
print(funcs[0]())  # 4, not 0!

# CORRECT — use default arg to capture value:
funcs = [lambda i=i: i for i in range(5)]
print(funcs[0]())  # 0 ✓
```

---

### Q114. `is` vs `==`
```python
a = [1, 2, 3]
b = [1, 2, 3]
print(a == b)  # True  (same value)
print(a is b)  # False (different objects)

x = 256
y = 256
print(x is y)  # True  (Python caches small ints -5 to 256)
```

---

### Q115. Unpacking tricks
```python
first, *rest = [1, 2, 3, 4, 5]
# first=1, rest=[2,3,4,5]

*head, last = [1, 2, 3, 4, 5]
# head=[1,2,3,4], last=5

a, b, *_, z = [1, 2, 3, 4, 5, 6]
# a=1, b=2, z=6
```

---

### Q116. `//` vs `/` vs `%`
```python
print(7 / 2)    # 3.5  — float division
print(7 // 2)   # 3    — floor division
print(7 % 2)    # 1    — modulo (remainder)
print(-7 // 2)  # -4   — floors toward negative infinity!
print(-7 % 2)   # 1    — always same sign as divisor
```

---

### Q117. List multiplication vs nested list
```python
# CORRECT 1D:
row = [0] * 5   # [0,0,0,0,0]

# WRONG 2D — all rows are same object:
grid = [[0] * 3] * 3
grid[0][0] = 1
print(grid)  # [[1,0,0],[1,0,0],[1,0,0]] — all rows changed!

# CORRECT 2D:
grid = [[0]*3 for _ in range(3)]
grid[0][0] = 1
print(grid)  # [[1,0,0],[0,0,0],[0,0,0]] ✓
```

---

### Q118. Generator vs list for `sum`
```python
import sys

lst = [x**2 for x in range(1_000_000)]
gen = (x**2 for x in range(1_000_000))

print(sys.getsizeof(lst))  # ~8MB
print(sys.getsizeof(gen))  # ~112 bytes

# Same result, massively different memory:
sum(x**2 for x in range(1_000_000))  # use generator
```

---

## SECTION 14 — Frequently Asked Pattern Problems (Non-DSA)

### Q119. FizzBuzz (always asked for screening)
```python
def fizzbuzz(n: int) -> list:
    result = []
    for i in range(1, n+1):
        if i % 15 == 0:
            result.append("FizzBuzz")
        elif i % 3 == 0:
            result.append("Fizz")
        elif i % 5 == 0:
            result.append("Buzz")
        else:
            result.append(str(i))
    return result
```

---

### Q120. Caesar cipher
```python
def caesar_cipher(text: str, shift: int) -> str:
    result = []
    for ch in text:
        if ch.isalpha():
            base = ord("A") if ch.isupper() else ord("a")
            result.append(chr((ord(ch) - base + shift) % 26 + base))
        else:
            result.append(ch)
    return "".join(result)

# caesar_cipher("Hello World", 3) → "Khoor Zruog"
```

---

### Q121. Matrix transpose
```python
def transpose(matrix: list[list]) -> list[list]:
    return [list(row) for row in zip(*matrix)]

# [[1,2,3],[4,5,6]] → [[1,4],[2,5],[3,6]]
```

---

### Q122. Find common elements in N lists
```python
from functools import reduce

def common_elements(*lists) -> set:
    return reduce(lambda a, b: set(a) & set(b), lists)

# common_elements([1,2,3],[2,3,4],[3,4,5]) → {3}
```

---

### Q123. Flatten a dict into a list of (key, value) tuples recursively
```python
def dict_to_pairs(d: dict, prefix: str = "") -> list:
    pairs = []
    for k, v in d.items():
        key = f"{prefix}.{k}" if prefix else k
        if isinstance(v, dict):
            pairs.extend(dict_to_pairs(v, key))
        else:
            pairs.append((key, v))
    return pairs

# {"a":{"b":1,"c":2},"d":3} → [("a.b",1),("a.c",2),("d",3)]
```

---

### Q124. Convert list of tuples to dict, handling duplicate keys
```python
from collections import defaultdict

def tuples_to_dict(pairs: list[tuple]) -> dict:
    result = defaultdict(list)
    for k, v in pairs:
        result[k].append(v)
    return dict(result)

# [("a",1),("b",2),("a",3)] → {"a":[1,3],"b":[2]}
```

---

### Q125. Find all pairs in a list that sum to target (all pairs, not just first)
```python
def all_pairs_with_sum(nums: list, target: int) -> list:
    seen, pairs = set(), []
    for n in nums:
        complement = target - n
        if complement in seen:
            pairs.append((complement, n))
        seen.add(n)
    return pairs

# all_pairs_with_sum([1,2,3,4,5,6], 7) → [(1,6),(2,5),(3,4)]
```

---

### Q126. Sliding window — max sum of k consecutive elements
```python
def max_sum_window(lst: list, k: int) -> int:
    window_sum = sum(lst[:k])
    max_sum = window_sum
    for i in range(k, len(lst)):
        window_sum += lst[i] - lst[i-k]
        max_sum = max(max_sum, window_sum)
    return max_sum

# max_sum_window([1,4,2,9,7,3,8], 3) → 20 (from [9,7,4]? no... [9,7,3]? → 19, [2,9,7]→18, check: 1+4+2=7,4+2+9=15,2+9+7=18,9+7+3=19,7+3+8=18) → 19
```

---

### Q127. Count number of set bits in an integer
```python
def count_set_bits(n: int) -> int:
    return bin(n).count("1")

# Also: n.bit_count() in Python 3.10+
# count_set_bits(13) → 3  (1101 has three 1s)
```

---

### Q128. Roman numeral to integer
```python
def roman_to_int(s: str) -> int:
    vals = {"I":1,"V":5,"X":10,"L":50,"C":100,"D":500,"M":1000}
    total = 0
    for i in range(len(s)):
        if i+1 < len(s) and vals[s[i]] < vals[s[i+1]]:
            total -= vals[s[i]]
        else:
            total += vals[s[i]]
    return total

# roman_to_int("MCMXCIV") → 1994
```

---

### Q129. Find the majority element (appears > n/2 times)
```python
def majority_element(nums: list) -> int:
    count, candidate = 0, None
    for n in nums:
        if count == 0:
            candidate = n
        count += 1 if n == candidate else -1
    return candidate

# [2,2,1,1,1,2,2] → 2 (Boyer-Moore voting)
```

---

### Q130. Product of array except self (no division)
```python
def product_except_self(nums: list) -> list:
    n = len(nums)
    result = [1] * n
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]
    suffix = 1
    for i in range(n-1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]
    return result

# [1,2,3,4] → [24,12,8,6]
```

---

### Q131. Anagram grouping
```python
from collections import defaultdict

def group_anagrams(words: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    for word in words:
        key = "".join(sorted(word))
        groups[key].append(word)
    return list(groups.values())

# ["eat","tea","tan","ate","nat","bat"] → [["eat","tea","ate"],["tan","nat"],["bat"]]
```

---

### Q132. Longest substring without repeating characters
```python
def longest_unique_substring(s: str) -> int:
    seen = {}
    start = max_len = 0
    for i, ch in enumerate(s):
        if ch in seen and seen[ch] >= start:
            start = seen[ch] + 1
        seen[ch] = i
        max_len = max(max_len, i - start + 1)
    return max_len

# "abcabcbb" → 3 ("abc")
```

---

### Q133. Valid parentheses combinations (generate all)
```python
def generate_parentheses(n: int) -> list[str]:
    result = []
    def backtrack(current, open_count, close_count):
        if len(current) == 2 * n:
            result.append(current)
            return
        if open_count < n:
            backtrack(current + "(", open_count+1, close_count)
        if close_count < open_count:
            backtrack(current + ")", open_count, close_count+1)
    backtrack("", 0, 0)
    return result

# generate_parentheses(3) → ["((()))","(()())","(())()","()(())","()()()"]
```

---

### Q134. Merge overlapping intervals
```python
def merge_intervals(intervals: list[list]) -> list[list]:
    if not intervals:
        return []
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged

# [[1,3],[2,6],[8,10],[15,18]] → [[1,6],[8,10],[15,18]]
```

---

### Q135. Spiral order of a matrix
```python
def spiral_order(matrix: list[list]) -> list:
    result = []
    while matrix:
        result += matrix.pop(0)          # top row
        if matrix and matrix[0]:
            for row in matrix:
                result.append(row.pop()) # right column
        if matrix:
            result += matrix.pop()[::-1] # bottom row
        if matrix and matrix[0]:
            for row in matrix[::-1]:
                result.append(row.pop(0))# left column
    return result
```

---

## SECTION 15 — Error Handling & Testing

### Q136. Write proper exception handling
```python
class InsufficientFundsError(Exception):
    def __init__(self, amount: float, balance: float):
        super().__init__(f"Cannot withdraw {amount:.2f}, balance is {balance:.2f}")
        self.amount = amount
        self.balance = balance

def withdraw(balance: float, amount: float) -> float:
    if amount <= 0:
        raise ValueError("Amount must be positive")
    if amount > balance:
        raise InsufficientFundsError(amount, balance)
    return balance - amount

try:
    result = withdraw(100.0, 150.0)
except InsufficientFundsError as e:
    print(f"Error: {e}")
except ValueError as e:
    print(f"Invalid input: {e}")
finally:
    print("Transaction attempted")
```

---

### Q137. Write unit tests with `pytest`
```python
import pytest
from my_module import two_sum, is_palindrome

def test_two_sum_basic():
    assert two_sum([2, 7, 11, 15], 9) == (0, 1)

def test_two_sum_no_result():
    assert two_sum([1, 2, 3], 100) == ()

def test_palindrome_true():
    assert is_palindrome("racecar") is True

def test_palindrome_false():
    assert is_palindrome("hello") is False

def test_palindrome_with_spaces():
    assert is_palindrome("A man a plan a canal Panama") is True

@pytest.mark.parametrize("s, expected", [
    ("racecar", True),
    ("hello", False),
    ("", True),
    ("a", True),
])
def test_palindrome_parametrized(s, expected):
    assert is_palindrome(s) == expected
```

---

### Q138. Mock an external API call in tests
```python
from unittest.mock import patch, AsyncMock
import pytest

@pytest.mark.asyncio
async def test_fetch_user():
    mock_response = {"id": 1, "name": "Alice", "email": "alice@example.com"}
    with patch("mymodule.httpx.AsyncClient.get", new_callable=AsyncMock) as mock_get:
        mock_get.return_value.json = AsyncMock(return_value=mock_response)
        mock_get.return_value.status_code = 200
        result = await fetch_user(1)
        assert result["name"] == "Alice"
        mock_get.assert_called_once()
```

---

### Q139. Write a function with `*args` and `**kwargs`
```python
def flexible_fn(*args, **kwargs):
    print(f"Positional: {args}")
    print(f"Keyword: {kwargs}")
    return {"args": args, "kwargs": kwargs}

flexible_fn(1, 2, 3, name="Alice", role="Engineer")
# Positional: (1, 2, 3)
# Keyword: {'name': 'Alice', 'role': 'Engineer'}
```

---

### Q140. Implement `__getitem__` and `__len__`
```python
class NumberList:
    def __init__(self, data: list):
        self._data = data

    def __getitem__(self, index):
        return self._data[index]

    def __len__(self):
        return len(self._data)

    def __contains__(self, item):
        return item in self._data

nums = NumberList([10, 20, 30, 40])
print(nums[2])      # 30
print(len(nums))    # 4
print(20 in nums)   # True
print(nums[1:3])    # [20, 30]
```

---

## SECTION 16 — Final Boss Questions (Asked for Senior Roles)

### Q141. Implement a thread-safe singleton with double-checked locking
```python
import threading

class DatabaseConnection:
    _instance = None
    _lock = threading.Lock()

    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                if cls._instance is None:  # double-check
                    cls._instance = super().__new__(cls)
                    cls._instance._connected = False
        return cls._instance

    def connect(self, dsn: str):
        if not self._connected:
            print(f"Connecting to {dsn}")
            self._connected = True
```

---

### Q142. Implement a Pipeline using method chaining
```python
class DataPipeline:
    def __init__(self, data: list):
        self._data = data

    def filter(self, fn) -> "DataPipeline":
        self._data = [x for x in self._data if fn(x)]
        return self

    def map(self, fn) -> "DataPipeline":
        self._data = [fn(x) for x in self._data]
        return self

    def sort(self, key=None, reverse=False) -> "DataPipeline":
        self._data.sort(key=key, reverse=reverse)
        return self

    def limit(self, n: int) -> "DataPipeline":
        self._data = self._data[:n]
        return self

    def collect(self) -> list:
        return self._data

result = (
    DataPipeline([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
    .filter(lambda x: x % 2 == 0)
    .map(lambda x: x ** 2)
    .sort(reverse=True)
    .limit(3)
    .collect()
)
# [100, 64, 36]
```

---

### Q143. Implement a rate limiter (token bucket)
```python
import time, threading

class TokenBucket:
    def __init__(self, rate: float, capacity: float):
        self.rate = rate          # tokens added per second
        self.capacity = capacity  # max tokens
        self.tokens = capacity
        self.last = time.monotonic()
        self._lock = threading.Lock()

    def allow(self) -> bool:
        with self._lock:
            now = time.monotonic()
            self.tokens = min(
                self.capacity,
                self.tokens + (now - self.last) * self.rate
            )
            self.last = now
            if self.tokens >= 1:
                self.tokens -= 1
                return True
            return False

limiter = TokenBucket(rate=10, capacity=10)  # 10 req/sec
if limiter.allow():
    process_request()
else:
    return_429()
```

---

### Q144. Implement observer pattern
```python
from typing import Callable

class EventEmitter:
    def __init__(self):
        self._listeners: dict[str, list[Callable]] = {}

    def on(self, event: str, fn: Callable):
        self._listeners.setdefault(event, []).append(fn)

    def emit(self, event: str, *args, **kwargs):
        for fn in self._listeners.get(event, []):
            fn(*args, **kwargs)

emitter = EventEmitter()
emitter.on("claim_approved", lambda c: print(f"Email sent for claim {c['id']}"))
emitter.on("claim_approved", lambda c: print(f"Payment queued for {c['id']}"))
emitter.emit("claim_approved", {"id": "C001", "amount": 500})
```

---

### Q145. Implement a simple dependency injection container
```python
class Container:
    def __init__(self):
        self._services = {}

    def register(self, name: str, factory):
        self._services[name] = factory

    def resolve(self, name: str):
        if name not in self._services:
            raise KeyError(f"Service '{name}' not registered")
        return self._services[name]()

container = Container()
container.register("db", lambda: DatabaseConnection())
container.register("cache", lambda: RedisCache())

db = container.resolve("db")
cache = container.resolve("cache")
```

---

### Q146. Write a function that detects cycles in a list of dependencies
```python
def has_cycle(graph: dict) -> bool:
    WHITE, GRAY, BLACK = 0, 1, 2
    color = {node: WHITE for node in graph}

    def dfs(node) -> bool:
        color[node] = GRAY
        for neighbor in graph.get(node, []):
            if color[neighbor] == GRAY:
                return True  # back edge = cycle
            if color[neighbor] == WHITE and dfs(neighbor):
                return True
        color[node] = BLACK
        return False

    return any(dfs(node) for node in graph if color[node] == WHITE)

# has_cycle({"A":["B"],"B":["C"],"C":["A"]}) → True
# has_cycle({"A":["B"],"B":["C"],"C":[]}) → False
```

---

### Q147. Implement `functools.partial` manually
```python
def partial(func, *partial_args, **partial_kwargs):
    def wrapper(*args, **kwargs):
        return func(*partial_args, *args, **{**partial_kwargs, **kwargs})
    return wrapper

def power(base, exponent):
    return base ** exponent

square = partial(power, exponent=2)
cube   = partial(power, exponent=3)

print(square(4))  # 16
print(cube(3))    # 27
```

---

### Q148. Lazy property using `__set_name__` and `__get__`
```python
class lazy_property:
    def __init__(self, fn):
        self.fn = fn
        self.name = None

    def __set_name__(self, owner, name):
        self.name = name

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        value = self.fn(obj)
        setattr(obj, self.name, value)  # cache on instance
        return value

class DataProcessor:
    def __init__(self, path):
        self.path = path

    @lazy_property
    def data(self):
        print("Loading data...")
        return load_csv(self.path)  # only runs once

proc = DataProcessor("data.csv")
proc.data  # "Loading data..." then loads
proc.data  # returns cached value instantly
```

---

### Q149. Implement `zip` without using `zip`
```python
def my_zip(*iterables):
    iterators = [iter(it) for it in iterables]
    while True:
        try:
            yield tuple(next(it) for it in iterators)
        except StopIteration:
            return

list(my_zip([1,2,3], ["a","b","c"])) # [(1,"a"),(2,"b"),(3,"c")]
```

---

### Q150. Write a complete async web scraper with error handling
```python
import asyncio
import aiohttp
from dataclasses import dataclass

@dataclass
class PageResult:
    url: str
    status: int
    length: int
    error: str | None = None

async def fetch_page(session: aiohttp.ClientSession,
                     url: str, sem: asyncio.Semaphore) -> PageResult:
    async with sem:
        try:
            async with session.get(url, timeout=aiohttp.ClientTimeout(total=10)) as resp:
                content = await resp.text()
                return PageResult(url=url, status=resp.status, length=len(content))
        except asyncio.TimeoutError:
            return PageResult(url=url, status=0, length=0, error="Timeout")
        except Exception as e:
            return PageResult(url=url, status=0, length=0, error=str(e))

async def scrape_all(urls: list[str], max_concurrent: int = 20) -> list[PageResult]:
    sem = asyncio.Semaphore(max_concurrent)
    connector = aiohttp.TCPConnector(limit=max_concurrent)
    async with aiohttp.ClientSession(connector=connector) as session:
        tasks = [fetch_page(session, url, sem) for url in urls]
        return await asyncio.gather(*tasks)

results = asyncio.run(scrape_all(["https://example.com", "https://httpbin.org/get"]))
```

---

## QUICK REFERENCE — Most Frequently Asked (Top 25 You Must Solve)

| # | Problem | Key Technique |
|---|---------|---------------|
| 1 | Two Sum | HashMap |
| 2 | Palindrome check | Reverse / two pointer |
| 3 | Anagram check | Counter |
| 4 | Fibonacci | Iterative / memo |
| 5 | Reverse string / list | Slice `[::-1]` |
| 6 | Find duplicates | Set / Counter |
| 7 | FizzBuzz | Modulo |
| 8 | Stack implementation | List |
| 9 | Balanced brackets | Stack |
| 10 | Merge sorted lists | Two pointer |
| 11 | Remove duplicates preserving order | Set + list comp |
| 12 | Kadane's algorithm (max subarray) | Running max |
| 13 | Missing number 1..N | Sum formula |
| 14 | Word frequency | Counter |
| 15 | Flatten nested list | Recursive generator |
| 16 | Group by key | defaultdict |
| 17 | Sort list of dicts | `sorted(key=lambda)` |
| 18 | LRU Cache | OrderedDict |
| 19 | Binary search | lo/hi/mid |
| 20 | Rotate list by k | Slice rearrange |
| 21 | Run-length encoding | Loop with count |
| 22 | Merge intervals | Sort + compare |
| 23 | Longest unique substring | Sliding window |
| 24 | Anagram grouping | sorted key |
| 25 | Product except self | Prefix + suffix pass |
