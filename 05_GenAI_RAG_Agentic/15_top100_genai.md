# Top 100 GenAI Python Interview Questions
> Focus: 8–15 Years | Non-FAANG | GenAI Companies, Deloitte, Accenture, EPAM, Brillio, Healthcare AI
> Sources: Glassdoor, LinkedIn, Reddit, Medium, GenAI Engineer Interview Reports

---

## SECTION 1: LLM Fundamentals (Q1–Q20)

### Q1
- **Question:** What is a Large Language Model? How does it generate text?
- **Level:** 8–12 Yrs | **Company:** All GenAI companies | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Transformer-based model trained on massive text corpora with next-token prediction
  - Generates text autoregressively: predict next token given previous context
  - Temperature controls randomness; top-p/top-k controls sampling diversity
  - Context window: max tokens model can "see" at once (GPT-4o: 128K, Claude: 200K)
  - Not a database — doesn't "know" facts, probabilistically predicts likely tokens
- **Senior Answer:** Attention mechanism, KV cache, quantization tradeoffs, temperature=0 for deterministic output
- **Difficulty:** Medium

---

### Q2
- **Question:** What is the difference between `gpt-4o`, `gpt-4-turbo`, `gpt-3.5-turbo`, and embedding models?
- **Level:** 8–12 Yrs | **Company:** All GenAI companies | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - `gpt-4o`: multimodal (text+vision+audio), fast, cost-effective, 128K context
  - `gpt-4-turbo`: strong reasoning, 128K context, slower than gpt-4o
  - `gpt-3.5-turbo`: cheap, fast, good for simple tasks; quality lower than gpt-4
  - Embedding models (`text-embedding-3-small/large`): convert text to vector; NOT generative; used for similarity, RAG
  - Choose based on: task complexity, cost, latency, context needs
- **Difficulty:** Easy

---

### Q3
- **Question:** What is prompt engineering? Explain zero-shot, few-shot, and chain-of-thought prompting.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Zero-shot: no examples
  prompt = "Classify this claim as 'approved' or 'denied': {claim_description}"

  # Few-shot: provide examples
  prompt = """
  Classify insurance claims:
  Claim: "Routine checkup" → approved
  Claim: "Experimental treatment not in formulary" → denied
  Claim: "Emergency surgery for appendicitis" → approved
  Claim: "{claim_description}" →
  """

  # Chain-of-thought: ask for reasoning
  prompt = """
  Analyze this claim and explain step-by-step before giving a decision:
  Claim: {claim_description}
  Think step by step:
  1. What type of service is this?
  2. Is it covered by the plan?
  3. Are there any exclusions?
  Decision:
  """
  ```
- **Answer Points:** CoT dramatically improves accuracy on multi-step reasoning tasks; few-shot helps with formatting consistency; zero-shot simpler but less reliable
- **Difficulty:** Medium

---

### Q4
- **Question:** What are hallucinations in LLMs? How do you reduce them in production?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Hallucinations: LLM confidently generates false/fabricated information
  - Types: factual errors, made-up citations, outdated information, wrong reasoning
  - Mitigation:
    1. RAG: ground LLM in retrieved verified documents
    2. Structured output + validation: use Pydantic to validate LLM output
    3. Grounding checks: verify claims against known-good data
    4. Temperature=0: reduces randomness (not full solution)
    5. Self-consistency: run 3 times, majority vote
    6. Human-in-the-loop for high-stakes decisions (healthcare, legal)
- **Architect Answer:** Multi-stage validation pipeline; confidence scoring; fallback to human review when uncertainty high
- **Difficulty:** Medium

---

### Q5
- **Question:** How do you call the OpenAI API in Python? Show a complete example with error handling.
- **Level:** 5–8 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from openai import AsyncOpenAI
  import asyncio

  client = AsyncOpenAI(api_key=os.getenv("OPENAI_API_KEY"))

  async def chat_completion(messages: list[dict], model: str = "gpt-4o") -> str:
      try:
          response = await client.chat.completions.create(
              model=model,
              messages=messages,
              temperature=0.1,
              max_tokens=2000,
              timeout=30.0
          )
          return response.choices[0].message.content
      except openai.RateLimitError as e:
          logger.warning(f"Rate limited: {e}")
          await asyncio.sleep(60)
          raise
      except openai.APITimeoutError as e:
          logger.error(f"Timeout: {e}")
          raise
      except openai.APIError as e:
          logger.error(f"API error: {e}")
          raise
  ```
