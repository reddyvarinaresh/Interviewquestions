# System Design & Architecture Round — Complete Prep Guide
> Companies: Deloitte, Accenture, Optum, Carelon, EPAM, Cognizant, Wipro, TCS, Infosys, HCL, Capgemini, Brillio, Persistent, LTIMindtree
> Level: 8–15 Years | Senior Engineer / Tech Lead / Architect / Principal Engineer
> Sources: LinkedIn posts, Glassdoor interview reports, Reddit r/cscareerquestions, Blind, candidate feedback

---

## HOW THE SYSTEM DESIGN ROUND WORKS AT THESE COMPANIES

| Company | Round Style | Duration | What They Expect |
|---------|-------------|----------|-----------------|
| Deloitte / Accenture / EY | Whiteboard or Miro | 45–60 min | Business problem → scalable solution, cloud-aware |
| Optum / Carelon / UHG | Case-study based | 60 min | Healthcare domain + HIPAA + scale |
| EPAM / Brillio / Persistent | Technical deep-dive | 45 min | Clean architecture, microservices, trade-offs |
| TCS / Wipro / Infosys / HCL | High-level design | 30–45 min | Standard patterns, less deep |
| Cognizant / Capgemini | Solution architecture | 45 min | Cloud + integration patterns |

**The framework every interviewer wants you to use:**
```
1. Clarify requirements (functional + non-functional) — 5 min
2. Estimate scale (users, QPS, data volume, storage) — 3 min
3. High-level design (components, data flow) — 10 min
4. Deep dive into critical components — 20 min
5. Handle bottlenecks, failures, scale — 10 min
6. Trade-offs and alternatives — 5 min
```

---

## PART 1: MOST FREQUENTLY ASKED DESIGN QUESTIONS

### FREQUENCY RANKING (from LinkedIn + Glassdoor + Blind reports)

| Rank | Question | Companies Asking |
|------|----------|-----------------|
| 🔴1 | Design a URL shortener | All companies — most common warm-up |
| 🔴2 | Design a notification system | TCS, Wipro, Cognizant, EPAM |
| 🔴3 | Design an API rate limiter | Deloitte, Accenture, EPAM, Brillio |
| 🔴4 | Design a cache system (Redis-based) | All companies |
| 🔴5 | Design a claims processing system | Optum, Carelon, UHG, Cigna |
| 🔴6 | Design a real-time messaging/chat system | TCS, Wipro, Infosys, Capgemini |
| 🔴7 | Design a file upload/storage system | EPAM, Persistent, Brillio |
| 🔴8 | Design a search autocomplete system | Accenture, Deloitte, EPAM |
| 🔴9 | Design an event-driven order processing system | All service companies |
| 🔴10 | Design a RAG-based document Q&A system | Deloitte AI, Accenture GenAI, Carelon, Optum |
| 🟡11 | Design a real-time leaderboard | TCS, Wipro, Cognizant |
| 🟡12 | Design a distributed job scheduler | EPAM, Persistent, Brillio |
| 🟡13 | Design a healthcare member portal (HIPAA) | Optum, Carelon, Elevance |
| 🟡14 | Design a prior authorization AI system | Optum, Carelon, Cigna |
| 🟡15 | Design a multi-tenant SaaS platform | Deloitte, Accenture, EPAM |
| 🟡16 | Design a data pipeline for ML features | All AI/DS roles |
| 🟡17 | Design an audit logging system | Healthcare + Finance |
| 🟡18 | Design a distributed payment system | Capgemini, Wipro, Infosys |
| 🟡19 | Design a microservices authentication system | All companies |
| 🟡20 | Design a monitoring/observability platform | EPAM, Brillio, Persistent |

---

## PART 2: COMPLETE DESIGN WALKTHROUGHS

---

## DESIGN 1: URL Shortener (Most Common — Asked Everywhere)

### Step 1: Clarify Requirements
```
Functional:
- Given a long URL, return a short URL (e.g., short.ly/abc123)
- Short URL redirects to original URL
- Optional: custom alias, expiry date, analytics

Non-functional:
- 100M URLs created/day, 10B redirects/day
- Read-heavy (100:1 read/write ratio)
- P99 redirect latency < 10ms
- 99.99% availability
- URLs must be unique and not guessable
```

### Step 2: Estimate Scale
```
Writes: 100M/day = ~1,160 writes/sec
Reads:  10B/day  = ~115,700 reads/sec (100:1)
Storage: 100M URLs/day × 500 bytes = 50GB/day → 18TB/year
Short code: 7 chars (base62) = 62^7 = 3.5 trillion unique URLs
```

### Step 3: High-Level Design
```
Client
  │
  ▼
[Load Balancer] (nginx / AWS ALB)
  │
  ├──► [Write Service] ──► [PostgreSQL] (URL mapping, created_at, expires_at)
  │         │
  │    [ID Generator] (Snowflake / distributed counter)
  │         │
  │    [Redis Cache] (short_code → long_url, TTL match expiry)
  │
  └──► [Redirect Service] ──► [Redis Cache] ──► [PostgreSQL] (fallback)
             │
        [Analytics Service] ──► [Kafka] ──► [ClickHouse] (click stats)
```

