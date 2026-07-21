# Top 100 Cloud / DevOps Python Interview Questions
> Focus: 5–15 Years | Non-FAANG | Service, Consulting, Healthcare, Enterprise
> Sources: Glassdoor, LinkedIn, Reddit, Medium, AWS/Azure Blogs

---

## SECTION 1: Docker & Containers (Q1–Q20)

### Q1
- **Question:** Write a production-ready Dockerfile for a FastAPI Python application.
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```dockerfile
  FROM python:3.11-slim as builder
  WORKDIR /app
  COPY requirements.txt .
  RUN pip install --no-cache-dir --user -r requirements.txt

  FROM python:3.11-slim
  WORKDIR /app
  COPY --from=builder /root/.local /root/.local
  COPY . .
  ENV PATH=/root/.local/bin:$PATH
  ENV PYTHONUNBUFFERED=1
  ENV PYTHONDONTWRITEBYTECODE=1

  RUN adduser --disabled-password --gecos '' appuser && chown -R appuser /app
  USER appuser

  EXPOSE 8000
  HEALTHCHECK --interval=30s --timeout=5s --start-period=10s \
    CMD curl -f http://localhost:8000/health || exit 1

  CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]
  ```
- **Answer Points:** Multi-stage build; non-root user; `PYTHONUNBUFFERED=1` for Docker logs; health check; pinned base image
- **Difficulty:** Medium

---

### Q2
- **Question:** Why is `PYTHONUNBUFFERED=1` important in Docker? What happens without it?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Without it: Python buffers stdout; logs only appear when buffer flushes (on crash or full buffer)
  - In Docker: container might crash with no logs visible
  - `PYTHONUNBUFFERED=1` disables output buffering; logs appear in real-time
  - Alternative: `python -u` flag
- **Difficulty:** Easy

---

### Q3
- **Question:** What is a multi-stage Docker build? Why do you use it for Python?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Stage 1 (builder): install build tools, compile dependencies
  - Stage 2 (runtime): copy only compiled packages, no build tools
  - Result: much smaller final image (500MB → 100MB)
  - Fewer security vulnerabilities — no compiler, dev tools in prod image
  - Separate build and runtime concerns cleanly
- **Difficulty:** Easy

---

### Q4
- **Question:** How do you optimize Docker image size for a Python application?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Use `python:3.11-slim` instead of `python:3.11` (400MB smaller)
  - Multi-stage build
  - `pip install --no-cache-dir` — don't cache pip downloads in image
  - `.dockerignore` — exclude `__pycache__`, `.git`, `tests/`, `*.pyc`
  - Combine `RUN` commands to reduce layers
  - Use `COPY requirements.txt .` before `COPY . .` to maximize layer cache
- **Difficulty:** Easy

---

### Q5
- **Question:** How do you handle secrets in a Docker container for a Python service?
- **Level:** 8–12 Yrs | **Company:** Enterprise, Healthcare | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - NEVER bake secrets into Dockerfile or image
  - Environment variables via Kubernetes secrets (mounted, not shell-expanded)
  - Docker secrets (Swarm) or Kubernetes `secretKeyRef`
  - At runtime: load from AWS Secrets Manager / Azure Key Vault
  - Python: `pydantic_settings.BaseSettings` reads from env at startup
- **Difficulty:** Medium

---

### Q6–Q20 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 6 | What is `.dockerignore`? What should you always exclude for Python? | 5–8 Yrs | High | T1 |
| 7 | How do you configure Docker health checks for a FastAPI service? | 5–8 Yrs | High | T1 |
| 8 | How do you run a Python development environment with Docker Compose? | 5–8 Yrs | High | T1 |
| 9 | How does Docker layer caching work? How do you optimize for Python pip installs? | 5–8 Yrs | High | T1 |
| 10 | How do you configure Gunicorn + Uvicorn workers in Docker? | 8–12 Yrs | High | T1 |
| 11 | How do you pass runtime configuration to a Python Docker container? | 5–8 Yrs | High | T1 |
| 12 | How do you set resource limits (CPU, memory) for a Python container? | 8–12 Yrs | Medium | T2 |
| 13 | How do you debug a Python application running in a Docker container? | 5–8 Yrs | High | T1 |
| 14 | How do you implement graceful shutdown in a Docker container for Python? | 8–12 Yrs | High | T1 |
| 15 | How do you share volumes between containers in Docker Compose? | 5–8 Yrs | Medium | T2 |
| 16 | What is the difference between `CMD` and `ENTRYPOINT` in Dockerfile? | 5–8 Yrs | High | T1 |
| 17 | How do you scan a Python Docker image for vulnerabilities? | 8–12 Yrs | Medium | T2 |
| 18 | How do you implement Docker init to handle signals properly for Python? | 8–12 Yrs | Medium | T2 |
| 19 | How do you handle log rotation for a Python service in Docker? | 8–12 Yrs | Medium | T2 |
| 20 | How do you write a Docker Compose file for Python + PostgreSQL + Redis? | 5–8 Yrs | High | T1 |