- **Follow-Ups:** How do you implement retry with exponential backoff for rate limits?
- **Difficulty:** Easy

---

### Q6
- **Question:** How do you implement streaming responses from the OpenAI API in Python?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  async def stream_chat(prompt: str):
      stream = await client.chat.completions.create(
          model="gpt-4o",
          messages=[{"role": "user", "content": prompt}],
          stream=True
      )
      async for chunk in stream:
          content = chunk.choices[0].delta.content
          if content is not None:
              yield content
              # Or: print(content, end="", flush=True)

  # In FastAPI
  @app.post("/chat/stream")
  async def chat_stream(request: ChatRequest):
      return StreamingResponse(
          stream_chat(request.prompt),
          media_type="text/plain"
      )
  ```
- **Difficulty:** Medium

---

### Q7
- **Question:** What is structured output in OpenAI API? How is it different from JSON mode?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel

  class ClaimDecision(BaseModel):
      decision: Literal["approved", "denied", "pending_review"]
      reasoning: str
      confidence: float
      required_docs: list[str]

  # Structured output (strict schema enforcement — June 2024+)
  response = await client.beta.chat.completions.parse(
      model="gpt-4o",
      messages=[{"role": "user", "content": prompt}],
      response_format=ClaimDecision
  )
  claim = response.choices[0].message.parsed  # ClaimDecision instance

  # JSON mode (just instructs to output JSON, less strict)
  response = await client.chat.completions.create(
      model="gpt-4o",
      messages=[{"role": "user", "content": prompt}],
      response_format={"type": "json_object"}
  )
  ```
- **Difficulty:** Medium

---

### Q8
- **Question:** How do you handle token limits? What happens when your prompt exceeds the context window?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import tiktoken

  def count_tokens(text: str, model: str = "gpt-4o") -> int:
      enc = tiktoken.encoding_for_model(model)
      return len(enc.encode(text))

  def truncate_to_token_limit(text: str, max_tokens: int, model: str = "gpt-4o") -> str:
      enc = tiktoken.encoding_for_model(model)
      tokens = enc.encode(text)
      if len(tokens) <= max_tokens:
          return text
      return enc.decode(tokens[:max_tokens])
  ```
- **Answer Points:** Always reserve tokens for: system prompt + user query + response buffer; for long documents use RAG (chunk + retrieve) instead of stuffing full doc into context
- **Difficulty:** Medium

---

### Q9
- **Question:** What is function calling / tool calling in LLMs? How do you implement it?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  tools = [
      {
          "type": "function",
          "function": {
              "name": "check_eligibility",
              "description": "Check if a member is eligible for a specific service",
              "parameters": {
                  "type": "object",
                  "properties": {
                      "member_id": {"type": "string"},
                      "service_code": {"type": "string"},
                      "service_date": {"type": "string", "format": "date"}
                  },
                  "required": ["member_id", "service_code"]
              }
          }
      }
  ]

  response = await client.chat.completions.create(
      model="gpt-4o",
      messages=messages,
      tools=tools,
      tool_choice="auto"
  )

  # Check if LLM called a tool
  if response.choices[0].finish_reason == "tool_calls":
      tool_call = response.choices[0].message.tool_calls[0]
      args = json.loads(tool_call.function.arguments)
      result = await check_eligibility(**args)
      # Add tool result to messages and continue
  ```
- **Difficulty:** Hard

---

