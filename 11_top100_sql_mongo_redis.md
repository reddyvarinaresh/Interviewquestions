# Top 100 SQL / MongoDB / Redis with Python Interview Questions
> Focus: 5–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise
> Sources: Glassdoor, LinkedIn, Reddit, Medium, GeeksforGeeks

---

## SECTION 1: SQL & SQLAlchemy (Q1–Q35)

### Q1
- **Question:** How do you connect Python to PostgreSQL? Compare raw `psycopg2`, SQLAlchemy Core, and SQLAlchemy ORM.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `psycopg2`: raw driver; full control; verbose; best for batch inserts, complex queries
  - SQLAlchemy Core: SQL expression language; Pythonic but still SQL-close; good for DBA-style work
  - SQLAlchemy ORM: model classes map to tables; `relationship()`, lazy/eager loading; best for domain-driven apps
  - `asyncpg` + SQLAlchemy async: for async FastAPI services
- **Difficulty:** Easy

---

### Q2
- **Question:** What is the N+1 query problem? How do you detect and fix it in SQLAlchemy?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # N+1 Bug: 1 query for orders + N queries for each customer
  orders = session.query(Order).all()
  for order in orders:
      print(order.customer.name)  # triggers SELECT per order

  # Fix: eager load with joinedload
  from sqlalchemy.orm import joinedload
  orders = session.query(Order).options(joinedload(Order.customer)).all()

  # Or selectinload for large collections
  from sqlalchemy.orm import selectinload
  orders = session.query(Order).options(selectinload(Order.items)).all()
  ```
- **Detection:** Enable query logging: `engine = create_engine(url, echo=True)`
- **Follow-Ups:** When would you use `selectinload` vs `joinedload`?
- **Difficulty:** Medium

---

### Q3
- **Question:** How does SQLAlchemy's session lifecycle work? What is `expire_on_commit`?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Session tracks objects in identity map; tracks changes (dirty objects)
  - `session.add()` → pending; `session.commit()` → writes to DB; objects expire
  - `expire_on_commit=True` (default): after commit, all attributes expired; next access hits DB
  - `expire_on_commit=False`: attributes remain cached after commit; risk: stale data
  - `session.refresh(obj)` forces reload from DB
  - Always close session in `finally` or use dependency injection with generator
- **Difficulty:** Hard

---

### Q4
- **Question:** How do you implement connection pooling in SQLAlchemy? What settings matter?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from sqlalchemy import create_engine

  engine = create_engine(
      DATABASE_URL,
      pool_size=10,           # number of persistent connections
      max_overflow=20,        # extra connections beyond pool_size
      pool_timeout=30,        # wait time before giving up
      pool_recycle=3600,      # recycle connections after 1 hour (prevents stale connections)
      pool_pre_ping=True,     # test connection before use (handles DB restarts)
  )
  ```
- **Follow-Ups:** What is `pool_pre_ping`? Why is it important in cloud environments?
- **Difficulty:** Medium

---

### Q5
- **Question:** How do you run database migrations with Alembic in production?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `alembic init alembic` — set up migration environment
  - `alembic revision --autogenerate -m "add claims table"` — generate migration
  - Review generated migration carefully (autogenerate misses some things)
  - `alembic upgrade head` — run pending migrations
  - Production strategy: run migrations in CI/CD before app deployment; lock table during migration on large tables
  - Rollback: `alembic downgrade -1`
- **Difficulty:** Medium

---

### Q6
- **Question:** Write a SQL query to find the top 3 members with the highest total claim amount per plan.
- **Level:** 5–8 Yrs | **Company:** Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```sql
  WITH member_totals AS (
      SELECT
          member_id,
          plan_code,
          SUM(claim_amount) AS total_amount,
          RANK() OVER (PARTITION BY plan_code ORDER BY SUM(claim_amount) DESC) AS rnk
      FROM claims
      WHERE status = 'paid'
      GROUP BY member_id, plan_code
  )
  SELECT member_id, plan_code, total_amount
  FROM member_totals
  WHERE rnk <= 3
  ORDER BY plan_code, rnk;
  ```
