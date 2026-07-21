# Top 100 Practical Python Coding Questions (No Heavy DSA)
> Focus: 3–15 Years | Non-FAANG | Real Python Logic, Data Handling, Transformations, Debugging
> Sources: Glassdoor, LinkedIn, Reddit, GeeksforGeeks, LeetCode Discuss (non-hard)

---

## SECTION 1: String & Text Processing (Q1–Q15)

### Q1
- **Question:** Write a function that extracts all email addresses from a large text string.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import re
  def extract_emails(text: str) -> list[str]:
      pattern = r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}'
      return re.findall(pattern, text)
  ```
- **Follow-Ups:** How do you handle edge cases like `user+tag@domain.co.uk`?

---

### Q2
- **Question:** Write a function that parses a log line and returns a structured dict.
  Input: `"2024-01-15 12:34:56 ERROR service.py:45 Connection timeout"`
- **Level:** 5–8 Yrs | **Company:** Healthcare, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import re
  from datetime import datetime

  def parse_log_line(line: str) -> dict:
      pattern = r'(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) (\w+) ([\w.]+:\d+) (.+)'
      m = re.match(pattern, line)
      if not m:
          raise ValueError(f"Invalid log line: {line}")
      return {
          "timestamp": datetime.strptime(m.group(1), "%Y-%m-%d %H:%M:%S"),
          "level": m.group(2),
          "location": m.group(3),
          "message": m.group(4)
      }
  ```
- **Follow-Ups:** How do you handle multi-line log entries?

---

### Q3
- **Question:** Write a function to count word frequency in a text, ignoring punctuation and case.
- **Level:** 3–5 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import re
  from collections import Counter

  def word_frequency(text: str) -> Counter:
      words = re.findall(r'\b[a-z]+\b', text.lower())
      return Counter(words)
  ```

---

### Q4
- **Question:** Write a function to mask PII in a text string (SSN, phone, email → `[REDACTED]`).
- **Level:** 5–8 Yrs | **Company:** Healthcare, Insurance, Finance | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import re

  def mask_pii(text: str) -> str:
      # SSN: 123-45-6789
      text = re.sub(r'\b\d{3}-\d{2}-\d{4}\b', '[SSN REDACTED]', text)
      # Phone: (123) 456-7890 or 123-456-7890
      text = re.sub(r'\(?\d{3}\)?[-.\s]\d{3}[-.\s]\d{4}', '[PHONE REDACTED]', text)
      # Email
      text = re.sub(r'[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}', '[EMAIL REDACTED]', text)
      return text
  ```
- **Follow-Ups:** How would you handle PHI like diagnosis codes or member IDs?

---

### Q5
- **Question:** Write a function that converts a snake_case string to camelCase and vice versa.
- **Level:** 3–5 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def snake_to_camel(s: str) -> str:
      parts = s.split('_')
      return parts[0] + ''.join(p.capitalize() for p in parts[1:])

  def camel_to_snake(s: str) -> str:
      import re
      return re.sub(r'(?<!^)(?=[A-Z])', '_', s).lower()
  ```

---

### Q6–Q15 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 6 | Write a function to validate a date string matches `YYYY-MM-DD` format | 3–5 Yrs | High | T1 |
| 7 | Write a function to truncate a string to N words (not characters) | 3–5 Yrs | High | T1 |
| 8 | Write a function to find the most frequent character in a string | 3–5 Yrs | High | T1 |
| 9 | Write a function to check if two strings are anagrams | 3–5 Yrs | High | T1 |
| 10 | Write a function to parse a CSV-like line respecting quoted fields with commas | 5–8 Yrs | Medium | T2 |
| 11 | Write a function to generate a slug from a title string | 3–5 Yrs | Medium | T2 |
| 12 | Write a function to extract all URLs from an HTML string | 5–8 Yrs | Medium | T2 |
| 13 | Write a function to normalize whitespace in a string (collapse multiple spaces) | 3–5 Yrs | High | T1 |
| 14 | Write a function to tokenize a prompt into sentences using basic rules | 5–8 Yrs | Medium | T2 |
| 15 | Write a function to detect if a block of text exceeds N tokens (use `tiktoken`) | 8–12 Yrs | High | T1 |

---

## SECTION 2: List & Dict Manipulation (Q16–Q35)

### Q16
- **Question:** Write a function to deeply merge two nested dicts (second dict overrides first).
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def deep_merge(base: dict, override: dict) -> dict:
      result = base.copy()
      for key, val in override.items():
          if key in result and isinstance(result[key], dict) and isinstance(val, dict):
              result[key] = deep_merge(result[key], val)
          else:
              result[key] = val
      return result
  ```