### Q10–Q20 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 10 | What is system prompt? How do you design effective system prompts? | 5–8 Yrs | High | T1 |
| 11 | How do you implement retry logic for LLM API calls with rate limit handling? | 5–8 Yrs | High | T1 |
| 12 | What is `temperature`, `top_p`, `top_k`, `frequency_penalty`? How do they affect output? | 5–8 Yrs | High | T1 |
| 13 | How do you batch multiple prompts efficiently to an LLM API? | 8–12 Yrs | High | T1 |
| 14 | What is the difference between Azure OpenAI and OpenAI API? Enterprise considerations? | 8–12 Yrs | High | T1 |
| 15 | How do you implement LLM fallback (e.g., GPT-4 → GPT-3.5 on failure)? | 8–12 Yrs | High | T1 |
| 16 | What is prompt injection? How do you prevent it in a Python LLM service? | 8–12 Yrs | High | T1 |
| 17 | How do you implement cost tracking for LLM API calls in Python? | 8–12 Yrs | High | T1 |
| 18 | What is `logprobs`? How do you use it for confidence scoring? | 12–15 Yrs | Medium | T2 |
| 19 | How do you parse LLM output that contains markdown, code blocks, or mixed content? | 8–12 Yrs | High | T1 |
| 20 | How do you implement a prompt template library for a team of GenAI engineers? | 12–15 Yrs | High | T1 |

---

## SECTION 2: Embeddings & Similarity (Q21–Q40)

### Q21
- **Question:** What is a vector embedding? How does it represent semantic meaning?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Dense numerical vector (e.g., 1536 dimensions for text-embedding-3-small)
  - Semantically similar texts have similar vectors (close in vector space)
  - Generated by encoder model trained on massive corpus
  - Used for: semantic search, RAG retrieval, clustering, classification, deduplication
- **Code:**
  ```python
  response = await client.embeddings.create(
      model="text-embedding-3-small",
      input=["Patient has type 2 diabetes", "Diabetic patient, insulin dependent"]
  )
  embeddings = [e.embedding for e in response.data]
  similarity = np.dot(embeddings[0], embeddings[1])  # cosine similarity (normalized)
  ```
- **Difficulty:** Medium

---

### Q22
- **Question:** How do you compute cosine similarity between two embeddings in Python?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  import numpy as np

  def cosine_similarity(a: list[float], b: list[float]) -> float:
      a, b = np.array(a), np.array(b)
      return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

  # Or using sklearn
  from sklearn.metrics.pairwise import cosine_similarity
  sim = cosine_similarity([embedding1], [embedding2])[0][0]
  ```
- **Answer Points:** OpenAI embeddings are already normalized → dot product = cosine similarity; threshold ~0.85 for near-duplicate text
- **Difficulty:** Easy

---

### Q23
- **Question:** What are vector databases? Compare Pinecone, Chroma, Weaviate, Azure AI Search, Qdrant, pgvector.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Chroma**: open source, easy local dev, Python-native, good for prototyping
  - **Qdrant**: open source, Rust-based, fast, good filtering, production-ready
  - **Pinecone**: managed, serverless tier, no infra management, expensive at scale
  - **Weaviate**: open source, hybrid search built-in, GraphQL API
  - **Azure AI Search**: managed, hybrid search, integrates with Azure OpenAI, RBAC
  - **pgvector**: PostgreSQL extension, no new infra if already using Postgres
- **When to choose:** Prototyping → Chroma; Production managed → Qdrant/Pinecone; Enterprise Azure → Azure AI Search; Existing PG → pgvector
- **Difficulty:** Medium

---

### Q24
- **Question:** How do you store and query embeddings in pgvector with Python?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from sqlalchemy import Column, Integer, Text
  from pgvector.sqlalchemy import Vector
  from sqlalchemy.ext.declarative import declarative_base

  Base = declarative_base()

  class Document(Base):
      __tablename__ = "documents"
      id = Column(Integer, primary_key=True)
      content = Column(Text)
      embedding = Column(Vector(1536))  # text-embedding-3-small dimension

  # Insert
  doc = Document(content=text, embedding=embedding)
  session.add(doc)

  # Similarity search
  results = session.execute(
      select(Document)
      .order_by(Document.embedding.cosine_distance(query_embedding))
      .limit(5)
  ).scalars().all()
  ```
- **Difficulty:** Medium

---