### Step 4: Deep Dive — Short Code Generation
```python
import hashlib, base64

# Option 1: MD5 hash + take first 7 chars of base62
def generate_short_code(long_url: str, salt: str = "") -> str:
    digest = hashlib.md5((long_url + salt).encode()).digest()
    encoded = base64.urlsafe_b64encode(digest).decode()
    return encoded[:7]

# Option 2: Snowflake ID → base62 encode (preferred — no collision)
BASE62 = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"

def to_base62(n: int) -> str:
    if n == 0: return BASE62[0]
    result = []
    while n:
        result.append(BASE62[n % 62])
        n //= 62
    return "".join(reversed(result))
```

### Step 5: Redis Caching Strategy
```python
import redis
from datetime import timedelta

r = redis.Redis()

def get_long_url(short_code: str) -> str | None:
    # Cache-aside pattern
    cached = r.get(f"url:{short_code}")
    if cached:
        return cached.decode()
    # Cache miss — hit DB
    record = db.query("SELECT long_url, expires_at FROM urls WHERE short_code = %s", short_code)
    if not record:
        return None
    long_url, expires_at = record
    ttl = max(int((expires_at - datetime.utcnow()).total_seconds()), 3600)
    r.setex(f"url:{short_code}", ttl, long_url)
    return long_url
```

### Step 6: Handle Scale & Failures
- **Read scale:** Redis cluster handles 115K reads/sec; add read replicas for DB
- **Write scale:** Single PostgreSQL can handle 1,160 writes/sec; shard by short_code prefix if needed
- **Cache eviction:** LRU; hot URLs stay in cache
- **Collision handling:** check DB before inserting; if collision, retry with different salt
- **Expiry cleanup:** background Celery beat job deletes expired URLs daily

### Trade-offs to Mention
| Approach | Pros | Cons |
|----------|------|------|
| Hash-based | Simple | Collision risk, must check DB |
| Counter-based (Snowflake) | No collision | Needs distributed counter |
| Base62 encode | Short codes | Predictable sequence |
| Base58 | No ambiguous chars (0/O/l/I) | Slightly longer |

---

## DESIGN 2: API Rate Limiter

### Requirements
```
- Limit each user to N requests per time window
- Return HTTP 429 Too Many Requests when exceeded
- Support: per-user, per-IP, per-API-key, per-endpoint limits
- Distributed (multiple API gateway nodes must share state)
- P99 latency overhead < 2ms
```

### Algorithms Comparison
| Algorithm | How It Works | Pros | Cons |
|-----------|-------------|------|------|
| Fixed Window Counter | Count reqs per window | Simple | Burst at window boundary |
| Sliding Window Log | Store each req timestamp | Accurate | High memory |
| Sliding Window Counter | Weighted blend of two windows | Accurate, low memory | Slightly approximate |
| Token Bucket | Add tokens at rate R, consume 1 per req | Allows bursts | Complex |
| Leaky Bucket | Queue requests, process at fixed rate | Smooth output | Drops bursts |

### Implementation — Sliding Window (Redis)
```python
import redis, time

r = redis.Redis()

def is_allowed(user_id: str, limit: int = 100, window_sec: int = 60) -> bool:
    now = time.time()
    window_start = now - window_sec
    key = f"rate:{user_id}"

    pipe = r.pipeline()
    pipe.zremrangebyscore(key, 0, window_start)      # remove old entries
    pipe.zadd(key, {str(now): now})                   # add current request
    pipe.zcard(key)                                   # count in window
    pipe.expire(key, window_sec)
    results = pipe.execute()

    count = results[2]
    return count <= limit

# FastAPI middleware
from fastapi import Request, HTTPException

async def rate_limit_middleware(request: Request, call_next):
    user_id = request.headers.get("X-API-Key", request.client.host)
    if not is_allowed(user_id, limit=100, window_sec=60):
        raise HTTPException(status_code=429, detail="Rate limit exceeded")
    return await call_next(request)
```

### Token Bucket — For Burst-Friendly APIs
```python
import time, redis

r = redis.Redis()

def token_bucket_allow(user_id: str, rate: float = 10,
                        capacity: float = 50) -> bool:
    key = f"bucket:{user_id}"
    now = time.time()

    # Lua script for atomicity
    lua = """
    local key = KEYS[1]
    local now = tonumber(ARGV[1])
    local rate = tonumber(ARGV[2])
    local capacity = tonumber(ARGV[3])

    local data = redis.call("HMGET", key, "tokens", "last_refill")
    local tokens = tonumber(data[1]) or capacity
    local last = tonumber(data[2]) or now

    tokens = math.min(capacity, tokens + (now - last) * rate)
    if tokens >= 1 then
        tokens = tokens - 1
        redis.call("HMSET", key, "tokens", tokens, "last_refill", now)
        redis.call("EXPIRE", key, 3600)
        return 1
    end
    return 0
    """
    script = r.register_script(lua)
    return bool(script(keys=[key], args=[now, rate, capacity]))
```

