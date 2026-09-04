# Top Python Questions — GenAI / AI-First Company Roles
> Target: 8–15 Years | GenAI Startups, AI Labs, Deloitte AI, Accenture GenAI, EPAM, Brillio, Persistent Systems AI
> Roles: GenAI Engineer, AI Engineer, LLM Engineer, ML Engineer, AI Architect, LLMOps Engineer
> Sources: Glassdoor, LinkedIn, Reddit, Medium, GenAI Interview Reports 2024–2025

---

## What GenAI Company Interviews Look Like

GenAI-focused companies test for:
- Deep LLM integration: OpenAI/Azure API, streaming, structured output, tool calling
- RAG pipeline end-to-end: chunking, embedding, hybrid search, reranking, evaluation
- Agentic AI: LangGraph, multi-agent patterns, HITL, state management
- LLMOps: observability (Langfuse), cost control, prompt versioning, evaluation pipelines
- MCP: building MCP servers and integrating with AI agents
- Python at senior/architect level: async, FastAPI, Pydantic, design patterns
- Fast, high-quality delivery: GenAI moves fast; they want engineers who ship and iterate

**Rounds:** System design (GenAI focused) → Live coding (Python + LLM) → LLM architecture deep dive → Culture fit

---

## SECTION 1: LLM Engineering (Q1–Q25)

### Q1
- **Question:** Walk me through how you would build a production LLM service from scratch. What does the full stack look like?
- **Level:** 8–12 Yrs | **Tier:** T1
- **Answer Points:**
  - **API layer:** FastAPI; async endpoints; JWT auth; rate limiting (Redis); request validation (Pydantic)
  - **LLM integration:** `openai` async SDK; model fallback chain; retry with backoff; streaming support
  - **Prompt management:** versioned prompt registry (YAML + DB); A/B testing capability
  - **Caching:** semantic cache (Redis + embeddings) for common queries; TTL-based invalidation
  - **Observability:** Langfuse traces for every LLM call; cost per request; latency p50/p95/p99
  - **Evaluation:** nightly RAGAS / LLM-judge pipeline against golden dataset; alert on degradation
  - **Infrastructure:** Docker → Kubernetes; HPA on CPU/request rate; graceful shutdown
  - **Cost controls:** per-user/tenant token budget; auto-fallback to cheaper model at budget cap

---

### Q2
- **Question:** Implement an async LLM client with retry, timeout, fallback, and cost tracking.
- **Tier:** T1
- **Answer:**
  ```python
  import asyncio, time
  from openai import AsyncOpenAI
  from dataclasses import dataclass

  MODELS = [
      {"name": "gpt-4o", "input_cost": 0.0025, "output_cost": 0.010, "timeout": 30},
      {"name": "gpt-4o-mini", "input_cost": 0.00015, "output_cost": 0.0006, "timeout": 15},
  ]

  @dataclass
  class LLMResponse:
      content: str
      model: str
      input_tokens: int
      output_tokens: int
      cost_usd: float
      latency_ms: float

  async def call_llm(messages: list[dict], max_retries: int = 2) -> LLMResponse:
      client = AsyncOpenAI()
      for model_cfg in MODELS:
          for attempt in range(max_retries + 1):
              try:
                  start = time.perf_counter()
                  response = await asyncio.wait_for(
                      client.chat.completions.create(
                          model=model_cfg["name"],
                          messages=messages,
                          temperature=0.1
                      ),
                      timeout=model_cfg["timeout"]
                  )
                  latency = (time.perf_counter() - start) * 1000
                  usage = response.usage
                  cost = (usage.prompt_tokens / 1000 * model_cfg["input_cost"] +
                          usage.completion_tokens / 1000 * model_cfg["output_cost"])
                  return LLMResponse(
                      content=response.choices[0].message.content,
                      model=model_cfg["name"],
                      input_tokens=usage.prompt_tokens,
                      output_tokens=usage.completion_tokens,
                      cost_usd=cost,
                      latency_ms=latency
                  )
              except (asyncio.TimeoutError, Exception) as e:
                  if attempt == max_retries:
                      break
                  await asyncio.sleep(2 ** attempt)
      raise RuntimeError("All LLM models exhausted")
  ```