### Q25–Q40 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 25 | How do you embed and upsert documents into Qdrant with Python? | 8–12 Yrs | High | T1 |
| 26 | What is HNSW index? How does it enable fast approximate nearest neighbor search? | 12–15 Yrs | Medium | T2 |
| 27 | What is the difference between dense and sparse embeddings? | 8–12 Yrs | High | T1 |
| 28 | How do you handle multilingual embeddings? Which models support them? | 8–12 Yrs | Medium | T2 |
| 29 | How do you update an embedding when the underlying document changes? | 8–12 Yrs | High | T1 |
| 30 | How do you batch embed thousands of documents efficiently? | 8–12 Yrs | High | T1 |
| 31 | What is `FastEmbed`? How does it differ from calling the OpenAI embedding API? | 8–12 Yrs | Medium | T2 |
| 32 | How do you implement semantic deduplication using embeddings? | 8–12 Yrs | Medium | T2 |
| 33 | How do you cluster documents using embeddings? (k-means, UMAP + HDBSCAN) | 8–12 Yrs | Medium | T2 |
| 34 | How do you evaluate embedding quality for a domain-specific corpus? | 12–15 Yrs | Medium | T2 |
| 35 | What is `Matryoshka` embedding? How does it enable flexible dimension truncation? | 12–15 Yrs | Low | T3 |
| 36 | How do you implement a semantic cache using embeddings + Redis? | 8–12 Yrs | High | T1 |
| 37 | How do you use embeddings for anomaly detection in claim descriptions? | 8–12 Yrs | Medium | T2 |
| 38 | How does `ColBERT` differ from standard bi-encoder models for retrieval? | 12–15 Yrs | Low | T3 |
| 39 | How do you store embeddings cost-effectively at scale (millions of documents)? | 12–15 Yrs | Medium | T2 |
| 40 | How do you implement namespace/tenant isolation in a vector database? | 12–15 Yrs | High | T1 |

---

## SECTION 3: LLM Integration Patterns (Q41–Q70)

### Q41
- **Question:** What is a prompt template? How do you implement a version-controlled prompt library?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel
  from string import Template

  class PromptTemplate(BaseModel):
      name: str
      version: str
      template: str
      variables: list[str]

      def render(self, **kwargs) -> str:
          missing = set(self.variables) - set(kwargs.keys())
          if missing:
              raise ValueError(f"Missing variables: {missing}")
          return Template(self.template).safe_substitute(**kwargs)

  CLAIM_ANALYSIS_PROMPT = PromptTemplate(
      name="claim_analysis",
      version="1.3.0",
      template="Analyze this $claim_type claim for member $member_id:\n$claim_text",
      variables=["claim_type", "member_id", "claim_text"]
  )
  ```
- **Difficulty:** Medium

---

### Q42
- **Question:** How do you implement multi-turn conversation memory in a Python LLM service?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from redis import asyncio as aioredis
  import json

  async def get_conversation_history(redis, session_id: str, max_messages: int = 20) -> list[dict]:
      messages = await redis.lrange(f"conv:{session_id}", -max_messages, -1)
      return [json.loads(m) for m in messages]

  async def add_message(redis, session_id: str, role: str, content: str, ttl: int = 3600):
      message = json.dumps({"role": role, "content": content})
      await redis.rpush(f"conv:{session_id}", message)
      await redis.expire(f"conv:{session_id}", ttl)

  async def chat_with_memory(session_id: str, user_message: str) -> str:
      history = await get_conversation_history(redis, session_id)
      messages = history + [{"role": "user", "content": user_message}]
      response_text = await chat_completion(messages)
      await add_message(redis, session_id, "user", user_message)
      await add_message(redis, session_id, "assistant", response_text)
      return response_text
  ```
- **Difficulty:** Medium

---

### Q43
- **Question:** How do you implement guardrails for LLM outputs? (safety, format, policy)
- **Level:** 8–12 Yrs | **Company:** Healthcare GenAI, All | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Input guardrails:** detect and block harmful prompts; PII masking before sending to LLM
  - **Output guardrails:** validate format (Pydantic); check for PII in response; policy compliance check
  - **Tools:** `guardrails-ai`, `nemoguardrails` (NVIDIA), `llama-guard`
  - **Healthcare:** never output PHI; always include disclaimer for medical info; confidence thresholds
  - **Implementation:** validator chain — if output fails validation, retry with corrective instructions
