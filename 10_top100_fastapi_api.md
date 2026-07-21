# Top 100 FastAPI / API Development Interview Questions
> Focus: 5–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise, GenAI
> Sources: Glassdoor, LinkedIn, Reddit, Medium, GeeksforGeeks

---

## SECTION 1: FastAPI Fundamentals (Q1–Q25)

### Q1
- **Question:** Why would you choose FastAPI over Flask or Django REST Framework?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Async-first: built on `asyncio`; handles high concurrency without extra threads
  - Automatic OpenAPI docs (Swagger + ReDoc) from type annotations
  - Pydantic v2 validation — fast, type-safe, automatic error responses (422)
  - Dependency injection built-in
  - 2–3x faster than Flask for I/O-bound workloads (uvicorn/uvloop)
  - Type hints drive everything: request parsing, response serialization, docs
- **When Flask:** simpler apps, existing Flask codebase, need synchronous-only
- **When Django:** full-stack with ORM, admin panel, batteries-included
- **Difficulty:** Easy

---

### Q2
- **Question:** How does FastAPI use Python type hints for request validation?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi import FastAPI, Query, Path
  from pydantic import BaseModel

  app = FastAPI()

  class CreateUserRequest(BaseModel):
      name: str
      email: str
      age: int

  @app.post("/users/{org_id}")
  async def create_user(
      org_id: int = Path(..., gt=0),           # path param, validated
      limit: int = Query(default=10, le=100),  # query param with constraint
      body: CreateUserRequest = None           # request body from Pydantic
  ):
      ...
  ```
- **Follow-Ups:** What does FastAPI return if validation fails? (422 with detailed error)
- **Difficulty:** Easy

---

### Q3
- **Question:** How does FastAPI's dependency injection (`Depends`) work? Implement a DB session dependency.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi import Depends
  from sqlalchemy.orm import Session

  def get_db():
      db = SessionLocal()
      try:
          yield db
      except Exception:
          db.rollback()
          raise
      finally:
          db.close()

  @app.get("/users/{user_id}")
  async def get_user(user_id: int, db: Session = Depends(get_db)):
      return db.query(User).filter(User.id == user_id).first()
  ```
- **Follow-Ups:** How do you override dependencies in tests? (`app.dependency_overrides`)
- **Difficulty:** Medium

---

### Q4
- **Question:** How do you implement JWT authentication in FastAPI?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi.security import OAuth2PasswordBearer
  from jose import JWTError, jwt

  oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/auth/token")

  async def get_current_user(token: str = Depends(oauth2_scheme)):
      try:
          payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
          user_id = payload.get("sub")
          if not user_id:
              raise HTTPException(401, "Invalid token")
          return await user_service.get_by_id(user_id)
      except JWTError:
          raise HTTPException(401, "Could not validate credentials")

  @app.get("/me")
  async def get_me(user = Depends(get_current_user)):
      return user
  ```
- **Follow-Ups:** How do you implement token refresh? How do you handle token revocation?
- **Difficulty:** Medium

---

### Q5
- **Question:** How do you implement middleware in FastAPI? Write a timing middleware.
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import time
  from fastapi import Request

  @app.middleware("http")
  async def timing_middleware(request: Request, call_next):
      start = time.perf_counter()
      response = await call_next(request)
      elapsed = time.perf_counter() - start
      response.headers["X-Process-Time"] = f"{elapsed:.4f}"
      logger.info(f"{request.method} {request.url.path} {response.status_code} {elapsed:.4f}s")
      return response
  ```
- **Follow-Ups:** What is the difference between FastAPI middleware and Starlette middleware?
- **Difficulty:** Medium

---

### Q6
- **Question:** How do you implement RBAC (Role-Based Access Control) in FastAPI?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from enum import Enum
  from fastapi import HTTPException

  class Role(str, Enum):
      ADMIN = "admin"
      VIEWER = "viewer"

  def require_role(*roles: Role):
      def dependency(current_user = Depends(get_current_user)):
          if current_user.role not in roles:
              raise HTTPException(403, "Insufficient permissions")
          return current_user
      return Depends(dependency)

  @app.delete("/users/{id}")
  async def delete_user(
      id: int,
      user = require_role(Role.ADMIN)
  ):
      ...
  ```
- **Difficulty:** Medium

---

### Q7
- **Question:** How do you implement rate limiting in FastAPI?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import redis.asyncio as redis
  from fastapi import Request, HTTPException

  redis_client = redis.Redis(...)

  async def rate_limit(request: Request, max_calls: int = 100, window: int = 60):
      key = f"rate:{request.client.host}:{request.url.path}"
      current = await redis_client.incr(key)
      if current == 1:
          await redis_client.expire(key, window)
      if current > max_calls:
          raise HTTPException(429, "Rate limit exceeded")

  @app.get("/api/data")
  async def get_data(_=Depends(rate_limit)):
      ...
  ```