---

## SECTION 2: Kubernetes (Q21–Q45)

### Q21
- **Question:** How do you deploy a Python FastAPI service to Kubernetes? Walk through the manifests.
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```yaml
  # deployment.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: claims-api
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: claims-api
    template:
      metadata:
        labels:
          app: claims-api
      spec:
        containers:
        - name: claims-api
          image: company/claims-api:1.2.3
          ports:
          - containerPort: 8000
          resources:
            requests:
              cpu: "250m"
              memory: "256Mi"
            limits:
              cpu: "1000m"
              memory: "512Mi"
          livenessProbe:
            httpGet:
              path: /health/live
              port: 8000
            initialDelaySeconds: 10
            periodSeconds: 15
          readinessProbe:
            httpGet:
              path: /health/ready
              port: 8000
            initialDelaySeconds: 5
            periodSeconds: 10
          env:
          - name: DB_PASSWORD
            valueFrom:
              secretKeyRef:
                name: db-secret
                key: password
  ```
- **Difficulty:** Medium

---

### Q22
- **Question:** What is the difference between liveness and readiness probes? How do you implement them in FastAPI?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  @app.get("/health/live")
  async def liveness():
      """Is the process alive? Fast check — never fails if process is running."""
      return {"status": "alive"}

  @app.get("/health/ready")
  async def readiness(db: Session = Depends(get_db), redis: Redis = Depends(get_redis)):
      """Is the service ready to serve traffic? Checks dependencies."""
      checks = {}
      try:
          await db.execute(text("SELECT 1"))
          checks["database"] = "ok"
      except Exception:
          checks["database"] = "error"

      try:
          await redis.ping()
          checks["redis"] = "ok"
      except Exception:
          checks["redis"] = "error"

      if any(v != "ok" for v in checks.values()):
          raise HTTPException(503, detail=checks)
      return checks
  ```
- **Answer Points:** Liveness failure → restart pod; Readiness failure → remove from service (stop traffic); never make liveness probe check external deps
- **Difficulty:** Medium

---

### Q23
- **Question:** How do you implement horizontal pod autoscaling (HPA) for a Python service?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```yaml
  apiVersion: autoscaling/v2
  kind: HorizontalPodAutoscaler
  metadata:
    name: claims-api-hpa
  spec:
    scaleTargetRef:
      apiVersion: apps/v1
      kind: Deployment
      name: claims-api
    minReplicas: 2
    maxReplicas: 20
    metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 80
  ```
- **Answer Points:** Python is single-threaded by default — CPU autoscaling works well; also scale on custom metrics (queue depth, request rate)
- **Difficulty:** Medium

---

### Q24
- **Question:** What is a Kubernetes ConfigMap vs Secret? How do you use them in Python?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - ConfigMap: non-sensitive config (feature flags, timeouts, log level)
  - Secret: sensitive data (DB passwords, API keys, certs); base64 encoded (not encrypted by default — use Sealed Secrets or external secret manager)
  - Mount as env vars or as files
  - Python reads via `os.environ.get()` or `pydantic_settings.BaseSettings`
- **Difficulty:** Easy

---

### Q25–Q45 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 25 | What is a Kubernetes Service? Difference between ClusterIP, NodePort, LoadBalancer? | 8–12 Yrs | High | T1 |
| 26 | What is an Ingress controller? How do you route traffic to multiple Python services? | 8–12 Yrs | High | T1 |
| 27 | How do you implement rolling updates for a Python service in Kubernetes? | 8–12 Yrs | High | T1 |
| 28 | What is a Pod Disruption Budget? When is it important for Python services? | 8–12 Yrs | Medium | T2 |
| 29 | How do you handle database migrations in Kubernetes for a Python app? | 8–12 Yrs | High | T1 |
| 30 | How do you implement a CronJob in Kubernetes for a Python batch job? | 5–8 Yrs | High | T1 |
| 31 | How do you use init containers to wait for dependencies before a Python service starts? | 8–12 Yrs | Medium | T2 |
| 32 | How do you implement blue-green deployment in Kubernetes? | 8–12 Yrs | Medium | T2 |
| 33 | How do you implement canary deployment in Kubernetes for a Python service? | 8–12 Yrs | Medium | T2 |
| 34 | How do you set up Kubernetes RBAC for a Python service's service account? | 8–12 Yrs | Medium | T2 |
| 35 | What are Kubernetes resource requests vs limits? How do you size them for Python? | 8–12 Yrs | High | T1 |
| 36 | How do you implement Kubernetes Secrets with AWS Secrets Manager / Azure Key Vault? | 8–12 Yrs | High | T1 |
| 37 | How do you troubleshoot a CrashLoopBackOff for a Python pod? | 8–12 Yrs | High | T1 |
| 38 | How do you use Kubernetes Jobs for one-time Python tasks? | 5–8 Yrs | High | T1 |
| 39 | What is `kubectl exec` used for? How do you debug a running Python pod? | 5–8 Yrs | High | T1 |
| 40 | How do you handle timezone in Kubernetes for Python CronJobs? | 5–8 Yrs | Medium | T2 |
| 41 | How do you configure readiness gates for a Python service with external warmup? | 12–15 Yrs | Low | T3 |
| 42 | What is Helm? How do you package a Python service as a Helm chart? | 8–12 Yrs | Medium | T2 |
| 43 | How do you implement namespace isolation for a multi-tenant Python platform? | 12–15 Yrs | Medium | T2 |
| 44 | How does Kubernetes handle SIGTERM for graceful shutdown of Python? | 8–12 Yrs | High | T1 |
| 45 | How do you set up network policies to restrict traffic between Python microservices? | 12–15 Yrs | Medium | T2 |

---

## SECTION 3: AWS / Azure for Python (Q46–Q70)

### Q46
- **Question:** How do you use `boto3` to interact with AWS services in Python? Give examples.
- **Level:** 5–8 Yrs | **Company:** AWS shops | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import boto3

  # S3
  s3 = boto3.client('s3')
  s3.upload_file('local_file.csv', 'my-bucket', 'data/file.csv')
  obj = s3.get_object(Bucket='my-bucket', Key='data/file.csv')
  content = obj['Body'].read()

  # SQS
  sqs = boto3.client('sqs')
  response = sqs.send_message(QueueUrl=QUEUE_URL, MessageBody=json.dumps(data))
  messages = sqs.receive_message(QueueUrl=QUEUE_URL, MaxNumberOfMessages=10)

  # DynamoDB
  dynamodb = boto3.resource('dynamodb')
  table = dynamodb.Table('claims')
  table.put_item(Item={'claim_id': 'C001', 'status': 'pending'})
  ```