- **Difficulty:** Hard

---

### Q44
- **Question:** How do you implement semantic caching for LLM calls to reduce cost and latency?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  class SemanticCache:
      def __init__(self, redis_client, embed_func, similarity_threshold: float = 0.95):
          self.redis = redis_client
          self.embed = embed_func
          self.threshold = similarity_threshold

      async def get(self, query: str) -> str | None:
          query_emb = await self.embed(query)
          # Search Redis for similar cached queries
          cached_keys = await self.redis.keys("cache:*")
          for key in cached_keys:
              data = json.loads(await self.redis.get(key))
              sim = cosine_similarity(query_emb, data['embedding'])
              if sim >= self.threshold:
                  return data['response']
          return None

      async def set(self, query: str, response: str, ttl: int = 3600):
          query_emb = await self.embed(query)
          key = f"cache:{hashlib.sha256(query.encode()).hexdigest()[:16]}"
          await self.redis.setex(key, ttl, json.dumps({'embedding': query_emb, 'response': response}))
  ```
- **Difficulty:** Hard

---

### Q45–Q70 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 45 | What is the `Instructor` library? How does it enforce Pydantic output from LLMs? | 8–12 Yrs | Medium | T2 |
| 46 | How do you implement a fallback chain: try GPT-4 → GPT-3.5 → rule-based? | 8–12 Yrs | High | T1 |
| 47 | How do you implement LLM response caching with TTL and invalidation? | 8–12 Yrs | High | T1 |
| 48 | How do you manage LLM context window for long documents? | 8–12 Yrs | High | T1 |
| 49 | What is self-consistency prompting? How does it improve LLM accuracy? | 8–12 Yrs | Medium | T2 |
| 50 | How do you implement a classification pipeline using an LLM with confidence scores? | 8–12 Yrs | High | T1 |
| 51 | How do you implement a Python LLM service that handles 1000 concurrent requests? | 12–15 Yrs | High | T1 |
| 52 | How do you implement async parallel LLM calls for batch processing? | 8–12 Yrs | High | T1 |
| 53 | How do you handle LLM context poisoning attacks in a multi-user system? | 12–15 Yrs | Medium | T2 |
| 54 | How do you implement a "chain of thought with verification" pipeline? | 12–15 Yrs | Medium | T2 |
| 55 | How do you extract structured data from unstructured LLM responses? | 8–12 Yrs | High | T1 |
| 56 | How do you implement a Python GenAI service with Azure OpenAI and fallback to OpenAI? | 8–12 Yrs | High | T1 |
| 57 | What is `Outlines` library? How does grammar-constrained generation work? | 12–15 Yrs | Low | T3 |
| 58 | How do you implement LLM load balancing across multiple API keys? | 12–15 Yrs | Medium | T2 |
| 59 | How do you implement document extraction using LLM + Pydantic? | 8–12 Yrs | High | T1 |
| 60 | How do you implement a Python tool that automatically generates test data using LLMs? | 8–12 Yrs | Medium | T2 |
| 61 | What is ReAct prompting? How does it combine reasoning and tool use? | 8–12 Yrs | High | T1 |
| 62 | How do you implement a Python router that sends queries to different LLMs based on type? | 8–12 Yrs | High | T1 |
| 63 | How do you handle LLM non-determinism in automated testing? | 8–12 Yrs | Medium | T2 |
| 64 | How do you implement an LLM-based data extraction pipeline for insurance forms? | 8–12 Yrs | High | T1 |
| 65 | How do you implement multi-modal input (text + image) in a Python LLM service? | 8–12 Yrs | High | T1 |
| 66 | What is `litellm`? How does it simplify switching between LLM providers? | 8–12 Yrs | Medium | T2 |
| 67 | How do you implement feedback collection from LLM outputs for fine-tuning? | 12–15 Yrs | Medium | T2 |
| 68 | How do you implement HIPAA-compliant LLM pipelines for healthcare? | 12–15 Yrs | High | T1 |
| 69 | What is LLM-as-a-judge? How do you implement it for automated quality evaluation? | 12–15 Yrs | Medium | T2 |
| 70 | How do you implement a Python service that summarizes clinical notes with LLMs? | 8–12 Yrs | High | T1 |

---

## SECTION 4: Fine-tuning & Advanced Topics (Q71–Q100)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 71 | What is fine-tuning? When do you choose fine-tuning vs RAG vs prompt engineering? | 12–15 Yrs | High | T1 |
| 72 | What is LoRA? How does it reduce memory requirements for fine-tuning? | 12–15 Yrs | Medium | T2 |
| 73 | What is QLoRA? How does it combine quantization and LoRA? | 12–15 Yrs | Medium | T2 |
| 74 | How do you fine-tune a model using OpenAI's fine-tuning API in Python? | 12–15 Yrs | Medium | T2 |
| 75 | What is RLHF? Explain PPO and DPO at a conceptual level. | 12–15 Yrs | Low | T3 |
| 76 | What is SFT (Supervised Fine-Tuning)? How do you prepare training data? | 12–15 Yrs | Medium | T2 |
| 77 | What is `Hugging Face Transformers`? How do you run inference in Python? | 8–12 Yrs | High | T1 |
| 78 | How do you use `peft` library for parameter-efficient fine-tuning? | 12–15 Yrs | Low | T3 |
| 79 | How do you use `trl` library (Transformer Reinforcement Learning)? | 12–15 Yrs | Low | T3 |
| 80 | What is model distillation? How would you distill GPT-4 outputs for a smaller model? | 12–15 Yrs | Low | T3 |
| 81 | What is `vLLM`? How does it enable high-throughput LLM serving? | 12–15 Yrs | Medium | T2 |
| 82 | What is quantization? Compare INT8, INT4, AWQ, GPTQ for model serving. | 12–15 Yrs | Low | T3 |
| 83 | How do you deploy an open-source LLM (Llama 3) for production inference in Python? | 12–15 Yrs | Medium | T2 |
| 84 | What is `Ollama`? How do you use it for local LLM inference in Python? | 8–12 Yrs | Medium | T2 |
| 85 | How do you implement evaluation of fine-tuned models? What metrics matter? | 12–15 Yrs | Medium | T2 |
| 86 | How do you prevent catastrophic forgetting during fine-tuning? | 12–15 Yrs | Low | T3 |
| 87 | What is continual pre-training vs instruction tuning vs fine-tuning? | 12–15 Yrs | Low | T3 |
| 88 | How do you build a synthetic dataset for fine-tuning using GPT-4? | 12–15 Yrs | Medium | T2 |
| 89 | What is prompt compression? How does it reduce token cost? | 12–15 Yrs | Low | T3 |
| 90 | How do you implement a Python pipeline for automated LLM evaluation? | 12–15 Yrs | High | T1 |
| 91 | What is `DeepSpeed`? How does it enable training large models on limited hardware? | 12–15 Yrs | Low | T3 |
| 92 | How do you implement model A/B testing for two different LLMs in Python? | 12–15 Yrs | High | T1 |
| 93 | What is context distillation? How does it compress few-shot examples into weights? | 12–15 Yrs | Low | T3 |
| 94 | How do you implement multi-modal fine-tuning for document + text tasks? | 12–15 Yrs | Low | T3 |
| 95 | How do you implement Python-based LLM red teaming? | 12–15 Yrs | Medium | T2 |
| 96 | What is adversarial prompt testing? How do you automate it? | 12–15 Yrs | Medium | T2 |
| 97 | How do you build a HIPAA-compliant fine-tuning pipeline for clinical NLP? | 12–15 Yrs | High | T1 |
| 98 | How do you handle data privacy during fine-tuning (PII scrubbing, differential privacy)? | 12–15 Yrs | High | T1 |
| 99 | How do you implement a Python CI pipeline for LLM evaluation on every code change? | 12–15 Yrs | High | T1 |
| 100 | Design a complete GenAI platform for healthcare: LLM fine-tuned on clinical data + RAG + agentic workflows + LLMOps + HIPAA compliance. | 12–15 Yrs | High | T1 |