- **Difficulty:** Medium

---

### Q8
- **Question:** How do you implement streaming responses in FastAPI for LLM output?
- **Level:** 8–12 Yrs | **Company:** GenAI companies | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi.responses import StreamingResponse

  async def generate_stream(prompt: str):
      async for chunk in openai_client.chat.completions.create(
          model="gpt-4o",
          messages=[{"role": "user", "content": prompt}],
          stream=True
      ):
          content = chunk.choices[0].delta.content or ""
          if content:
              yield f"data: {json.dumps({'text': content})}\n\n"
      yield "data: [DONE]\n\n"

  @app.post("/chat/stream")
  async def chat_stream(request: ChatRequest):
      return StreamingResponse(
          generate_stream(request.prompt),
          media_type="text/event-stream",
          headers={"Cache-Control": "no-cache"}
      )
  ```
- **Difficulty:** Medium

---

### Q9
- **Question:** How do you handle background tasks in FastAPI?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi import BackgroundTasks

  async def send_email(to: str, subject: str, body: str):
      # async email sending
      await email_client.send(to, subject, body)

  @app.post("/users/register")
  async def register(data: RegisterRequest, background_tasks: BackgroundTasks):
      user = await create_user(data)
      background_tasks.add_task(send_email, user.email, "Welcome!", "...")
      return {"message": "Registered"}
  ```
- **Follow-Ups:** What are the limitations of `BackgroundTasks`? When should you use Celery instead?
- **Difficulty:** Easy

---

### Q10
- **Question:** How do you implement API versioning in FastAPI?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi import APIRouter

  v1_router = APIRouter(prefix="/api/v1")
  v2_router = APIRouter(prefix="/api/v2")

  @v1_router.get("/users")
  async def get_users_v1(): ...

  @v2_router.get("/users")
  async def get_users_v2(): ...  # enhanced version

  app.include_router(v1_router)
  app.include_router(v2_router)
  ```
- **Follow-Ups:** Header-based versioning vs URL-based? What about deprecation strategy?
- **Difficulty:** Medium

---

### Q11
- **Question:** How do you implement pagination in a FastAPI endpoint?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel
  from typing import Generic, TypeVar

  T = TypeVar("T")

  class Page(BaseModel, Generic[T]):
      items: list[T]
      total: int
      page: int
      size: int
      pages: int

  @app.get("/users", response_model=Page[UserResponse])
  async def list_users(
      page: int = Query(1, ge=1),
      size: int = Query(20, le=100),
      db: Session = Depends(get_db)
  ):
      offset = (page - 1) * size
      total = db.query(User).count()
      items = db.query(User).offset(offset).limit(size).all()
      return Page(items=items, total=total, page=page, size=size, pages=ceil(total/size))
  ```
- **Difficulty:** Medium

---

### Q12
- **Question:** How does Pydantic v2 differ from v1? What are the migration challenges?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - v2 uses Rust core (`pydantic-core`) — 5–50x faster validation
  - `@validator` → `@field_validator` (class method, different signature)
  - `@root_validator` → `@model_validator(mode='before'/'after')`
  - `Config` inner class → `model_config = ConfigDict(...)`
  - `dict()` → `model_dump()`; `json()` → `model_dump_json()`
  - Breaking: strict mode by default for some coercions; `validate_assignment` behavior changed
- **Difficulty:** Medium

---