---

### Q17
- **Question:** Write a function to flatten a nested dict with dot-notation keys.
  Input: `{"a": {"b": {"c": 1}}}` → `{"a.b.c": 1}`
- **Level:** 5–8 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def flatten_dict(d: dict, prefix: str = '') -> dict:
      result = {}
      for k, v in d.items():
          key = f"{prefix}.{k}" if prefix else k
          if isinstance(v, dict):
              result.update(flatten_dict(v, key))
          else:
              result[key] = v
      return result
  ```

---

### Q18
- **Question:** Write a function that groups a list of dicts by a given key and returns a dict of lists.
- **Level:** 3–5 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from collections import defaultdict

  def group_by(records: list[dict], key: str) -> dict:
      groups = defaultdict(list)
      for record in records:
          groups[record[key]].append(record)
      return dict(groups)
  ```

---

### Q19
- **Question:** Write a function to deduplicate a list of dicts by a given key.
- **Level:** 3–5 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def dedup_by_key(records: list[dict], key: str) -> list[dict]:
      seen = set()
      result = []
      for r in records:
          val = r[key]
          if val not in seen:
              seen.add(val)
              result.append(r)
      return result
  ```

---

### Q20
- **Question:** Write a function to rotate a list left by N positions.
- **Level:** 3–5 Yrs | **Company:** All | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  def rotate_left(lst: list, n: int) -> list:
      if not lst:
          return lst
      n = n % len(lst)
      return lst[n:] + lst[:n]
  ```

---

### Q21–Q35 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 21 | Write a function to chunk a list into batches of size N | 3–5 Yrs | High | T1 |
| 22 | Write a function to find all keys in a nested dict that have a given value | 5–8 Yrs | High | T1 |
| 23 | Write a function to compute a running average over a list | 3–5 Yrs | High | T1 |
| 24 | Write a function to pivot a list of dicts into a dict of lists | 5–8 Yrs | Medium | T2 |
| 25 | Write a function to compute set difference between two lists of dicts by a key | 5–8 Yrs | Medium | T2 |
| 26 | Write a function to sort a list of objects by multiple fields with mixed order | 5–8 Yrs | High | T1 |
| 27 | Write a function to zip two dicts together by matching keys | 5–8 Yrs | Medium | T2 |
| 28 | Write a function to compute cumulative sum of a list | 3–5 Yrs | High | T1 |
| 29 | Write a function to find the top N items from a large list without full sort | 8–12 Yrs | Medium | T2 |
| 30 | Write a function to transpose a matrix (list of lists) using zip | 5–8 Yrs | Medium | T2 |
| 31 | Write a function to compare two dicts and return added, removed, and changed keys | 5–8 Yrs | High | T1 |
| 32 | Write a function to safely navigate a nested dict with a dot-notation path | 5–8 Yrs | High | T1 |
| 33 | Write a function to aggregate a list of dicts by a key with sum of a value field | 5–8 Yrs | High | T1 |
| 34 | Write a function to interleave two lists element by element | 3–5 Yrs | Medium | T2 |
| 35 | Write a function to convert a flat list of parent-child pairs into a tree dict | 8–12 Yrs | Medium | T2 |

---

## SECTION 3: File & Data Processing (Q36–Q55)