### Architecture
```
Client ──► [API Gateway / Nginx]
                │
         [Rate Limiter Middleware]
                │
         [Redis Cluster] (shared state across all gateway nodes)
                │
         Allow / Deny (429)
                │
         [Backend Services]
```

---

## DESIGN 3: Real-Time Notification System

### Requirements
```
Functional: Send notifications via email, SMS, push, in-app
- 1M notifications/day, burst to 100K/min during events
- Templates with personalization
- Retry on failure, DLQ for permanent failures
- Delivery tracking (sent/delivered/failed/opened)

Non-functional:
- Email SLA: < 30 sec, SMS < 10 sec, Push < 5 sec
- 99.9% delivery guarantee
```

### Architecture
```
[Event Sources] (claims system, eligibility, alerts)
       │
       ▼
   [Kafka Topics]
   notifications.email
   notifications.sms
   notifications.push
       │
       ▼
[Notification Dispatcher] (Python service)
   - Reads from Kafka
   - Resolves template + user preferences
   - Routes to channel workers
       │
   ┌───┼───┐
   ▼   ▼   ▼
[Email  [SMS    [Push
Worker] Worker] Worker]
(SES)  (Twilio) (FCM/APNs)
   │
   ▼
[PostgreSQL] — delivery status, audit log
[Redis]      — deduplication (event_id, TTL 24h)
[Kafka DLQ]  — failed after 3 retries
```

### Python Implementation — Dispatcher
```python
from aiokafka import AIOKafkaConsumer
from pydantic import BaseModel
from enum import Enum
import asyncio, json, redis

class Channel(str, Enum):
    EMAIL = "email"
    SMS   = "sms"
    PUSH  = "push"

class NotificationEvent(BaseModel):
    event_id: str
    user_id: str
    channel: Channel
    template_id: str
    variables: dict
    priority: int = 5

r = redis.Redis()

async def is_duplicate(event_id: str) -> bool:
    return not r.set(f"notif:dedup:{event_id}", 1, nx=True, ex=86400)

async def dispatch(event: NotificationEvent):
    if await is_duplicate(event.event_id):
        return  # already processed

    template = await load_template(event.template_id)
    content = template.render(**event.variables)

    match event.channel:
        case Channel.EMAIL: await send_email(event.user_id, content)
        case Channel.SMS:   await send_sms(event.user_id, content)
        case Channel.PUSH:  await send_push(event.user_id, content)

    await record_delivery(event.event_id, "sent")

async def consume():
    consumer = AIOKafkaConsumer(
        "notifications.*",
        bootstrap_servers="kafka:9092",
        group_id="notification-dispatcher",
        value_deserializer=lambda v: json.loads(v)
    )
    await consumer.start()
    async for msg in consumer:
        try:
            event = NotificationEvent(**msg.value)
            await dispatch(event)
        except Exception as e:
            await send_to_dlq(msg.value, str(e))
```

---

## DESIGN 4: Claims Processing System (Optum / Carelon / UHG — VERY Frequently Asked)

### Requirements
```
Functional:
- Ingest claims from providers (EDI 837, API, portal)
- Validate → Adjudicate → Pay → EOB generation
- 500K claims/day, peak 10K/hr
- Statuses: received, validated, adjudicated, paid, denied, appealed
- Full audit trail (HIPAA)

Non-functional:
- End-to-end SLA: < 30 days (regulatory), auto-adj < 24hrs
- 99.99% uptime
- HIPAA compliance: PHI encryption, audit log, access control
- Idempotent: same claim submitted twice → same outcome
```

### Architecture
```
[Provider] ──EDI 837──► [Ingestion Service]
[Provider] ──API──────►    │
[Portal]  ──Web──────►     │
                        [Kafka: claims.received]
                            │
                    [Validation Service]
                    - Member eligibility check
                    - Duplicate detection (Redis Bloom)
                    - Schema validation (Pydantic)
                            │
                    [Kafka: claims.validated]
                            │
                    [Adjudication Service]
                    - ML Model: approve/deny/review
                    - Rules engine (coverage, limits)
                    - HITL queue (confidence < 0.80)
                            │
                    [Kafka: claims.adjudicated]
                            │
                    [Payment Service] ──► [Bank API]
                            │
                    [EOB Generation Service]
                            │
                    [Notification Service] ──► Member/Provider
                            │
                    [PostgreSQL] — claim records, immutable audit log
                    [Redis]      — eligibility cache, dedup
                    [S3]         — claim documents, EOBs (encrypted)
```