### Q13
- **Question:** How do you test a FastAPI endpoint with dependencies mocked?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from fastapi.testclient import TestClient

  def override_get_db():
      yield test_db  # use test database

  def override_get_current_user():
      return User(id=1, role="admin")  # mock user

  app.dependency_overrides[get_db] = override_get_db
  app.dependency_overrides[get_current_user] = override_get_current_user

  client = TestClient(app)

  def test_get_user():
      response = client.get("/users/1")
      assert response.status_code == 200
  ```
- **Difficulty:** Medium

---

### Q14–Q25 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 14 | How do you handle file uploads in FastAPI? Stream large files without loading into memory? | 5–8 Yrs | High | T1 |
| 15 | How do you configure CORS in FastAPI? | 5–8 Yrs | High | T1 |
| 16 | How do you implement request body validation with Pydantic v2 `model_validator`? | 8–12 Yrs | High | T1 |
| 17 | How do you return different response schemas based on conditions? | 8–12 Yrs | Medium | T2 |
| 18 | How do you implement WebSocket support in FastAPI? | 8–12 Yrs | Medium | T2 |
| 19 | How do you configure OpenAPI documentation in FastAPI? | 5–8 Yrs | Medium | T2 |
| 20 | How does FastAPI handle async vs sync route handlers differently? | 8–12 Yrs | High | T1 |
| 21 | How do you implement a FastAPI app with lifespan events (startup/shutdown)? | 8–12 Yrs | High | T1 |
| 22 | How do you implement a global exception handler for unhandled errors? | 8–12 Yrs | High | T1 |
| 23 | How do you add custom response headers to every FastAPI response? | 5–8 Yrs | Medium | T2 |
| 24 | How do you implement request timeout middleware in FastAPI? | 8–12 Yrs | High | T1 |
| 25 | How do you structure a large FastAPI application into multiple routers? | 8–12 Yrs | High | T1 |

---

## SECTION 2: REST API Design (Q26–Q50)

### Q26
- **Question:** What are REST API best practices? Design a REST API for a healthcare claims system.
- **Level:** 8–12 Yrs | **Company:** Healthcare, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Nouns for resources: `/claims`, `/members`, `/providers` — not `/getClaim`
  - HTTP methods: GET (read), POST (create), PUT/PATCH (update), DELETE
  - Status codes: 200 (OK), 201 (Created), 204 (No Content), 400 (Bad Request), 401, 403, 404, 409 (Conflict), 422, 500
  - Versioning: `/api/v1/claims`
  - Pagination: `?page=1&size=20` or cursor-based
  - Filtering: `?status=pending&from_date=2024-01-01`
  - Idempotency keys for POST operations in claims
- **Difficulty:** Medium

---

### Q27
- **Question:** What is idempotency? How do you implement idempotency keys in a REST API?
- **Level:** 8–12 Yrs | **Company:** Healthcare, Finance, Insurance | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  @app.post("/claims")
  async def submit_claim(
      claim: ClaimRequest,
      idempotency_key: str = Header(..., alias="Idempotency-Key"),
      db: Session = Depends(get_db),
      redis: Redis = Depends(get_redis)
  ):
      # Check if already processed
      cached = await redis.get(f"idem:{idempotency_key}")
      if cached:
          return json.loads(cached)  # Return same response

      result = await process_claim(claim, db)

      # Cache result for 24 hours
      await redis.setex(f"idem:{idempotency_key}", 86400, json.dumps(result))
      return result
  ```
- **Difficulty:** Hard

---

### Q28
- **Question:** What is the difference between PUT and PATCH? When do you use each?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - PUT: replace entire resource; must send full representation; idempotent
  - PATCH: partial update; only send changed fields; use `Optional` fields in Pydantic
  - FastAPI PATCH pattern: `model.copy(update=patch_data.dict(exclude_unset=True))`
- **Difficulty:** Easy

---

### Q29–Q50 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 29 | How do you design an API for bulk operations? What are the tradeoffs? | 8–12 Yrs | High | T1 |
| 30 | How do you handle long-running operations in a REST API? (async job pattern) | 8–12 Yrs | High | T1 |
| 31 | What is cursor-based pagination? How does it differ from offset pagination? | 8–12 Yrs | Medium | T2 |
| 32 | How do you implement API key authentication alongside JWT? | 8–12 Yrs | Medium | T2 |
| 33 | How do you implement request signing for API security? | 12–15 Yrs | Medium | T2 |
| 34 | What is HATEOAS? Do you implement it in practice? | 12–15 Yrs | Low | T3 |
| 35 | How do you document API deprecation and sunset headers? | 8–12 Yrs | Medium | T2 |
| 36 | How do you handle multipart form data in a REST API? | 5–8 Yrs | Medium | T2 |
| 37 | How do you implement conditional requests (ETag, If-None-Match)? | 8–12 Yrs | Low | T3 |
| 38 | How do you implement webhook callbacks from your API? | 8–12 Yrs | High | T1 |
| 39 | How do you handle timezone and locale in API responses? | 8–12 Yrs | High | T1 |
| 40 | What are the security headers every REST API should set? | 8–12 Yrs | High | T1 |
| 41 | How do you implement API throttling with user-level and global limits? | 8–12 Yrs | High | T1 |
| 42 | How do you design a consistent error response format? | 5–8 Yrs | High | T1 |
| 43 | How do you handle API schema evolution without breaking existing clients? | 12–15 Yrs | High | T1 |
| 44 | How do you implement a health check endpoint that returns meaningful status? | 5–8 Yrs | High | T1 |
| 45 | What is an API gateway? How does it differ from a FastAPI middleware? | 12–15 Yrs | High | T1 |
| 46 | How do you implement multi-tenant API isolation at the route level? | 12–15 Yrs | Medium | T2 |
| 47 | How do you implement request tracing from API gateway to service to DB? | 8–12 Yrs | High | T1 |
| 48 | How do you prevent SQL injection and XSS in a FastAPI application? | 5–8 Yrs | High | T1 |
| 49 | How do you implement server-sent events (SSE) in FastAPI? | 8–12 Yrs | Medium | T2 |
| 50 | How do you implement a circuit breaker for downstream API calls in FastAPI? | 8–12 Yrs | High | T1 |