---

### Q3
- **Question:** How do you implement structured output extraction from an LLM with validation and retry?
- **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel, ValidationError
  from openai import AsyncOpenAI

  class ClaimDecision(BaseModel):
      decision: Literal["approved", "denied", "pending_review"]
      reasoning: str
      confidence: float
      icd_codes: list[str]

  async def extract_structured(prompt: str, max_retries: int = 3) -> ClaimDecision:
      client = AsyncOpenAI()
      for attempt in range(max_retries):
          try:
              response = await client.beta.chat.completions.parse(
                  model="gpt-4o",
                  messages=[{"role": "user", "content": prompt}],
                  response_format=ClaimDecision
              )
              return response.choices[0].message.parsed
          except ValidationError as e:
              # Add corrective instruction and retry
              prompt += f"\n\nPrevious output was invalid: {e}. Please fix and retry."
              if attempt == max_retries - 1:
                  raise
  ```

---

### Q4–Q25 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 4 | How do you implement streaming from an LLM and forward it via FastAPI SSE? | LLM | T1 |
| 5 | How do you implement function/tool calling in a ReAct agent loop manually? | Agents | T1 |
| 6 | How do you implement semantic caching for LLM calls using Redis? | LLMOps | T1 |
| 7 | How do you implement LLM output guardrails (safety, format, policy)? | LLMOps | T1 |
| 8 | How do you handle token limits for a 100-page document sent to an LLM? | LLM | T1 |
| 9 | How do you implement multi-turn conversation memory using Redis? | LLM | T1 |
| 10 | How do you count tokens before making an LLM call? (`tiktoken`) | LLM | T1 |
| 11 | What is `litellm`? How does it simplify multi-provider LLM routing? | LLM | T1 |
| 12 | How do you implement LLM-as-a-judge for automated output evaluation? | LLMOps | T1 |
| 13 | How do you implement cost tracking + budget enforcement per user/tenant? | LLMOps | T1 |
| 14 | How do you implement A/B testing for two different LLM prompts in production? | LLMOps | T1 |
| 15 | What is `Instructor` library? How does it guarantee Pydantic output from LLMs? | LLM | T2 |
| 16 | How do you implement a prompt regression test that runs in CI? | LLMOps | T1 |
| 17 | How do you implement a shadow LLM deployment for quality comparison? | LLMOps | T1 |
| 18 | How do you implement async parallel LLM calls for batch document processing? | LLM | T1 |
| 19 | How do you implement a Python LLM gateway that enforces org-wide policies? | LLMOps | T1 |
| 20 | How do you implement prompt injection detection in a Python LLM service? | Security | T1 |
| 21 | How do you implement a Python service that handles 10K LLM requests/minute? | Scaling | T1 |
| 22 | How do you use `logprobs` for LLM confidence scoring? | LLM | T2 |
| 23 | How do you implement multi-modal LLM calls (text + image) in Python? | LLM | T1 |
| 24 | How do you implement a Python LLM service with Azure OpenAI + OpenAI fallback? | LLM | T1 |
| 25 | How do you implement response caching with semantic similarity threshold? | LLMOps | T1 |

---

## SECTION 2: RAG Engineering (Q26–Q50)

### Q26
- **Question:** Build a production RAG pipeline from scratch. What are the key design decisions?
- **Tier:** T1
- **Answer Points:**
  - **Chunking:** semantic chunking for coherent chunks; 400-token child + 2000-token parent (hierarchical)
  - **Embedding:** `text-embedding-3-small` (cost) or `text-embedding-3-large` (quality); local `FastEmbed` for privacy
  - **Vector DB:** Qdrant (self-hosted/cloud) for production; pgvector if already on Postgres
  - **Search:** hybrid search (dense + BM25 sparse) with RRF fusion; domain-specific terms benefit from BM25
  - **Reranking:** cross-encoder (`ms-marco-MiniLM`) to rerank top-20 → top-5
  - **Context assembly:** format chunks with source + section header; 3000 tokens for context
  - **LLM generation:** GPT-4o with structured output; include citations
  - **Evaluation:** RAGAS on golden dataset; nightly CI run; alert on faithfulness < 0.80

---

### Q27
- **Question:** How do you implement hybrid search with RRF in Python with Qdrant?
- **Tier:** T1
- **Answer:**
  ```python
  from qdrant_client import QdrantClient, models
  from fastembed import SparseTextEmbedding, TextEmbedding

  dense_model = TextEmbedding("BAAI/bge-small-en-v1.5")
  sparse_model = SparseTextEmbedding("Qdrant/bm25")

  async def hybrid_search(client: QdrantClient, query: str, k: int = 5) -> list:
      dense_vector = list(dense_model.embed([query]))[0].tolist()
      sparse_vector = list(sparse_model.embed([query]))[0]

      results = client.query_points(
          collection_name="documents",
          prefetch=[
              models.Prefetch(
                  query=dense_vector,
                  using="dense",
                  limit=20
              ),
              models.Prefetch(
                  query=models.SparseVector(
                      indices=sparse_vector.indices.tolist(),
                      values=sparse_vector.values.tolist()
                  ),
                  using="sparse",
                  limit=20
              )
          ],
          query=models.FusionQuery(fusion=models.Fusion.RRF),
          limit=k,
          with_payload=True
      )
      return results.points
  ```

---

### Q28–Q50 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 28 | How do you implement cross-encoder reranking in Python? | RAG | T1 |
| 29 | How do you implement HyDE for better retrieval on short queries? | RAG | T1 |
| 30 | How do you implement multi-query retrieval to improve recall? | RAG | T1 |
| 31 | How do you implement parent-document retrieval in Python without LangChain? | RAG | T1 |
| 32 | How do you evaluate a RAG pipeline using RAGAS? Show the code. | RAG | T1 |
| 33 | How do you implement citation extraction in RAG responses? | RAG | T1 |
| 34 | How do you implement metadata filtering in Qdrant before vector search? | RAG | T1 |
| 35 | How do you implement real-time document updates in a production vector store? | RAG | T1 |
| 36 | How do you handle tables and structured data in a RAG pipeline? | RAG | T1 |
| 37 | How do you implement namespace-based tenant isolation in Qdrant? | RAG | T1 |
| 38 | How do you implement a RAG pipeline for 10M documents at scale? | RAG | T1 |
| 39 | How do you implement context compression to reduce LLM token cost in RAG? | RAG | T1 |
| 40 | How do you implement a RAG evaluation CI pipeline that blocks bad deployments? | LLMOps | T1 |
| 41 | How do you implement a RAG pipeline that extracts and stores entities as metadata? | RAG | T2 |
| 42 | What is GraphRAG? When does it outperform standard RAG? | RAG | T2 |
| 43 | How do you implement a RAG chatbot with session memory and citations? | RAG | T1 |
| 44 | How do you implement document chunking that respects section structure? | RAG | T1 |
| 45 | How do you implement PII scrubbing before indexing documents into RAG? | RAG | T1 |
| 46 | How do you implement self-RAG where the model decides whether to retrieve? | RAG | T2 |
| 47 | How do you implement a RAG pipeline that uses both SQL and vector search? | RAG | T1 |
| 48 | How do you implement a RAG pipeline that streams answers token by token? | RAG | T1 |
| 49 | How do you monitor RAG faithfulness and relevance in production daily? | LLMOps | T1 |
| 50 | How do you implement access-controlled RAG where users only see their documents? | RAG | T1 |

---

## SECTION 3: Agentic AI Engineering (Q51–Q75)

### Q51
- **Question:** Build a multi-agent prior authorization system using LangGraph. Walk through the design.
- **Tier:** T1
- **Answer Points:**
  - **State:** `ClaimState` TypedDict with: claim details, eligibility result, clinical findings, decision, confidence, messages
  - **Nodes:** intake → eligibility_check → clinical_criteria_match → adjudication → notification
  - **Conditional routing:** after eligibility fails → denial node; after clinical match → confidence threshold routing
  - **HITL:** `interrupt_before` adjudication when `confidence < 0.7` or `amount > 50000`; human provides decision via `Command(resume=...)`
  - **Checkpointing:** PostgreSQL; thread_id = claim_id; supports resume after HITL
  - **Tools:** `check_eligibility_tool`, `get_clinical_criteria_tool`, `rag_search_tool`, `submit_decision_tool`
  - **Observability:** Langfuse callback handler on every run; cost per claim tracked

---

### Q52
- **Question:** Implement a LangGraph agent with tools that calls a database and an external API.
- **Tier:** T1
- **Answer:**
  ```python
  from langgraph.graph import StateGraph, START, END
  from langgraph.prebuilt import ToolNode, tools_condition
  from langchain_openai import ChatOpenAI
  from langchain_core.tools import tool
  from typing import Annotated, TypedDict
  from langgraph.graph.message import add_messages

  @tool
  async def get_member_info(member_id: str) -> dict:
      """Get member information from the database."""
      async with get_db() as db:
          member = await db.get(Member, member_id)
          return {"name": member.name, "plan": member.plan_code, "active": member.is_active}

  @tool
  async def check_eligibility_api(member_id: str, service_code: str) -> dict:
      """Check real-time eligibility for a service."""
      async with aiohttp.ClientSession() as session:
          async with session.get(f"{ELIGIBILITY_API}/{member_id}/{service_code}") as resp:
              return await resp.json()

  tools = [get_member_info, check_eligibility_api]
  llm = ChatOpenAI(model="gpt-4o", temperature=0).bind_tools(tools)

  class State(TypedDict):
      messages: Annotated[list, add_messages]

  async def agent_node(state: State) -> State:
      return {"messages": [await llm.ainvoke(state["messages"])]}

  graph = StateGraph(State)
  graph.add_node("agent", agent_node)
  graph.add_node("tools", ToolNode(tools))
  graph.add_edge(START, "agent")
  graph.add_conditional_edges("agent", tools_condition)
  graph.add_edge("tools", "agent")
  graph.add_edge("agent", END)
  app = graph.compile()
  ```

---

### Q53–Q75 (Condensed)

| # | Question | Topic | Tier |
|---|----------|-------|------|
| 53 | How do you implement a supervisor-worker multi-agent system in LangGraph? | Agents | T1 |
| 54 | How do you implement HITL approval in a LangGraph workflow with PostgreSQL checkpointing? | Agents | T1 |
| 55 | How do you implement agent observability with Langfuse per step? | LLMOps | T1 |
| 56 | How do you implement agent cost control (stop if > $0.50 per run)? | LLMOps | T1 |
| 57 | How do you implement agent retry for tool failures in LangGraph? | Agents | T1 |
| 58 | How do you implement parallel agent execution in LangGraph? | Agents | T1 |
| 59 | How do you implement an agent that validates its own structured output? | Agents | T1 |
| 60 | How do you implement a RAG agent that decides when to retrieve vs answer from memory? | Agents | T1 |
| 61 | How do you test a LangGraph agent without calling real LLMs? | Testing | T1 |
| 62 | How do you implement a LangGraph agent with streaming output in FastAPI? | Agents | T1 |
| 63 | How do you implement an agent that writes a multi-section report from multiple tools? | Agents | T1 |
| 64 | How do you implement a code-writing and testing agent in Python? | Agents | T2 |
| 65 | How do you implement a semantic router that routes queries to 10 specialized agents? | Agents | T1 |
| 66 | How do you implement an agent that handles 1000 concurrent document processing jobs? | Agents | T1 |
| 67 | How do you implement an MCP server for a healthcare database? | MCP | T1 |
| 68 | How do you integrate MCP tools into a LangGraph agentic workflow? | MCP | T1 |
| 69 | How do you implement a multi-tenant agent platform with per-tenant tool sets? | Agents | T1 |
| 70 | How do you implement agent governance (policy enforcement per tool call)? | Agents | T1 |
| 71 | How do you implement a claims adjudication agent that logs every decision step? | Agents | T1 |
| 72 | How do you implement an agent that operates within a token/cost budget? | LLMOps | T1 |
| 73 | How do you implement agent versioning and A/B testing in production? | LLMOps | T1 |
| 74 | How do you implement a self-evaluating agent that scores and retries its own answers? | Agents | T2 |
| 75 | How do you design a 24/7 agentic platform processing 100K documents/day? | Architecture | T1 |

---

## SECTION 4: GenAI System Design & Architecture (Q76–Q100)

| # | Question | Focus | Tier |
|---|----------|-------|------|
| 76 | Design a complete GenAI platform for healthcare prior authorization from scratch. | Architecture | T1 |
| 77 | Design a RAG-based enterprise knowledge base for 10M documents, multi-tenant. | Architecture | T1 |
| 78 | Design an LLMOps platform that supports 20 GenAI teams. | LLMOps | T1 |
| 79 | Design a GenAI chatbot for member support that handles 1M queries/month. | Architecture | T1 |
| 80 | Design a multi-modal document intelligence pipeline for insurance forms. | Architecture | T1 |
| 81 | Design an AI governance framework for an enterprise GenAI platform. | Governance | T1 |
| 82 | How do you implement a HIPAA-compliant GenAI pipeline from end to end? | Compliance | T1 |
| 83 | How do you implement continuous evaluation + automated rollback for LLM deployments? | LLMOps | T1 |
| 84 | How do you implement a prompt registry and versioning system for 50+ prompts? | LLMOps | T1 |
| 85 | How do you implement a GenAI platform that works with both Azure OpenAI and Ollama? | Architecture | T1 |
| 86 | How do you design a Python-based MCP server ecosystem for enterprise AI agents? | MCP | T1 |
| 87 | How do you implement a claims fraud detection system combining ML + LLM agents? | Architecture | T1 |
| 88 | How do you design a GenAI platform that guarantees reproducible decisions for audits? | Compliance | T1 |
| 89 | How do you implement a fine-tuning pipeline for a domain-specific LLM? | GenAI | T2 |
| 90 | How do you implement a Python platform for synthetic data generation for ML? | GenAI | T2 |
| 91 | How do you make the case to a CTO for investing in RAG vs fine-tuning? | Strategy | T1 |
| 92 | How do you implement a GenAI Center of Excellence for a 2000-person enterprise? | Leadership | T1 |
| 93 | How do you evaluate and select an LLM for a specific enterprise use case? | Strategy | T1 |
| 94 | How do you implement FinOps for a GenAI platform spending $100K/month on LLMs? | LLMOps | T1 |
| 95 | How do you implement a Python platform that handles 10K concurrent agent runs? | Architecture | T1 |
| 96 | How do you implement an AI red-teaming process for a production GenAI system? | Security | T1 |
| 97 | How do you implement responsible AI practices in a healthcare GenAI system? | Governance | T1 |
| 98 | How do you build a developer platform for GenAI teams with reusable components? | Platform | T1 |
| 99 | Describe the most complex GenAI system you've built. What would you change? | Experience | T1 |
| 100 | Design a complete enterprise GenAI platform: multi-agent orchestration, RAG at scale, LLMOps, MCP integrations, HIPAA compliance, CI/CD with prompt regression gates, and cost governance. | Architecture | T1 |