### Key Design Decisions to Discuss
```python
# 1. Idempotency — same claim submitted twice
async def ingest_claim(claim: ClaimRequest) -> ClaimResponse:
    # Hash claim content for dedup key
    claim_hash = hashlib.sha256(claim.json().encode()).hexdigest()
    existing = await db.fetchrow("SELECT id FROM claims WHERE content_hash = $1", claim_hash)
    if existing:
        return ClaimResponse(id=existing["id"], status="duplicate_rejected")

    claim_id = await db.fetchval(
        "INSERT INTO claims (content_hash, data, status) VALUES ($1, $2, $3) RETURNING id",
        claim_hash, claim.json(), "received"
    )
    await kafka.send("claims.received", {"claim_id": claim_id})
    return ClaimResponse(id=claim_id, status="received")

# 2. Audit log — immutable append-only
async def audit(claim_id: str, action: str, actor: str, details: dict):
    await db.execute(
        """INSERT INTO claim_audit_log (claim_id, action, actor, details, ts)
           VALUES ($1, $2, $3, $4, NOW())""",
        claim_id, action, actor, json.dumps(details)
    )

# 3. PHI encryption at rest
from cryptography.fernet import Fernet

KEY = Fernet.generate_key()  # stored in AWS KMS / Azure Key Vault
fernet = Fernet(KEY)

def encrypt_phi(data: str) -> str:
    return fernet.encrypt(data.encode()).decode()

def decrypt_phi(token: str) -> str:
    return fernet.decrypt(token.encode()).decode()
```

### HIPAA Points to Always Mention
- All PHI fields encrypted at rest (AES-256) and in transit (TLS 1.2+)
- Audit log for every access to PHI (who, when, what)
- Role-based access control (RBAC) — adjudicators see only their queue
- Minimum necessary principle — mask unnecessary fields
- Business Associate Agreement (BAA) with all cloud providers
- Data retention: 7 years minimum

---

## DESIGN 5: Real-Time Chat / Messaging System

### Requirements
```
- 1:1 and group chat (up to 500 members)
- Message delivery guarantee (at least once)
- Online/offline status
- Message history (last 30 days hot, older in cold storage)
- 10M active users, 100K concurrent connections
- P99 message delivery < 500ms
```

### Architecture
```
[Client] ──WebSocket──► [WebSocket Gateway] (stateful)
                              │
                        [Redis PubSub] (message fan-out)
                              │
                     [Message Service]
                     - Persist to Cassandra
                     - Fan out to recipients
                              │
                    ┌─────────┴──────────┐
                    ▼                    ▼
             [Online Users]      [Offline Users]
             WebSocket push      [Push Notification]
                                 (FCM/APNs via Kafka)
                              │
                    [Cassandra] — messages (partition by conversation_id)
                    [Redis]     — online status, recent messages cache
                    [S3]        — media attachments
```

### WebSocket Connection Manager (Python)
```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
import asyncio, redis.asyncio as aioredis, json

app = FastAPI()
r = aioredis.Redis()

# In-process connection registry
connections: dict[str, WebSocket] = {}

@app.websocket("/ws/{user_id}")
async def websocket_endpoint(websocket: WebSocket, user_id: str):
    await websocket.accept()
    connections[user_id] = websocket
    await r.set(f"online:{user_id}", 1, ex=300)  # mark online

    # Subscribe to user's channel
    pubsub = r.pubsub()
    await pubsub.subscribe(f"user:{user_id}")

    async def listen():
        async for message in pubsub.listen():
            if message["type"] == "message":
                await websocket.send_text(message["data"].decode())

    listener_task = asyncio.create_task(listen())
    try:
        while True:
            data = await websocket.receive_text()
            msg = json.loads(data)
            await route_message(user_id, msg)
    except WebSocketDisconnect:
        del connections[user_id]
        await r.delete(f"online:{user_id}")
        listener_task.cancel()

async def route_message(sender_id: str, msg: dict):
    recipient_id = msg["to"]
    payload = json.dumps({"from": sender_id, "text": msg["text"], "ts": msg["ts"]})
    # Publish — recipient's WebSocket Gateway picks it up
    await r.publish(f"user:{recipient_id}", payload)
    # Persist
    await save_message(sender_id, recipient_id, msg)
```

---

## DESIGN 6: RAG-Based Document Q&A System (Deloitte AI / Accenture GenAI / Optum)

### Requirements
```
- Users ask questions over 5M internal documents (policies, guidelines, contracts)
- Answers must cite sources
- Latency < 5 seconds end-to-end
- Multi-tenant: users see only their org's documents
- HIPAA-compliant for healthcare documents
- Feedback loop for quality improvement
```

### Architecture
```
INGESTION PIPELINE (async batch):
[Documents: PDF/DOCX/HTML]
    │
[Document Parser] (Azure Document Intelligence / Unstructured.io)
    │
[Chunker] (400 tokens, 50 overlap, section-aware)
    │
[Embedder] (text-embedding-3-small, batch 100)
    │
[Qdrant] (vectors + metadata: tenant_id, doc_id, section, date)

QUERY PIPELINE (real-time):
[User Query]
    │
[Query Rewriter] (GPT-4o-mini → 3 query variants)
    │
[Hybrid Retrieval] (dense + BM25 → RRF fusion)
    │                     ↑ Qdrant filter: tenant_id = user.org
[Cross-Encoder Reranker] (top 5 of top 20)
    │
[Context Builder] (inject chunks + metadata)
    │
[GPT-4o] (answer + citations)
    │
[Response] (streamed via SSE)
    │
[Langfuse] (trace: latency, tokens, cost, user feedback)
```