- **Difficulty:** Medium

---

### Q7
- **Question:** How do you write a SQL query to find duplicate records and delete all but one?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```sql
  -- Find duplicates
  SELECT member_id, service_date, COUNT(*) as cnt
  FROM claims
  GROUP BY member_id, service_date
  HAVING COUNT(*) > 1;

  -- Delete duplicates, keep lowest id
  DELETE FROM claims
  WHERE id NOT IN (
      SELECT MIN(id)
      FROM claims
      GROUP BY member_id, service_date
  );

  -- PostgreSQL: using CTE + ctid
  DELETE FROM claims
  WHERE ctid NOT IN (
      SELECT MIN(ctid)
      FROM claims
      GROUP BY member_id, service_date
  );
  ```
- **Difficulty:** Medium

---

### Q8
- **Question:** Explain SQL window functions. Write an example using `ROW_NUMBER`, `RANK`, `LAG`.
- **Level:** 8–12 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```sql
  SELECT
      claim_id,
      member_id,
      claim_date,
      amount,
      ROW_NUMBER() OVER (PARTITION BY member_id ORDER BY claim_date) AS claim_seq,
      RANK() OVER (PARTITION BY member_id ORDER BY amount DESC) AS amount_rank,
      LAG(amount, 1) OVER (PARTITION BY member_id ORDER BY claim_date) AS prev_amount,
      amount - LAG(amount, 1) OVER (PARTITION BY member_id ORDER BY claim_date) AS delta
  FROM claims;
  ```
- **Difficulty:** Medium

---

### Q9
- **Question:** How do you execute bulk inserts efficiently in Python with PostgreSQL?
- **Level:** 8–12 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # SQLAlchemy Core bulk insert (fastest ORM option)
  session.execute(
      insert(Claim),
      [{"member_id": r.member_id, "amount": r.amount} for r in records]
  )
  session.commit()

  # psycopg2 execute_values (fastest raw)
  from psycopg2.extras import execute_values
  execute_values(
      cursor,
      "INSERT INTO claims (member_id, amount) VALUES %s",
      [(r.member_id, r.amount) for r in records],
      page_size=1000
  )

  # COPY for maximum throughput
  cursor.copy_expert("COPY claims FROM STDIN CSV", csv_buffer)
  ```
- **Difficulty:** Hard

---

### Q10
- **Question:** How do you handle database transactions with SQLAlchemy? When do you use savepoints?
- **Level:** 8–12 Yrs | **Company:** Enterprise, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Basic transaction
  with session.begin():
      session.add(claim)
      session.add(audit_log)
  # auto-commits or rolls back on exception

  # Savepoint for partial rollback
  with session.begin():
      session.add(claim)
      sp = session.begin_nested()  # savepoint
      try:
          session.add(notification)  # may fail
          sp.commit()
      except Exception:
          sp.rollback()  # only notification rolled back, claim still committed
  ```
- **Difficulty:** Hard

---