### Q36
- **Question:** Write a function to read a large CSV in chunks and process each chunk.
- **Level:** 5–8 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import pandas as pd

  def process_large_csv(filepath: str, chunk_size: int = 10000):
      for chunk in pd.read_csv(filepath, chunksize=chunk_size):
          # Clean
          chunk = chunk.dropna(subset=['required_col'])
          chunk['amount'] = pd.to_numeric(chunk['amount'], errors='coerce')
          yield chunk

  # Usage
  for chunk in process_large_csv("claims.csv"):
      db.bulk_insert(chunk.to_dict('records'))
  ```

---

### Q37
- **Question:** Write a function to parse a JSON file where some fields may be missing, with defaults.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import json
  from dataclasses import dataclass, field
  from typing import Optional

  @dataclass
  class Record:
      id: str
      name: str
      status: str = "unknown"
      amount: float = 0.0
      tags: list = field(default_factory=list)

  def load_records(filepath: str) -> list[Record]:
      with open(filepath) as f:
          data = json.load(f)
      return [Record(**{k: v for k, v in item.items() if k in Record.__dataclass_fields__})
              for item in data]
  ```

---

### Q38
- **Question:** Write a function to merge multiple JSON files from a directory into one list.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import json
  from pathlib import Path

  def merge_json_files(directory: str) -> list:
      result = []
      for path in Path(directory).glob("*.json"):
          with open(path) as f:
              data = json.load(f)
              if isinstance(data, list):
                  result.extend(data)
              else:
                  result.append(data)
      return result
  ```

---

### Q39
- **Question:** Write a script that reads a log file, groups errors by type, and writes a summary CSV.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import re, csv
  from collections import defaultdict

  def summarize_logs(log_path: str, out_path: str):
      error_counts = defaultdict(int)
      with open(log_path) as f:
          for line in f:
              if 'ERROR' in line:
                  m = re.search(r'ERROR\s+(.+?)(\s+at|$)', line)
                  if m:
                      error_counts[m.group(1).strip()] += 1

      with open(out_path, 'w', newline='') as f:
          writer = csv.writer(f)
          writer.writerow(['error_type', 'count'])
          for error, count in sorted(error_counts.items(), key=lambda x: -x[1]):
              writer.writerow([error, count])
  ```

---

### Q40–Q55 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 40 | Write a function to compare two CSV files and report row differences | 5–8 Yrs | High | T1 |
| 41 | Write a function to read an Excel file and clean specific columns | 5–8 Yrs | High | T1 |
| 42 | Write a function that validates required fields in each row of a CSV | 5–8 Yrs | High | T1 |
| 43 | Write a function to archive old log files older than N days | 5–8 Yrs | Medium | T2 |
| 44 | Write a generator that tails a log file in real time (like `tail -f`) | 8–12 Yrs | Medium | T2 |
| 45 | Write a function to convert a nested XML structure to a Python dict | 5–8 Yrs | Medium | T2 |
| 46 | Write a function to read and validate a YAML config file with defaults | 5–8 Yrs | High | T1 |
| 47 | Write a function to split a large file into N equal chunks | 5–8 Yrs | Medium | T2 |
| 48 | Write a function to atomically write a file (write to temp, then rename) | 8–12 Yrs | Medium | T2 |
| 49 | Write a function to read a gzipped CSV file line by line without decompressing fully | 8–12 Yrs | Medium | T2 |
| 50 | Write a function to normalize a JSON schema with snake_case keys | 5–8 Yrs | High | T1 |
| 51 | Write a function that loads all .py config files in a plugins directory | 8–12 Yrs | Medium | T2 |
| 52 | Write a function to recursively find all files with a given extension | 3–5 Yrs | High | T1 |
| 53 | Write a function to count lines, words, and characters in a file (wc equivalent) | 3–5 Yrs | Medium | T2 |
| 54 | Write a function to merge two sorted CSV files without loading both into memory | 8–12 Yrs | Medium | T2 |
| 55 | Write a function to stream newline-delimited JSON (NDJSON) and yield records | 8–12 Yrs | High | T1 |

---

## SECTION 4: Data Transformation & Business Logic (Q56–Q80)