---

## SECTION 3: Pydantic Deep Dive (Q51–Q70)

### Q51
- **Question:** How do you implement a Pydantic v2 model with custom validation?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel, field_validator, model_validator
  from typing import Optional

  class ClaimModel(BaseModel):
      claim_id: str
      amount: float
      diagnosis_codes: list[str]
      status: str = "pending"

      @field_validator('amount')
      @classmethod
      def amount_must_be_positive(cls, v):
          if v <= 0:
              raise ValueError('Amount must be positive')
          return round(v, 2)

      @field_validator('diagnosis_codes')
      @classmethod
      def codes_not_empty(cls, v):
          if not v:
              raise ValueError('At least one diagnosis code required')
          return [code.upper() for code in v]

      @model_validator(mode='after')
      def check_high_amount_has_authorization(self):
          if self.amount > 10000 and 'AUTH' not in self.claim_id:
              raise ValueError('High-value claims require authorization')
          return self
  ```
- **Difficulty:** Medium

---

### Q52
- **Question:** How do you use Pydantic's `model_config` for strict mode, alias, and serialization?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel, ConfigDict, Field

  class UserResponse(BaseModel):
      model_config = ConfigDict(
          populate_by_name=True,
          str_strip_whitespace=True,
          strict=False
      )

      user_id: int = Field(alias="userId")
      full_name: str = Field(alias="fullName")
      email_address: str = Field(alias="emailAddress")
  ```
- **Difficulty:** Medium

---

### Q53–Q70 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 53 | How do you handle optional vs required fields in Pydantic v2? | 5–8 Yrs | High | T1 |
| 54 | How do you use `Pydantic Settings` for environment-based configuration? | 5–8 Yrs | High | T1 |
| 55 | How do you parse LLM JSON output into a Pydantic model with error handling? | 8–12 Yrs | High | T1 |
| 56 | How does Pydantic `TypeAdapter` work? When do you use it? | 8–12 Yrs | Medium | T2 |
| 57 | How do you serialize a Pydantic model to JSON with custom serializers? | 5–8 Yrs | High | T1 |
| 58 | How do you implement a discriminated union in Pydantic v2? | 8–12 Yrs | Medium | T2 |
| 59 | How do you use `Annotated` with Pydantic for field constraints? | 8–12 Yrs | High | T1 |
| 60 | How do you implement recursive Pydantic models (self-referencing)? | 8–12 Yrs | Medium | T2 |
| 61 | How do you handle datetime serialization/deserialization in Pydantic? | 5–8 Yrs | High | T1 |
| 62 | How do you validate a Pydantic model from a raw dict with extra fields? | 5–8 Yrs | Medium | T2 |
| 63 | How does Pydantic v2's `model_rebuild()` work? | 8–12 Yrs | Low | T3 |
| 64 | How do you use Pydantic to validate YAML config at startup? | 5–8 Yrs | Medium | T2 |
| 65 | How do you extend a Pydantic model with additional fields for API responses? | 5–8 Yrs | High | T1 |
| 66 | How does `model_dump(exclude_unset=True)` work? Why is it important for PATCH? | 8–12 Yrs | High | T1 |
| 67 | What is `model_validate_json()` vs `model_validate()`? | 8–12 Yrs | Medium | T2 |
| 68 | How do you implement custom JSON schema generation with Pydantic? | 8–12 Yrs | Low | T3 |
| 69 | How do you use Pydantic for validating Kafka message schemas? | 8–12 Yrs | High | T1 |
| 70 | How do you mock Pydantic validation in unit tests? | 5–8 Yrs | Medium | T2 |

---

## SECTION 4: API Security & Production (Q71–Q100)

