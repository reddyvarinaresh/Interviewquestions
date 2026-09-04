# Top Python Questions — Service / Consulting / IT Companies
> Target: 5–15 Years | Infosys, Wipro, TCS, Cognizant, Accenture, Capgemini, HCL, EPAM, LTIMindtree
> Focus: Client delivery, project-based work, multi-domain exposure, team execution
> Sources: Glassdoor, Ambitionbox, LinkedIn, Reddit, Interview Reports

---

## What Service Company Interviews Look Like

Service companies interview for **client delivery capability** — can you:
- Write solid, maintainable Python code for client projects
- Work with SQL, REST APIs, cloud services on any client stack
- Communicate clearly with clients and team members
- Debug and deliver under deadline pressure
- Ramp up quickly on unfamiliar codebases

**Rounds:** Technical screen (30–45 min coding) → Core technical panel → Architecture/design → HR
**Depth:** Moderate — fewer internals, more practical breadth

---

## SECTION 1: High-Frequency Python Questions (Q1–Q30)

| # | Question | Topic | Frequency | Tier |
|---|----------|-------|-----------|------|
| 1 | What is the difference between list, tuple, set, dict? When do you use each? | Data Structures | Very High | T1 |
| 2 | What is mutable vs immutable in Python? Give examples. | Core | Very High | T1 |
| 3 | What are list comprehensions? Write one to filter and transform a list. | Pythonic | Very High | T1 |
| 4 | What is a decorator? Write a logging decorator. | Decorators | Very High | T1 |
| 5 | What is a generator? How does it save memory? | Generators | Very High | T1 |
| 6 | What is `*args` and `**kwargs`? Give a practical example. | Functions | Very High | T1 |
| 7 | What is the difference between `deepcopy` and `copy`? | Core | High | T1 |
| 8 | What is OOP? Explain inheritance, polymorphism, encapsulation with Python. | OOP | Very High | T1 |
| 9 | What is `@staticmethod` vs `@classmethod` vs instance method? | OOP | High | T1 |
| 10 | What is a context manager? Write one for a DB connection. | Context Managers | High | T1 |
| 11 | How do you handle exceptions in Python? Write a custom exception class. | Exceptions | Very High | T1 |
| 12 | What is the GIL? Does it affect multiprocessing? | Concurrency | High | T1 |
| 13 | When do you use threading vs multiprocessing vs asyncio? | Concurrency | High | T1 |
| 14 | What is `asyncio`? Write a simple async function. | Asyncio | High | T1 |
| 15 | How does Python's `sorted()` work? Sort a list of dicts by multiple keys. | Core | High | T1 |
| 16 | What is `defaultdict`, `Counter`, `OrderedDict`? Use cases for each. | Collections | High | T1 |
| 17 | What is `lambda`? When do you use it vs a named function? | Functions | High | T1 |
| 18 | What is LEGB scope? What is a closure? Give an example. | Scope | High | T1 |
| 19 | What is `__init__` vs `__new__`? | OOP | Medium | T2 |
| 20 | What is `__str__` vs `__repr__`? | OOP | High | T1 |
| 21 | What is `functools.lru_cache`? How does it work? | Performance | High | T1 |
| 22 | What is `itertools.chain`, `groupby`, `product`? Write examples. | Itertools | Medium | T2 |
| 23 | What is type hinting? Show a function with Pydantic validation. | Type Hints | High | T1 |
| 24 | What is `dataclass`? How does it simplify Python class definitions? | OOP | High | T1 |
| 25 | What is the difference between `is` and `==`? | Core | High | T1 |
| 26 | How do you read and write JSON, CSV, YAML files in Python? | File I/O | Very High | T1 |
| 27 | What is `os.environ`? How do you manage configs across environments? | Config | High | T1 |
| 28 | How does `requests` library work? Show a POST with JSON body and error handling. | HTTP | Very High | T1 |
| 29 | What is Python's `logging` module? Configure it for structured JSON logging. | Logging | High | T1 |
| 30 | What is `pytest`? Write a test with fixtures and `parametrize`. | Testing | High | T1 |

---

## SECTION 2: Service Company–Specific Coding Questions (Q31–Q60)