### Q11–Q35 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 11 | What is `EXPLAIN ANALYZE`? How do you use it to find slow queries? | 8–12 Yrs | High | T1 |
| 12 | How do you add a database index? When does an index hurt performance? | 5–8 Yrs | High | T1 |
| 13 | What is a partial index? Give a use case for it. | 8–12 Yrs | Medium | T2 |
| 14 | What is the difference between `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL OUTER JOIN`? | 3–5 Yrs | High | T1 |
| 15 | What is a CTE? How does it differ from a subquery? | 5–8 Yrs | High | T1 |
| 16 | What is a recursive CTE? Write one to traverse a hierarchy. | 8–12 Yrs | Medium | T2 |
| 17 | What is database normalization? When would you intentionally denormalize? | 5–8 Yrs | High | T1 |
| 18 | What are isolation levels? Compare READ COMMITTED vs SERIALIZABLE. | 8–12 Yrs | High | T1 |
| 19 | How do you prevent a phantom read in PostgreSQL? | 8–12 Yrs | Medium | T2 |
| 20 | What is an optimistic lock in SQL? How do you implement it with a version column? | 8–12 Yrs | High | T1 |
| 21 | How does SQLAlchemy handle lazy vs eager loading? What are the tradeoffs? | 8–12 Yrs | High | T1 |
| 22 | How do you implement soft delete in SQLAlchemy ORM? | 5–8 Yrs | High | T1 |
| 23 | How do you use SQLAlchemy events (`@event.listens_for`) for auditing? | 8–12 Yrs | Medium | T2 |
| 24 | How do you implement a custom SQLAlchemy type (e.g., encrypted field)? | 8–12 Yrs | Medium | T2 |
| 25 | How do you paginate efficiently with SQL for large tables? (offset vs keyset) | 8–12 Yrs | High | T1 |
| 26 | How do you implement database sharding? What are the tradeoffs? | 12–15 Yrs | Medium | T2 |
| 27 | What is a materialized view? When do you use it? | 8–12 Yrs | Medium | T2 |
| 28 | How do you handle schema changes (column adds/renames) with zero downtime? | 12–15 Yrs | High | T1 |
| 29 | How do you write a SQL query to detect gaps in a sequence? | 8–12 Yrs | Medium | T2 |
| 30 | How do you implement read replicas in a Python service? | 12–15 Yrs | Medium | T2 |
| 31 | What is the difference between `TRUNCATE` and `DELETE`? Performance implications? | 5–8 Yrs | Medium | T2 |
| 32 | How do you handle a long-running transaction that's blocking other queries? | 8–12 Yrs | High | T1 |
| 33 | How do you implement a leaderboard query with RANK and DENSE_RANK? | 5–8 Yrs | Medium | T2 |
| 34 | How do you store and query JSON data in PostgreSQL? (`JSONB` type) | 8–12 Yrs | High | T1 |
| 35 | How do you implement database connection retry logic in Python? | 8–12 Yrs | High | T1 |

---

## SECTION 2: MongoDB with Python (Q36–Q65)

### Q36
- **Question:** How do you connect Python to MongoDB? Compare `pymongo` and `motor`.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # pymongo (sync)
  from pymongo import MongoClient
  client = MongoClient("mongodb://localhost:27017/", maxPoolSize=50)
  db = client["healthcare"]
  collection = db["claims"]

  # motor (async)
  import motor.motor_asyncio
  client = motor.motor_asyncio.AsyncIOMotorClient("mongodb://localhost:27017/")
  db = client["healthcare"]
  ```
- **Answer Points:** `motor` wraps `pymongo` with asyncio; essential for FastAPI; same query API
- **Difficulty:** Easy

---

### Q37
- **Question:** What is MongoDB's aggregation pipeline? Write one to group claims by status and sum amounts.
- **Level:** 5–8 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  pipeline = [
      {"$match": {"created_at": {"$gte": start_date}}},
      {"$group": {
          "_id": "$status",
          "total_amount": {"$sum": "$amount"},
          "count": {"$sum": 1},
          "avg_amount": {"$avg": "$amount"}
      }},
      {"$sort": {"total_amount": -1}},
      {"$project": {
          "status": "$_id",
          "total_amount": 1,
          "count": 1,
          "avg_amount": {"$round": ["$avg_amount", 2]},
          "_id": 0
      }}
  ]
  results = list(collection.aggregate(pipeline))
  ```
- **Difficulty:** Medium

---

### Q38
- **Question:** How do you create indexes in MongoDB with Python? What types are available?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pymongo import ASCENDING, DESCENDING, TEXT

  # Single field index
  collection.create_index([("member_id", ASCENDING)], unique=True)

  # Compound index
  collection.create_index([("plan_code", ASCENDING), ("status", ASCENDING)])

  # Text index for search
  collection.create_index([("description", TEXT)])

  # TTL index for auto-expiry
  collection.create_index([("created_at", ASCENDING)], expireAfterSeconds=2592000)

  # Partial index
  collection.create_index(
      [("amount", DESCENDING)],
      partialFilterExpression={"status": "pending"}
  )
  ```
- **Difficulty:** Medium

---

### Q39
- **Question:** What is MongoDB's `$lookup`? Write an aggregation that joins claims with members.
- **Level:** 8–12 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  pipeline = [
      {"$lookup": {
          "from": "members",
          "localField": "member_id",
          "foreignField": "_id",
          "as": "member"
      }},
      {"$unwind": {"path": "$member", "preserveNullAndEmptyArrays": True}},
      {"$project": {
          "claim_id": 1,
          "amount": 1,
          "member_name": "$member.full_name",
          "plan_code": "$member.plan_code"
      }}
  ]
  ```