- **Difficulty:** Easy

---

### Q47
- **Question:** How do you write a Python Lambda function? What are best practices?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import json
  import logging

  logger = logging.getLogger()
  logger.setLevel(logging.INFO)

  def lambda_handler(event, context):
      """
      event: dict containing trigger-specific data
      context: runtime info (remaining_time_in_millis, function_name, etc.)
      """
      try:
          logger.info(f"Event: {json.dumps(event)}")
          # Process
          result = process_event(event)
          return {
              'statusCode': 200,
              'body': json.dumps(result)
          }
      except ValueError as e:
          logger.error(f"Validation error: {e}")
          return {'statusCode': 400, 'body': str(e)}
      except Exception as e:
          logger.exception("Unhandled error")
          raise  # Lambda will retry if configured
  ```
- **Answer Points:** Keep handler thin; import expensive libs at module level (reuse across invocations); use layers for large dependencies; `context.get_remaining_time_in_millis()` for timeout awareness
- **Difficulty:** Easy

---

### Q48
- **Question:** How do you optimize Python Lambda cold start time?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Minimize package size — Lambda layers for common libs; exclude unused packages
  - Lazy imports inside handler for rarely used modules
  - Provisioned concurrency: keeps instances warm (cost tradeoff)
  - Use Python 3.11+ (faster runtime startup)
  - Keep deployment package < 50MB (unzipped); use S3 for larger packages
  - Use ARM64 (Graviton2) — up to 20% cheaper + often faster cold start
- **Difficulty:** Medium

---

### Q49
- **Question:** How do you process SQS messages in Python with error handling and DLQ?
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def lambda_handler(event, context):
      failures = []
      for record in event['Records']:
          try:
              message = json.loads(record['body'])
              process_message(message)
          except Exception as e:
              logger.error(f"Failed to process {record['messageId']}: {e}")
              failures.append({'itemIdentifier': record['messageId']})

      # Return failed items for SQS partial batch response
      # SQS will retry only failed messages; successful ones deleted
      return {'batchItemFailures': failures}
  ```