### Python — Full Query Handler
```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from pydantic import BaseModel
from openai import AsyncOpenAI
from qdrant_client import QdrantClient, models
import asyncio, json

app = FastAPI()
client = AsyncOpenAI()
qdrant = QdrantClient(url="http://qdrant:6333")

class QueryRequest(BaseModel):
    question: str
    tenant_id: str
    user_id: str

async def embed(text: str) -> list[float]:
    resp = await client.embeddings.create(input=[text], model="text-embedding-3-small")
    return resp.data[0].embedding

async def retrieve(query: str, tenant_id: str, k: int = 5) -> list[dict]:
    vec = await embed(query)
    results = qdrant.search(
        collection_name="documents",
        query_vector=vec,
        limit=k,
        query_filter=models.Filter(must=[
            models.FieldCondition(key="tenant_id",
                                   match=models.MatchValue(value=tenant_id))
        ]),
        with_payload=True
    )
    return [{"text": r.payload["text"], "source": r.payload["source"],
             "score": r.score} for r in results]

@app.post("/query/stream")
async def query_stream(req: QueryRequest):
    docs = await retrieve(req.question, req.tenant_id)
    context = "\n\n".join(f"[{i+1}] {d['text']}" for i, d in enumerate(docs))
    sources = [d["source"] for d in docs]

    async def generate():
        stream = await client.chat.completions.create(
            model="gpt-4o",
            messages=[
                {"role": "system", "content":
                 "Answer using only the context. Cite [1],[2] etc. "
                 "If answer not in context, say 'Not found in documents.'"},
                {"role": "user", "content": f"Context:\n{context}\n\nQuestion: {req.question}"}
            ],
            stream=True, temperature=0
        )
        async for chunk in stream:
            delta = chunk.choices[0].delta.content or ""
            if delta:
                yield f"data: {json.dumps({'text': delta})}\n\n"
        yield f"data: {json.dumps({'sources': sources, 'done': True})}\n\n"

    return StreamingResponse(generate(), media_type="text/event-stream")
```

---

## DESIGN 7: Distributed Job Scheduler

### Requirements
```
- Schedule one-time and recurring jobs (cron-like)
- Distributed: multiple worker nodes
- At-least-once execution guarantee
- Job retries with exponential backoff
- Priority queues
- 100K jobs/day, peak 500 jobs/min
```

### Architecture
```
[API Service] — create/cancel/query jobs
    │
[PostgreSQL] — jobs table (id, schedule, status, next_run, priority, retries)
    │
[Scheduler Service] — poll for due jobs, publish to queue
    │
[Redis/SQS Queues] — high/medium/low priority
    │
[Worker Pool] — n workers, each picks jobs from queue
    │
[Job Execution] — runs user-defined function
    │
[Status Update] — success/failed → PostgreSQL
    │
[DLQ] — after max_retries exhausted
```

### Python — Scheduler with APScheduler + Celery
```python
from celery import Celery
from celery.schedules import crontab
from kombu import Queue
import asyncpg

# Define priority queues
app = Celery("scheduler", broker="redis://redis:6379/0")
app.conf.task_queues = (
    Queue("high",   routing_key="task.high"),
    Queue("medium", routing_key="task.medium"),
    Queue("low",    routing_key="task.low"),
)
app.conf.task_default_queue = "medium"
app.conf.task_acks_late = True           # ack only after completion
app.conf.task_reject_on_worker_lost = True  # requeue if worker dies

@app.task(bind=True, max_retries=3, default_retry_delay=60)
def execute_job(self, job_id: str, fn_name: str, kwargs: dict):
    try:
        fn = JOB_REGISTRY[fn_name]
        result = fn(**kwargs)
        update_job_status(job_id, "completed", result)
    except Exception as exc:
        update_job_status(job_id, "retrying")
        raise self.retry(exc=exc, countdown=2 ** self.request.retries * 60)

# Scheduler polls DB every minute
@app.on_after_configure.connect
def setup_polling(sender, **kwargs):
    sender.add_periodic_task(60.0, poll_due_jobs.s(), name="poll jobs")

@app.task
def poll_due_jobs():
    due = db.fetch_due_jobs()
    for job in due:
        execute_job.apply_async(
            args=[job["id"], job["fn_name"], job["kwargs"]],
            queue=job["priority"],
            task_id=job["id"]  # idempotent task ID
        )
```

---

## DESIGN 8: Multi-Tenant SaaS Platform