- **Difficulty:** Medium

---

### Q40
- **Question:** How do you perform bulk writes efficiently in MongoDB with Python?
- **Level:** 8–12 Yrs | **Company:** Data, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pymongo import InsertOne, UpdateOne, DeleteOne, ReplaceOne

  operations = [
      InsertOne({"member_id": "M001", "amount": 500}),
      UpdateOne({"claim_id": "C002"}, {"$set": {"status": "approved"}}),
      UpdateOne(
          {"member_id": "M003"},
          {"$set": {"updated_at": datetime.utcnow()}},
          upsert=True
      )
  ]

  result = collection.bulk_write(operations, ordered=False)
  print(f"Inserted: {result.inserted_count}, Updated: {result.modified_count}")
  ```
- **Difficulty:** Medium

---

### Q41
- **Question:** What is MongoDB's change stream? How do you use it in Python for event-driven processing?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  async def watch_claims():
      pipeline = [{"$match": {"operationType": {"$in": ["insert", "update"]}}}]
      async with collection.watch(pipeline) as stream:
          async for change in stream:
              event_type = change["operationType"]
              doc = change.get("fullDocument") or change.get("updateDescription")
              await process_change(event_type, doc)
  ```
- **Answer Points:** Requires replica set; change streams survive network interruptions with resume token
- **Difficulty:** Hard

---

### Q42–Q65 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 42 | How do you implement pagination in MongoDB? (skip/limit vs cursor-based) | 5–8 Yrs | High | T1 |
| 43 | How do you design a MongoDB schema for a healthcare claims system? | 8–12 Yrs | High | T1 |
| 44 | What is the `$facet` stage in MongoDB aggregation? | 8–12 Yrs | Medium | T2 |
| 45 | How do you implement full-text search in MongoDB? | 8–12 Yrs | Medium | T2 |
| 46 | What is `$push` vs `$addToSet` in MongoDB update operators? | 5–8 Yrs | Medium | T2 |
| 47 | How do you implement optimistic concurrency control in MongoDB? | 8–12 Yrs | Medium | T2 |
| 48 | How do you handle document schema evolution in MongoDB? | 8–12 Yrs | High | T1 |
| 49 | What is `explain()` in MongoDB? How do you use it to optimize queries? | 8–12 Yrs | High | T1 |
| 50 | How does MongoDB's write concern work? `w=1` vs `w=majority`? | 8–12 Yrs | Medium | T2 |
| 51 | What is MongoDB's read preference? When do you use `secondaryPreferred`? | 8–12 Yrs | Medium | T2 |
| 52 | How do you implement transactions in MongoDB with Python? | 8–12 Yrs | High | T1 |
| 53 | What is the difference between embedding vs referencing in MongoDB? | 5–8 Yrs | High | T1 |
| 54 | How do you use `PyMongo`'s codec options for custom type encoding? | 8–12 Yrs | Low | T3 |
| 55 | How do you monitor MongoDB query performance in Python? | 8–12 Yrs | Medium | T2 |
| 56 | What is `$cond` in MongoDB aggregation? Write an if-else expression. | 8–12 Yrs | Medium | T2 |
| 57 | How do you archive old documents to cold storage in MongoDB? | 8–12 Yrs | Medium | T2 |
| 58 | What is GridFS? When would you use it in Python? | 8–12 Yrs | Low | T3 |
| 59 | How do you implement a document-level audit trail in MongoDB? | 8–12 Yrs | Medium | T2 |
| 60 | How do you handle duplicate key errors in MongoDB bulk operations? | 5–8 Yrs | High | T1 |
| 61 | How do you use `pymongo`'s connection pool in a multithreaded Flask app? | 8–12 Yrs | Medium | T2 |
| 62 | What is `$merge` stage in MongoDB aggregation? | 8–12 Yrs | Low | T3 |
| 63 | How do you validate MongoDB document structure with Pydantic? | 5–8 Yrs | High | T1 |
| 64 | How do you run a MapReduce operation in MongoDB with Python? | 12–15 Yrs | Low | T3 |
| 65 | How do you implement a distributed lock using MongoDB? | 12–15 Yrs | Medium | T2 |