### Q31
- **Question:** You receive a list of transaction records (dict format). Group them by `customer_id` and compute total amount per customer. Sort by total amount descending.
- **Answer:**
  ```python
  from collections import defaultdict

  def summarize_transactions(transactions: list[dict]) -> list[dict]:
      totals = defaultdict(float)
      for t in transactions:
          totals[t["customer_id"]] += t["amount"]
      return sorted(
          [{"customer_id": k, "total": v} for k, v in totals.items()],
          key=lambda x: x["total"],
          reverse=True
      )
  ```

---

### Q32
- **Question:** Write a function to paginate a REST API with offset/limit and collect all results.
- **Answer:**
  ```python
  import requests

  def fetch_all_pages(base_url: str, page_size: int = 100) -> list[dict]:
      results = []
      offset = 0
      while True:
          resp = requests.get(base_url, params={"limit": page_size, "offset": offset})
          resp.raise_for_status()
          data = resp.json()
          results.extend(data.get("items", []))
          if len(data.get("items", [])) < page_size:
              break
          offset += page_size
      return results
  ```

---

### Q33
- **Question:** You need to call 3 external APIs and combine results. How do you do it efficiently?
- **Answer:**
  ```python
  import asyncio, aiohttp

  async def fetch_json(session: aiohttp.ClientSession, url: str) -> dict:
      async with session.get(url, timeout=aiohttp.ClientTimeout(total=10)) as resp:
          resp.raise_for_status()
          return await resp.json()

  async def get_combined_data(member_id: str) -> dict:
      urls = {
          "eligibility": f"https://eligibility-api/members/{member_id}",
          "claims": f"https://claims-api/members/{member_id}/claims",
          "benefits": f"https://benefits-api/members/{member_id}/benefits"
      }
      async with aiohttp.ClientSession() as session:
          results = await asyncio.gather(
              *[fetch_json(session, url) for url in urls.values()],
              return_exceptions=True
          )
      return {key: result for key, result in zip(urls.keys(), results)}
  ```

---

### Q34–Q60 (Condensed)

| # | Question | Tier |
|---|----------|------|
| 34 | Write a function to parse a large CSV file and validate each row against rules | T1 |
| 35 | Write a decorator that logs function name, args, return value, and duration | T1 |
| 36 | Write a class that implements a retry-able HTTP client with exponential backoff | T1 |
| 37 | Write a function to transform a flat list of records into a nested hierarchy by `parent_id` | T1 |
| 38 | Write a function to compare two JSON objects and return a diff | T1 |
| 39 | Write a script to read a SQL table and export it as a CSV with filtered columns | T1 |
| 40 | Write a function to process SFTP file uploads and move to processed/error folders | T1 |
| 41 | Write a function to merge N config files with deep dict merge semantics | T1 |
| 42 | Write a function that converts CamelCase dict keys to snake_case recursively | T1 |
| 43 | Write a function that deduplicates a list of dicts by composite key | T1 |
| 44 | Write a FastAPI endpoint that accepts a file upload and returns word count | T1 |
| 45 | Write a function that parses and normalizes date strings in multiple formats | T1 |
| 46 | Write a context manager that rolls back a list of actions on exception | T2 |
| 47 | Write a generator that lazily streams records from a database table in batches | T1 |
| 48 | Write a function to safely read a deeply nested JSON value with a fallback | T1 |
| 49 | Write a function to batch-call an API and handle partial failures | T1 |
| 50 | Write a function that validates a list of email addresses and returns invalid ones | T1 |
| 51 | Write a function to extract all unique domains from a list of email addresses | T1 |
| 52 | Write a function to compute the most common N items from a list | T1 |
| 53 | Write a function that implements a simple dependency resolver (topological sort) | T2 |
| 54 | Write a function to calculate SLA breach report from a list of ticket records | T1 |
| 55 | Write a function to synchronize records between two systems (upsert + delete) | T1 |
| 56 | Write a class that wraps `boto3` S3 operations with retry and logging | T1 |
| 57 | Write a function to transform HL7 message fields into a Python dict | T2 |
| 58 | Write a function to validate Pydantic models in bulk and collect all errors | T1 |
| 59 | Write a function to compute SLA metrics (avg, p95, p99 response time) | T1 |
| 60 | Write a Python script to poll an SQS queue and process messages in batches | T1 |

---