### Requirements
```
- Multiple organizations (tenants) sharing the platform
- Complete data isolation
- Per-tenant config, feature flags, rate limits
- 1000 tenants, 10K users each
- Custom domain per tenant
```

### Tenancy Models — Trade-offs
| Model | Isolation | Cost | Complexity |
|-------|-----------|------|-----------|
| Separate DB per tenant | Full | High (1000 DBs) | High ops |
| Separate schema per tenant | Good | Medium | Medium |
| Shared DB, row-level (tenant_id) | Low | Cheap | Low — RLS in PostgreSQL |
| Separate K8s namespace | Full | Very High | Very High |

**Recommended for most companies: Row-Level Security (RLS) in PostgreSQL**

```sql
-- Enable RLS on every table
ALTER TABLE claims ENABLE ROW LEVEL SECURITY;

-- Policy: users only see their tenant's data
CREATE POLICY tenant_isolation ON claims
    USING (tenant_id = current_setting('app.current_tenant')::uuid);

-- Set tenant context at connection time
SET app.current_tenant = '3f4a2b1c-...';
```

```python
# SQLAlchemy — set tenant context per request
from sqlalchemy import event, text

@event.listens_for(engine, "connect")
def set_tenant(dbapi_conn, connection_record):
    pass  # don't set here — set per request

async def get_db_for_tenant(tenant_id: str):
    async with async_session() as session:
        await session.execute(text(f"SET app.current_tenant = '{tenant_id}'"))
        yield session
```

---

## DESIGN 9: Microservices Authentication System

### Requirements
```
- JWT-based auth with refresh tokens
- OAuth2 / SSO (SAML 2.0 / OIDC) for enterprise tenants
- Service-to-service auth (mTLS or service tokens)
- Token revocation
- Audit: every login, token refresh, failed attempt logged
```

### Architecture
```
[Client]
  │ POST /auth/login
  ▼
[Auth Service]
  - Validate credentials (DB / LDAP / SSO)
  - Issue JWT (access: 15min) + Refresh Token (7 days)
  - Store refresh token hash in Redis (TTL 7 days)
  │
  ├──► [Redis] — active refresh tokens, blacklisted tokens
  ├──► [PostgreSQL] — users, roles, audit_log
  └──► Return: {access_token, refresh_token}

[Client] ──Bearer JWT──► [API Gateway]
                              │
                         [JWT Middleware]
                         - Verify signature
                         - Check expiry
                         - Check blacklist (Redis)
                         - Inject user_id + roles into request
                              │
                         [Microservices]
```

### JWT Implementation
```python
from datetime import datetime, timedelta
import jwt, redis, hashlib, secrets

SECRET_KEY = "from-env-or-vault"
r = redis.Redis()

def create_tokens(user_id: str, roles: list[str]) -> dict:
    access_payload = {
        "sub": user_id, "roles": roles, "type": "access",
        "exp": datetime.utcnow() + timedelta(minutes=15),
        "iat": datetime.utcnow(), "jti": secrets.token_hex(16)
    }
    access_token = jwt.encode(access_payload, SECRET_KEY, algorithm="HS256")

    refresh_token = secrets.token_urlsafe(64)
    token_hash = hashlib.sha256(refresh_token.encode()).hexdigest()
    r.setex(f"refresh:{token_hash}", 604800,  # 7 days
            f"{user_id}:{','.join(roles)}")

    return {"access_token": access_token, "refresh_token": refresh_token}

def verify_access_token(token: str) -> dict:
    payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
    # Check blacklist
    if r.get(f"blacklist:{payload['jti']}"):
        raise jwt.InvalidTokenError("Token revoked")
    return payload

def revoke_token(jti: str, expires_in: int = 900):
    r.setex(f"blacklist:{jti}", expires_in, 1)
```

---

## DESIGN 10: Data Pipeline for ML Features

### Requirements
```
- Ingest 500GB/day from multiple sources (DB, Kafka, S3, APIs)
- Compute features for 10M members
- Serve features in < 10ms for real-time scoring
- Backfill: recompute historical features
- Point-in-time correctness (no future leakage)
```

### Architecture — Lambda Architecture
```
BATCH LAYER (Airflow + Spark/Pandas):
[Sources: DB, S3, APIs]
    │
[Airflow DAG] — daily at 2am
    │
[Spark/Pandas] — compute batch features
    │
[Feature Store (Feast/Redis/PostgreSQL)]
    - Offline store: S3 Parquet (historical)
    - Online store: Redis (low-latency serving)

STREAMING LAYER (Kafka + Flink/Python):
[Real-time events: claims, eligibility changes]
    │
[Kafka Consumer] — Python service
    │
[Feature computation] — rolling windows, aggregates
    │
[Redis] — update online store in real-time

SERVING:
[ML Model API] ──► [Feature Store] ──► [Redis] < 10ms
```