---

## SECTION 3: Redis with Python (Q66–Q100)

### Q66
- **Question:** How do you connect to Redis in Python? Compare `redis-py` and `redis.asyncio`.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Sync
  import redis
  r = redis.Redis(host='localhost', port=6379, db=0, decode_responses=True)

  # Async (for FastAPI)
  import redis.asyncio as aioredis
  r = aioredis.Redis(host='localhost', port=6379, decode_responses=True)

  # Connection pool (for production)
  pool = redis.ConnectionPool(host='localhost', port=6379, max_connections=50)
  r = redis.Redis(connection_pool=pool)
  ```
- **Difficulty:** Easy

---

### Q67
- **Question:** How do you implement a function-level cache with Redis in Python (with TTL)?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import json, functools
  from redis import Redis

  def redis_cache(ttl: int = 300):
      def decorator(func):
          @functools.wraps(func)
          async def wrapper(*args, **kwargs):
              key = f"{func.__name__}:{args}:{sorted(kwargs.items())}"
              cached = await redis.get(key)
              if cached:
                  return json.loads(cached)
              result = await func(*args, **kwargs)
              await redis.setex(key, ttl, json.dumps(result, default=str))
              return result
          return wrapper
      return decorator

  @redis_cache(ttl=600)
  async def get_member_plan(member_id: str):
      return await db.query_member_plan(member_id)
  ```
- **Difficulty:** Medium

---

### Q68
- **Question:** How do you implement a distributed lock in Redis? What is the Redlock algorithm?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import uuid, asyncio

  async def acquire_lock(redis, key: str, ttl: int = 10) -> str | None:
      lock_val = str(uuid.uuid4())
      acquired = await redis.set(key, lock_val, ex=ttl, nx=True)
      return lock_val if acquired else None

  async def release_lock(redis, key: str, lock_val: str):
      # Lua script for atomic check-and-delete
      lua = """
      if redis.call("get", KEYS[1]) == ARGV[1] then
          return redis.call("del", KEYS[1])
      else
          return 0
      end
      """
      await redis.eval(lua, 1, key, lock_val)

  # Usage
  async def process_member(member_id: str):
      lock_val = await acquire_lock(redis, f"lock:member:{member_id}")
      if not lock_val:
          raise RuntimeError("Could not acquire lock")
      try:
          await do_processing(member_id)
      finally:
          await release_lock(redis, f"lock:member:{member_id}", lock_val)
  ```
- **Answer Points:** Redlock = acquire lock on N independent Redis nodes; majority wins; safer for distributed systems
- **Difficulty:** Hard

---

### Q69
- **Question:** How do you implement a Redis-based rate limiter (sliding window)?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  async def sliding_window_rate_limit(
      redis, key: str, max_calls: int, window_secs: int
  ) -> bool:
      now = time.time()
      window_start = now - window_secs
      pipe = redis.pipeline()
      pipe.zremrangebyscore(key, 0, window_start)  # remove old entries
      pipe.zadd(key, {str(uuid.uuid4()): now})      # add current request
      pipe.zcard(key)                                # count in window
      pipe.expire(key, window_secs)
      results = await pipe.execute()
      return results[2] <= max_calls
  ```
- **Difficulty:** Hard

---