- **Answer Points:** Enable `ReportBatchItemFailures`; configure DLQ with `maxReceiveCount=3`; always log message ID with failure
- **Difficulty:** Medium

---

### Q50–Q70 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 50 | How do you use AWS Step Functions with Python Lambda? | 8–12 Yrs | Medium | T2 |
| 51 | How do you implement cross-account AWS access in Python with `boto3`? | 8–12 Yrs | Medium | T2 |
| 52 | How do you use AWS Parameter Store vs Secrets Manager in Python? | 5–8 Yrs | High | T1 |
| 53 | How do you write to DynamoDB efficiently from Python? Batch writes? | 8–12 Yrs | High | T1 |
| 54 | How does S3 event notification trigger a Python Lambda? | 5–8 Yrs | High | T1 |
| 55 | How do you use AWS Glue with Python for ETL? | 8–12 Yrs | Medium | T2 |
| 56 | How do you use `azure-sdk-for-python`? How does authentication work? | 5–8 Yrs | High | T1 |
| 57 | How do you write an Azure Function in Python? What triggers are available? | 5–8 Yrs | High | T1 |
| 58 | How do you use Azure Service Bus with Python? | 8–12 Yrs | High | T1 |
| 59 | How do you interact with Azure Blob Storage from Python? | 5–8 Yrs | High | T1 |
| 60 | How do you use Azure OpenAI vs OpenAI API in Python? Key differences? | 8–12 Yrs | High | T1 |
| 61 | How do you use `DefaultAzureCredential` for authentication in Python? | 8–12 Yrs | High | T1 |
| 62 | How do you deploy a Python FastAPI to Azure App Service or AKS? | 8–12 Yrs | High | T1 |
| 63 | How does Azure Key Vault Python SDK work for secrets retrieval? | 8–12 Yrs | High | T1 |
| 64 | How do you process Azure Event Hub messages with Python? | 8–12 Yrs | Medium | T2 |
| 65 | How do you use Azure Document Intelligence (Form Recognizer) in Python? | 8–12 Yrs | High | T1 |
| 66 | How do you implement Azure Durable Functions in Python for long workflows? | 12–15 Yrs | Medium | T2 |
| 67 | How do you use GCP Pub/Sub with Python? | 8–12 Yrs | Medium | T2 |
| 68 | How do you use Terraform with Python (Pulumi, CDK for Terraform)? | 8–12 Yrs | Medium | T2 |
| 69 | How do you implement infrastructure-as-code for a Python service on AWS? | 8–12 Yrs | Medium | T2 |
| 70 | How do you handle IAM permissions for a Python service accessing multiple AWS services? | 8–12 Yrs | High | T1 |

---

## SECTION 4: CI/CD & DevOps (Q71–Q100)

### Q71
- **Question:** How do you set up CI/CD for a Python FastAPI service? Walk through a GitHub Actions pipeline.
- **Level:** 8–12 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```yaml
  name: CI/CD Pipeline
  on:
    push:
      branches: [main]
    pull_request:
      branches: [main]

  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - run: pip install -r requirements-dev.txt
      - run: ruff check .
      - run: mypy app/
      - run: pytest --cov=app --cov-report=xml
      - uses: codecov/codecov-action@v3

    build-push:
      needs: test
      runs-on: ubuntu-latest
      if: github.ref == 'refs/heads/main'
      steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - run: docker build -t ghcr.io/${{ github.repository }}:${{ github.sha }} .
      - run: docker push ghcr.io/${{ github.repository }}:${{ github.sha }}

    deploy:
      needs: build-push
      runs-on: ubuntu-latest
      steps:
      - run: kubectl set image deployment/claims-api claims-api=ghcr.io/${{ github.repository }}:${{ github.sha }}
  ```