### Python — Feature Pipeline with Airflow
```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime, timedelta
import pandas as pd, redis, json

r = redis.Redis()

def compute_member_features(execution_date: str, **ctx):
    df = pd.read_sql("""
        SELECT member_id,
               COUNT(claim_id)                    AS claim_count_30d,
               SUM(paid_amount)                   AS total_paid_30d,
               AVG(paid_amount)                   AS avg_paid_30d,
               SUM(CASE WHEN denied THEN 1 END)   AS denial_count_30d,
               MAX(service_date)                  AS last_service_date
        FROM claims
        WHERE service_date BETWEEN %(start)s AND %(end)s
        GROUP BY member_id
    """, con=engine, params={
        "start": (datetime.fromisoformat(execution_date) - timedelta(days=30)).date(),
        "end": execution_date
    })

    # Write to Redis online store
    pipe = r.pipeline()
    for _, row in df.iterrows():
        features = row.drop("member_id").to_dict()
        pipe.setex(f"features:{row.member_id}", 86400 * 7, json.dumps(features))
    pipe.execute()

    # Write to offline store (S3 Parquet)
    df["as_of_date"] = execution_date
    df.to_parquet(f"s3://ml-features/members/{execution_date}/features.parquet", index=False)

with DAG("member_features", schedule_interval="0 2 * * *",
          start_date=datetime(2024, 1, 1), catchup=False) as dag:
    PythonOperator(task_id="compute_features", python_callable=compute_member_features)
```

---

## PART 3: KEY SCALABILITY PATTERNS — MUST KNOW

### 1. Horizontal Scaling
```
- Stateless services: scale out by adding instances
- Session state in Redis (not in-process)
- Load balancing: round-robin, least connections, IP hash
- Auto-scaling: K8s HPA (scale on CPU/RPS), KEDA (scale on queue depth)
```

### 2. Database Scaling
```
Vertical:   Bigger instance — quick fix, has limit
Read replicas: Route reads to replicas (90% of queries are reads)
Sharding:   Split data by key (member_id % n_shards)
CQRS:       Separate read model (Elasticsearch) from write model (PostgreSQL)
Partitioning: PostgreSQL table partitioning by date — old partitions archivable
```

### 3. Caching Strategies
| Pattern | When | Example |
|---------|------|---------|
| Cache-aside | Read-heavy, cache miss OK | Member eligibility |
| Write-through | Consistency critical | User profile |
| Write-behind | High write throughput | Analytics counters |
| Read-through | Simple reads | Config data |
| Cache warming | Predictable access patterns | Daily batch pre-populate |

### 4. Async & Queue Patterns
```python
# Fan-out: one event → multiple consumers
# Claims submitted → [notification, audit, analytics, payment]
# Use Kafka topics with multiple consumer groups

# Competing consumers: multiple workers on same queue
# Use Celery or SQS — each message processed by exactly one worker

# Priority queues: high/medium/low
# Redis sorted set or SQS FIFO with message groups
```

### 5. Circuit Breaker
```python
# Using pybreaker
import pybreaker

breaker = pybreaker.CircuitBreaker(fail_max=5, reset_timeout=60)

@breaker
def call_eligibility_service(member_id: str) -> dict:
    return httpx.get(f"http://eligibility-svc/check/{member_id}").json()

# States: CLOSED (normal) → OPEN (failing, reject fast) → HALF-OPEN (test)
```

### 6. Saga Pattern (Distributed Transactions)
```
Claim Processing Saga:
1. Reserve claim slot ──────────────────────────────► OK
2. Check eligibility ──► If FAIL: release claim slot
3. Adjudicate ──────────► If FAIL: release + update status to "failed"
4. Schedule payment ────► If FAIL: reverse adjudication
5. Send notification ───► If FAIL: retry notification (non-critical)

Implement as:
- Choreography: each service publishes events, next listens
- Orchestration: saga orchestrator (LangGraph / Temporal / Conductor) tells each step what to do
```

---

## PART 4: COMMON FOLLOW-UP QUESTIONS (Asked After the Design)

| Question | Key Answer Points |
|----------|------------------|
| How do you handle database failover? | Primary-replica with automatic failover (AWS RDS Multi-AZ, pgBouncer), health checks, circuit breaker |
| How do you ensure exactly-once delivery? | Idempotency key + conditional insert, Kafka transactional producer, outbox pattern |
| How do you handle hot partitions in Kafka? | Random partition suffix on key, sticky partitioning, increase partitions |
| How do you monitor the system? | RED metrics (Rate/Errors/Duration), Prometheus + Grafana, distributed tracing (Jaeger/Zipkin), structured logs (ELK) |
| How do you handle schema changes without downtime? | Expand-contract migrations, backward-compatible schema, feature flags |
| What happens if Redis goes down? | Fallback to DB, degrade gracefully, Redis Sentinel/Cluster for HA |
| How do you handle slow consumers in Kafka? | Consumer lag monitoring, scale consumer group, back-pressure handling |
| How do you test distributed systems? | Contract testing (Pact), chaos engineering (Chaos Monkey), integration tests with Testcontainers |
| How do you do zero-downtime deployment? | Blue-green, canary (10% traffic), rolling updates in K8s |
| How do you handle versioning of APIs? | URL versioning `/v1/`, `v2/`, header versioning, deprecation headers |