### Q56
- **Question:** Write a function that calculates age from a date of birth string.
- **Level:** 3–5 Yrs | **Company:** Healthcare, Insurance | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from datetime import date

  def calculate_age(dob_str: str, fmt: str = "%Y-%m-%d") -> int:
      dob = date.fromisoformat(dob_str)
      today = date.today()
      return today.year - dob.year - ((today.month, today.day) < (dob.month, dob.day))
  ```

---

### Q57
- **Question:** Write a function to validate that insurance member data has required fields and valid formats.
- **Level:** 5–8 Yrs | **Company:** Healthcare, Insurance | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel, validator
  from typing import Optional
  import re

  class MemberRecord(BaseModel):
      member_id: str
      dob: str
      plan_code: str
      status: str

      @validator('member_id')
      def validate_member_id(cls, v):
          if not re.match(r'^[A-Z]{2}\d{8}$', v):
              raise ValueError('Invalid member ID format')
          return v

      @validator('status')
      def validate_status(cls, v):
          if v not in ('active', 'inactive', 'terminated'):
              raise ValueError(f'Invalid status: {v}')
          return v
  ```

---

### Q58
- **Question:** Write a function to apply business rules to a list of claims and flag those needing review.
- **Level:** 8–12 Yrs | **Company:** Healthcare, Insurance | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from dataclasses import dataclass
  from typing import Callable

  @dataclass
  class Claim:
      claim_id: str
      amount: float
      diagnosis_code: str
      provider_id: str

  RULES: list[Callable[[Claim], str | None]] = [
      lambda c: "High amount" if c.amount > 50000 else None,
      lambda c: "Unknown provider" if c.provider_id.startswith("UNK") else None,
      lambda c: "Experimental code" if c.diagnosis_code.startswith("Z99") else None,
  ]

  def flag_claims(claims: list[Claim]) -> list[tuple[Claim, list[str]]]:
      result = []
      for claim in claims:
          flags = [msg for rule in RULES if (msg := rule(claim))]
          if flags:
              result.append((claim, flags))
      return result
  ```

---

### Q59–Q80 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 59 | Write a function to compute monthly totals from a list of dated transactions | 5–8 Yrs | High | T1 |
| 60 | Write a function to detect outliers in a list of numbers using IQR method | 5–8 Yrs | Medium | T2 |
| 61 | Write a function to compute the running 7-day average from daily metrics | 5–8 Yrs | Medium | T2 |
| 62 | Write a function to reconcile two lists of records by ID and find discrepancies | 8–12 Yrs | High | T1 |
| 63 | Write a function to implement a simple rule engine from a list of condition-action pairs | 8–12 Yrs | High | T1 |
| 64 | Write a function that deduplicates claims based on member_id + service_date + amount | 5–8 Yrs | High | T1 |
| 65 | Write a function to compute discount tiers based on purchase quantity | 3–5 Yrs | Medium | T2 |
| 66 | Write a function to build a pivot table from a list of dicts without Pandas | 8–12 Yrs | Medium | T2 |
| 67 | Write a function to validate that a date range doesn't overlap with existing ranges | 8–12 Yrs | Medium | T2 |
| 68 | Write a function to parse a benefits enrollment string into a structured object | 5–8 Yrs | Medium | T2 |
| 69 | Write a function to compute SLA breach count from a list of ticket records | 8–12 Yrs | Medium | T2 |
| 70 | Write a function to convert UTC timestamps to a given timezone | 5–8 Yrs | High | T1 |
| 71 | Write a function to compute co-pay amount based on plan type and service category | 5–8 Yrs | High | T1 |
| 72 | Write a function to apply a sequence of transformations defined as a pipeline config | 8–12 Yrs | High | T1 |
| 73 | Write a function to calculate the Levenshtein distance for fuzzy name matching | 8–12 Yrs | Medium | T2 |
| 74 | Write a function that processes a batch of API responses and extracts structured data | 8–12 Yrs | High | T1 |
| 75 | Write a function to validate JSON against a schema dict without external libraries | 8–12 Yrs | Medium | T2 |
| 76 | Write a function to implement a basic state machine for claim processing workflow | 8–12 Yrs | High | T1 |
| 77 | Write a function to compute a hash fingerprint for a dict (for change detection) | 8–12 Yrs | Medium | T2 |
| 78 | Write a function to generate a unique idempotency key for an API request | 8–12 Yrs | High | T1 |
| 79 | Write a function to retry a batch operation and collect both successes and failures | 8–12 Yrs | High | T1 |
| 80 | Write a function to parse LLM JSON output with fallback when JSON is malformed | 8–12 Yrs | High | T1 |

---

## SECTION 5: Debugging & Code Quality (Q81–Q100)

### Q81
- **Question:** Here is broken code — find and fix the bug:
  ```python
  def get_users_by_dept(users, dept=[]):
      for u in users:
          if u['dept'] == 'engineering':
              dept.append(u)
      return dept
  ```
- **Level:** 3–5 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:** Mutable default argument bug — `dept=[]` is shared across calls. Fix: `dept=None`, then `if dept is None: dept = []`

---

### Q82
- **Question:** Find the bug in this code:
  ```python
  results = []
  for i in range(5):
      results.append(lambda: i * i)
  print([f() for f in results])  # Expected: [0, 1, 4, 9, 16]
  ```
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:** Late binding closure — all lambdas capture `i` by reference; at call time `i=4`. Fix: `lambda i=i: i*i`

---

### Q83
- **Question:** This function is slow for large files. Identify why and fix it:
  ```python
  def load_config(path):
      config = ""
      with open(path) as f:
          for line in f:
              config += line  # Bug: O(n²) string concatenation
      return config
  ```
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:** String `+=` in a loop creates O(n²) string copies. Fix: collect lines in list, join once: `return "".join(f.readlines())` or `return f.read()`

---

### Q84
- **Question:** Why does this fail silently?
  ```python
  def process(data):
      try:
          result = transform(data)
          return result
      except:
          pass  # Silent failure!
  ```
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:** Bare `except` swallows ALL exceptions including `KeyboardInterrupt`; returns `None` silently. Fix: catch specific exceptions; always log before suppressing; never use bare `except` in production

---

### Q85–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 85 | Fix: This generator is never consumed — what went wrong? | 5–8 Yrs | High | T1 |
| 86 | Find the thread safety bug in this shared counter class | 8–12 Yrs | High | T1 |
| 87 | Fix: This async function blocks the event loop — how? | 8–12 Yrs | High | T1 |
| 88 | This Pydantic model silently coerces strings to ints — is that correct behavior? | 5–8 Yrs | High | T1 |
| 89 | Find the N+1 query bug in this SQLAlchemy code | 8–12 Yrs | High | T1 |
| 90 | This Redis cache decorator doesn't cache — find why | 8–12 Yrs | High | T1 |
| 91 | This function mutates its input list — fix it without breaking callers | 3–5 Yrs | High | T1 |
| 92 | This datetime comparison is timezone-naive and fails in production — fix it | 5–8 Yrs | High | T1 |
| 93 | Fix: This retry decorator doesn't actually retry on connection errors | 5–8 Yrs | High | T1 |
| 94 | This dict comprehension has a subtle bug for duplicate values — find it | 5–8 Yrs | Medium | T2 |
| 95 | Find the memory leak in this class that stores callbacks | 8–12 Yrs | High | T1 |
| 96 | This FastAPI endpoint doesn't clean up its DB session on exception — fix it | 8–12 Yrs | High | T1 |
| 97 | Fix: This multiprocessing pool silently ignores exceptions in worker processes | 8–12 Yrs | Medium | T2 |
| 98 | This LangChain chain is not streaming — why? | 8–12 Yrs | High | T1 |
| 99 | This Celery task fails due to unpicklable argument — fix it | 8–12 Yrs | Medium | T2 |
| 100 | Review this Python ETL script and identify 5 production issues (performance, reliability, security) | 12–15 Yrs | High | T1 |