- **Difficulty:** Medium

---

### Q72
- **Question:** What linting and formatting tools do you use for Python in CI?
- **Level:** 5–8 Yrs | **Company:** All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `ruff` — fast linter + formatter (replaces `flake8`, `isort`, `pyupgrade`, `black`)
  - `black` — code formatter (opinionated, no config needed)
  - `mypy` or `pyright` — type checking
  - `bandit` — security vulnerability scanning
  - `safety` — check for known vulnerable dependencies
  - `pytest-cov` — test coverage
  - Run all in CI pre-merge gate
- **Difficulty:** Easy

---

### Q73–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 73 | How do you implement pre-commit hooks for Python code quality? | 5–8 Yrs | High | T1 |
| 74 | How do you manage Python dependencies securely? (`pip-audit`, `safety`) | 5–8 Yrs | High | T1 |
| 75 | How do you implement semantic versioning and changelog for a Python service? | 8–12 Yrs | Medium | T2 |
| 76 | How do you implement infrastructure testing for Python services? | 8–12 Yrs | Medium | T2 |
| 77 | How do you run integration tests in CI against real databases? | 8–12 Yrs | High | T1 |
| 78 | How do you set up a Python monorepo with multiple services? | 12–15 Yrs | Medium | T2 |
| 79 | How do you implement a release pipeline with rollback capability? | 8–12 Yrs | High | T1 |
| 80 | How do you implement GitOps for deploying Python services to Kubernetes? | 8–12 Yrs | Medium | T2 |
| 81 | How do you scan Python Docker images in CI? (`trivy`, `snyk`) | 8–12 Yrs | Medium | T2 |
| 82 | How do you manage database migration as part of a CI/CD pipeline? | 8–12 Yrs | High | T1 |
| 83 | How do you implement contract testing between Python microservices? | 12–15 Yrs | Medium | T2 |
| 84 | How do you implement load testing in CI using `locust`? | 8–12 Yrs | Medium | T2 |
| 85 | How do you use `pytest-xdist` to parallelize tests in CI? | 8–12 Yrs | Medium | T2 |
| 86 | How do you implement a feature branch deployment strategy? | 8–12 Yrs | Medium | T2 |
| 87 | How do you handle environment-specific configs in a CI/CD pipeline? | 5–8 Yrs | High | T1 |
| 88 | How do you implement automated performance regression testing for a Python API? | 12–15 Yrs | Medium | T2 |
| 89 | How do you implement compliance checks (HIPAA, SOC2) in CI for Python? | 12–15 Yrs | Medium | T2 |
| 90 | How do you implement SBOM (Software Bill of Materials) for a Python service? | 12–15 Yrs | Low | T3 |
| 91 | How do you monitor CI pipeline health and failure rates? | 8–12 Yrs | Medium | T2 |
| 92 | How do you implement secret scanning in Python repos? | 8–12 Yrs | High | T1 |
| 93 | How do you implement a Dependabot-style automated dependency update workflow? | 8–12 Yrs | Medium | T2 |
| 94 | How do you use `pyproject.toml` for a complete Python project setup? | 5–8 Yrs | Medium | T2 |
| 95 | How do you build and publish a Python package to PyPI in CI? | 8–12 Yrs | Medium | T2 |
| 96 | How do you implement A/B traffic splitting in a Kubernetes + Python deployment? | 12–15 Yrs | Medium | T2 |
| 97 | How do you implement smoke tests post-deployment for a Python service? | 8–12 Yrs | High | T1 |
| 98 | How do you implement chaos engineering tests for a Python service? | 12–15 Yrs | Low | T3 |
| 99 | How do you implement a self-service developer platform for Python microservices? | 12–15 Yrs | Low | T3 |
| 100 | Design a complete DevOps pipeline for a Python GenAI platform: CI, Docker, K8s, secrets, observability, canary deployments, and rollback. | 12–15 Yrs | High | T1 |