---

## PART 5: NUMBERS EVERY ARCHITECT MUST KNOW

```
Latency:
  L1 cache:        0.5 ns
  RAM access:      100 ns
  SSD read:        100 μs
  Network (same DC): 0.5 ms
  HDD seek:        10 ms
  Network (cross-region): 50-150 ms

Throughput:
  Redis:           100K-1M ops/sec
  PostgreSQL:      10K-50K TPS (well-tuned)
  Kafka:           1M+ messages/sec per broker
  HTTP (FastAPI):  10K-50K req/sec per pod

Storage:
  1 char ≈ 1 byte
  1M users × 1KB profile = 1GB
  1B records × 100 bytes = 100GB
  1 token (LLM) ≈ 4 chars
  1 embedding (1536-dim float32) ≈ 6KB

Rules of thumb:
  Read/write ratio in most apps = 10:1 to 100:1
  Cache hit rate should be > 90% for effective caching
  P99 latency ≈ 5-10x P50 in production
  Add 2x buffer to capacity estimates
```

---

## PART 6: HOW TO ANSWER IN THE INTERVIEW — TEMPLATE

```
1. "Let me start by clarifying requirements..."
   - Ask: DAU, QPS, data volume, read/write ratio
   - Ask: Consistency vs availability trade-off
   - Ask: Any specific compliance needs (HIPAA, SOC2, GDPR)?

2. "Let me estimate the scale..."
   - Write numbers on the board/screen
   - Calculate QPS, storage, bandwidth

3. "Here's my high-level design..."
   - Draw boxes and arrows
   - Name every component (don't say "database" — say PostgreSQL/DynamoDB)
   - Show data flow end-to-end

4. "Let me deep-dive into [most critical component]..."
   - Justify your choices with trade-offs
   - Mention alternatives you considered

5. "For failure handling..."
   - What happens if Redis is down? → fallback to DB
   - What happens if a service crashes? → Kafka retries, idempotency
   - What happens if DB is slow? → circuit breaker, timeouts

6. "For scale..."
   - How to scale from 100K to 10M users?
   - Sharding, CDN, read replicas, queue-based load leveling

7. "Trade-offs I made..."
   - Consistency vs availability (CAP theorem)
   - Cost vs latency
   - Complexity vs reliability
```

---

## PART 7: SYSTEM DESIGN CHEAT SHEET

### When to use what:

| Need | Use |
|------|-----|
| Session storage / cache | Redis |
| Relational data + ACID | PostgreSQL |
| Document storage | MongoDB |
| Time-series data | InfluxDB / TimescaleDB |
| Full-text search | Elasticsearch |
| Vector search | Qdrant / pgvector |
| Message queue (at-least-once) | Kafka / SQS |
| Task queue (workers) | Celery + Redis |
| Object storage | S3 / Azure Blob |
| CDN | CloudFront / Azure CDN |
| API gateway + auth | Kong / AWS API Gateway |
| Service mesh | Istio / Linkerd |
| Observability | Prometheus + Grafana + Jaeger |
| Secrets | AWS Secrets Manager / HashiCorp Vault |
| ML experiment tracking | MLflow |
| LLM observability | Langfuse / LangSmith |
| Vector DB for RAG | Qdrant / Pinecone |
| Workflow orchestration | Airflow / Prefect / Temporal |
| Distributed tracing | Jaeger / Zipkin / OpenTelemetry |

### CAP Theorem Quick Reference
```
CP systems (Consistent + Partition-tolerant):
  → MongoDB, HBase, Zookeeper
  → Use when: banking, claims, inventory (wrong data = disaster)

AP systems (Available + Partition-tolerant):
  → Cassandra, CouchDB, DynamoDB (default)
  → Use when: social feed, notifications, analytics (stale data OK)

CA systems (Consistent + Available) — impossible in distributed systems
  → Only possible if you accept no network partitions
  → Single-node PostgreSQL (not distributed)
```

---

## PART 8: QUICK DESIGN TEMPLATES (5-minute sketches)

### Any CRUD Service
```
Client → API Gateway → [Service] → PostgreSQL
                           │
                        Redis (cache)
                           │
                        Kafka (events)
```

### Any Event-Driven Pipeline
```
Source → Kafka → Consumer → Processor → DB
                     │
                    DLQ (failed messages)
```

### Any ML Serving System
```
Client → API → Feature Fetch (Redis) → Model (ONNX/joblib) → Response
                     │                        │
                  Feature Store         Model Registry
                  (Feast/Redis)         (MLflow)
```

### Any GenAI / RAG System
```
Query → Embed → Vector Search (Qdrant) → Rerank → LLM → Response
                      ↑ filter: tenant_id
         Ingest: Doc → Parse → Chunk → Embed → Qdrant
```