## SECTION 3: Database, API & Cloud (Q61–Q80)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 61 | How do you connect Python to PostgreSQL using SQLAlchemy? | SQL | T1 |
| 62 | What is the N+1 problem? How do you fix it in SQLAlchemy? | SQL | T1 |
| 63 | Write a query to find duplicate records and delete all but the most recent. | SQL | T1 |
| 64 | What is a CTE? Write one to compute running totals. | SQL | T1 |
| 65 | What is a window function? Write `ROW_NUMBER()` by partition. | SQL | T1 |
| 66 | How do you run Alembic migrations safely in production? | Database | T1 |
| 67 | How do you implement caching with Redis in a Python service? | Redis | T1 |
| 68 | What is the difference between Redis `SET` with EX vs `SETEX`? | Redis | T1 |
| 69 | How do you implement FastAPI authentication with JWT + dependency injection? | FastAPI | T1 |
| 70 | How do you validate and return structured errors from a FastAPI endpoint? | FastAPI | T1 |
| 71 | How do you handle file uploads > 500MB in FastAPI without loading into memory? | FastAPI | T1 |
| 72 | How do you implement idempotency keys for a POST endpoint? | API Design | T1 |
| 73 | How does `boto3` work? How do you read from S3 and write to DynamoDB? | AWS | T1 |
| 74 | How do you write and deploy a Python AWS Lambda function? | AWS | T1 |
| 75 | How do you process SQS events in Lambda with partial batch failure handling? | AWS | T1 |
| 76 | How do you use Azure Blob Storage SDK in Python? | Azure | T1 |
| 77 | How does `DefaultAzureCredential` work for authentication in Python? | Azure | T1 |
| 78 | How do you write a Python Docker container that follows security best practices? | Docker | T1 |
| 79 | How do you write a Kubernetes deployment manifest for a Python FastAPI service? | Kubernetes | T1 |
| 80 | How do you implement CI/CD for a Python service using GitHub Actions? | DevOps | T1 |

---

## SECTION 4: Service Company Scenario Questions (Q81–Q100)

| # | Question | Focus | Tier |
|---|----------|-------|------|
| 81 | A client's batch job that processes 5M records overnight is running 3x slower than before. How do you investigate? | Performance | T1 |
| 82 | You are given a legacy Python 2 codebase to migrate to Python 3.11. How do you approach this? | Migration | T1 |
| 83 | A client wants to add ML to their claims system. What questions do you ask before starting? | Consulting | T1 |
| 84 | Your FastAPI service is failing with 429 errors from a third-party API. How do you handle this? | Resilience | T1 |
| 85 | A client asks you to integrate their system with an AI chatbot. What would you design? | GenAI | T1 |
| 86 | How do you handle secrets rotation in a live Python service without downtime? | Security | T1 |
| 87 | How do you estimate effort for a Python ETL project for a client with no existing data catalog? | Consulting | T1 |
| 88 | A client's S3-triggered Lambda is occasionally dropping records. How do you diagnose? | Debugging | T1 |
| 89 | You need to deliver a FastAPI service to a client in 2 weeks. How do you prioritize? | Delivery | T1 |
| 90 | How do you handle a client who keeps changing requirements mid-sprint? | Consulting | T1 |
| 91 | How do you document a Python service you built for a client to maintain post-handoff? | Documentation | T1 |
| 92 | How do you ensure code quality on a Python project with 4 developers of varying skill? | Leadership | T1 |
| 93 | How do you test a Python service that depends on real-time data from a stock exchange? | Testing | T1 |
| 94 | A client wants to process 100K insurance claims per hour. Design the architecture. | System Design | T1 |
| 95 | How do you implement role-based access control for a client's multi-tenant Python app? | Security | T1 |
| 96 | How do you implement a Python-based ETL pipeline that is auditable and rerunnable? | Architecture | T1 |
| 97 | A client's API returns different schemas in different environments. How do you handle this? | Integration | T1 |
| 98 | How do you design a Python microservice that must integrate with 5 different client legacy systems? | Architecture | T1 |
| 99 | How would you add GenAI capabilities (chatbot, document search) to an existing Python monolith? | GenAI | T1 |
| 100 | You are the tech lead for a 6-month client engagement building a Python data platform. Walk through your first 30 days and technical approach. | Leadership | T1 |