### Q71
- **Question:** What are the OWASP top threats for REST APIs? How do you mitigate them in Python?
- **Level:** 8–12 Yrs | **Company:** Healthcare, Finance | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Broken Object Level Authorization: always filter by authenticated user's scope
  - Injection: use parameterized queries (never string concat SQL); Pydantic validation
  - Broken Authentication: short-lived JWTs; token rotation; secure storage
  - Excessive Data Exposure: explicit `response_model` in FastAPI
  - Mass Assignment: use `model_dump(exclude_unset=True)` and allowlist fields
  - Rate limiting: prevent brute force and DDoS
- **Difficulty:** Hard

---

### Q72
- **Question:** How do you handle secrets in a Python FastAPI service? AWS Secrets Manager / Azure Key Vault.
- **Level:** 8–12 Yrs | **Company:** Enterprise, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import boto3
  from pydantic_settings import BaseSettings

  class Settings(BaseSettings):
      db_url: str = ""
      jwt_secret: str = ""

      def load_from_secrets_manager(self):
          client = boto3.client('secretsmanager')
          secret = json.loads(client.get_secret_value(SecretId='prod/api/secrets')['SecretString'])
          self.db_url = secret['db_url']
          self.jwt_secret = secret['jwt_secret']

  settings = Settings()
  settings.load_from_secrets_manager()
  ```
- **Difficulty:** Medium

---

### Q73–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 73 | How do you implement OAuth2 with authorization code flow in FastAPI? | 8–12 Yrs | Medium | T2 |
| 74 | How do you handle CORS for a FastAPI service accessed from multiple frontends? | 5–8 Yrs | High | T1 |
| 75 | How do you implement service-to-service authentication (mTLS, API keys, JWT)? | 12–15 Yrs | Medium | T2 |
| 76 | How do you implement API response caching with cache-control headers? | 8–12 Yrs | Medium | T2 |
| 77 | How do you deploy FastAPI with Gunicorn + Uvicorn workers? How many workers? | 8–12 Yrs | High | T1 |
| 78 | How do you implement graceful shutdown for a FastAPI service in Kubernetes? | 8–12 Yrs | High | T1 |
| 79 | How do you configure connection pooling for a FastAPI + PostgreSQL service? | 8–12 Yrs | High | T1 |
| 80 | How do you implement health and readiness probes for FastAPI in Kubernetes? | 8–12 Yrs | High | T1 |
| 81 | How do you handle request body size limits in FastAPI? | 5–8 Yrs | Medium | T2 |
| 82 | How do you implement compression (gzip) for FastAPI responses? | 5–8 Yrs | Medium | T2 |
| 83 | How do you implement request deduplication for non-idempotent POST requests? | 8–12 Yrs | High | T1 |
| 84 | How do you implement request validation for API keys? | 5–8 Yrs | High | T1 |
| 85 | How do you implement a multi-tenant FastAPI app where each tenant has its own DB? | 12–15 Yrs | High | T1 |
| 86 | How do you handle long-running AI tasks with a job status polling endpoint? | 8–12 Yrs | High | T1 |
| 87 | How do you implement a feature flag system in FastAPI? | 8–12 Yrs | High | T1 |
| 88 | How do you track API usage per client for billing? | 12–15 Yrs | Medium | T2 |
| 89 | How do you implement soft delete in a FastAPI CRUD API? | 5–8 Yrs | Medium | T2 |
| 90 | How do you implement optimistic locking in a FastAPI update endpoint? | 8–12 Yrs | Medium | T2 |
| 91 | How do you return different HTTP responses for async vs sync FastAPI endpoints? | 8–12 Yrs | Medium | T2 |
| 92 | How do you implement event-driven response notifications for long jobs? | 12–15 Yrs | Medium | T2 |
| 93 | How do you implement a FastAPI service that proxies requests to another service? | 8–12 Yrs | Medium | T2 |
| 94 | How do you version Pydantic models between API v1 and v2 without duplication? | 8–12 Yrs | Medium | T2 |
| 95 | How do you handle concurrent updates to the same resource (race conditions in REST)? | 8–12 Yrs | High | T1 |
| 96 | How do you add OpenTelemetry tracing to every FastAPI endpoint automatically? | 8–12 Yrs | High | T1 |
| 97 | How do you implement a GraphQL API alongside REST in Python? | 12–15 Yrs | Low | T3 |
| 98 | How do you handle large response payloads in FastAPI efficiently? | 8–12 Yrs | Medium | T2 |
| 99 | How do you implement async database migrations with Alembic in a FastAPI app? | 8–12 Yrs | Medium | T2 |
| 100 | Design a production-grade FastAPI service for a healthcare claims intake API with auth, rate limiting, idempotency, streaming, observability, and HA. | 12–15 Yrs | High | T1 |