### Q70
- **Question:** What Redis data structures do you use in Python applications? Give a use case for each.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **String**: caching, session tokens, counters (`INCR`)
  - **Hash**: user session data, object fields (`HSET`, `HGETALL`)
  - **List**: task queues, recent activity feeds (`LPUSH`, `BRPOP`)
  - **Set**: unique visitors, online users, deduplication (`SADD`, `SMEMBERS`)
  - **Sorted Set**: leaderboards, rate limiting, scheduled tasks (`ZADD`, `ZRANGE`)
  - **Stream**: event streaming, audit log (`XADD`, `XREAD`)
  - **Pub/Sub**: real-time notifications, event broadcasting
- **Difficulty:** Medium

---

### Q71–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 71 | How do you implement a task queue using Redis Lists? (`LPUSH`/`BRPOP`) | 8–12 Yrs | High | T1 |
| 72 | How do you use Redis Pub/Sub in Python for real-time notifications? | 8–12 Yrs | Medium | T2 |
| 73 | How do you implement session management with Redis in FastAPI? | 8–12 Yrs | High | T1 |
| 74 | How does Redis Streams differ from Pub/Sub? When do you use each? | 8–12 Yrs | Medium | T2 |
| 75 | How do you implement a Celery broker using Redis? | 8–12 Yrs | High | T1 |
| 76 | What is Redis pipeline? How does it reduce round-trip latency? | 8–12 Yrs | High | T1 |
| 77 | How do you implement a leaderboard using Redis Sorted Sets? | 5–8 Yrs | Medium | T2 |
| 78 | What is Redis Lua scripting? When do you use it? | 8–12 Yrs | Medium | T2 |
| 79 | How do you handle Redis cache invalidation? What strategies exist? | 8–12 Yrs | High | T1 |
| 80 | What is cache stampede? How do you prevent it with Redis? | 8–12 Yrs | High | T1 |
| 81 | How do you implement cache-aside pattern vs write-through in Python? | 8–12 Yrs | High | T1 |
| 82 | How do you monitor Redis memory usage from Python? | 8–12 Yrs | Medium | T2 |
| 83 | How do you implement a two-level cache (in-memory L1 + Redis L2) in Python? | 12–15 Yrs | Medium | T2 |
| 84 | How does Redis TTL work? What happens when a key expires? | 5–8 Yrs | High | T1 |
| 85 | How do you implement Redis Sentinel failover in a Python service? | 12–15 Yrs | Low | T3 |
| 86 | What is Redis Cluster? How does key hashing work? | 12–15 Yrs | Low | T3 |
| 87 | How do you implement an idempotency store using Redis? | 8–12 Yrs | High | T1 |
| 88 | How do you use Redis to store and retrieve LLM conversation history? | 8–12 Yrs | High | T1 |
| 89 | How do you implement semantic caching with Redis for LLM responses? | 8–12 Yrs | Medium | T2 |
| 90 | How do you test Redis-dependent Python code in unit tests? | 5–8 Yrs | High | T1 |
| 91 | How do you handle Redis connection errors gracefully? | 5–8 Yrs | High | T1 |
| 92 | How do you implement a job scheduler using Redis Sorted Sets? | 8–12 Yrs | Medium | T2 |
| 93 | How do you use `redis-py`'s `SCAN` instead of `KEYS` for production? | 8–12 Yrs | Medium | T2 |
| 94 | How do you implement a token bucket rate limiter with Redis? | 8–12 Yrs | Medium | T2 |
| 95 | How do you store and retrieve complex Python objects in Redis? | 5–8 Yrs | High | T1 |
| 96 | What is `UNLINK` vs `DEL` in Redis? When does it matter? | 8–12 Yrs | Low | T3 |
| 97 | How do you implement a distributed counter that's accurate under high concurrency? | 8–12 Yrs | Medium | T2 |
| 98 | How do you use Redis for caching Pydantic model responses in FastAPI? | 8–12 Yrs | High | T1 |
| 99 | How do you implement a pub/sub fanout for real-time claim status updates? | 8–12 Yrs | Medium | T2 |
| 100 | Design a Redis-backed caching and rate-limiting layer for a high-traffic Python API serving 50K req/sec. | 12–15 Yrs | High | T1 |
