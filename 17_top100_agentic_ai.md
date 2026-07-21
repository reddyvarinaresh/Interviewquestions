# Top 100 Agentic AI Interview Questions
> Focus: 8–15 Years | Non-FAANG | GenAI Companies, Enterprise AI, Healthcare AI
> Sources: Glassdoor, LinkedIn, Reddit, Medium, GenAI Engineer Interview Reports

---

## SECTION 1: Agentic AI Fundamentals (Q1–Q20)

### Q1
- **Question:** What is an AI agent? How is it different from a basic LLM call?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Basic LLM call: one-shot; takes input, returns output; no state, no iteration
  - AI Agent: LLM in a loop — perceives state, reasons, selects/executes actions, observes result, repeats
  - Core components: LLM (brain), tools (actions), memory (state), planning (orchestration)
  - Agents can: browse web, query databases, call APIs, write/execute code, delegate to sub-agents
  - Key property: **autonomy** — agent decides what to do next based on observations
- **Senior Answer:** Agents trade reliability for capability; important to design guardrails, human-in-the-loop checkpoints for production
- **Difficulty:** Medium

---

### Q2
- **Question:** What is the ReAct pattern? How does it structure agent reasoning?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - ReAct = Reasoning + Acting; interleaves thought traces with tool actions
  - Loop: **Thought** (reasoning about what to do) → **Action** (tool call) → **Observation** (result) → repeat
  - Grounded reasoning: agent "thinks aloud" before acting; easier to debug
  - More reliable than action-only: agent can self-correct based on observations
- **Code:**
  ```
  Thought: I need to check if member M001 is eligible for this service.
  Action: check_eligibility(member_id="M001", service_code="99213")
  Observation: {"eligible": true, "copay": 25.0, "deductible_remaining": 500}
  Thought: Member is eligible. Copay is $25. I should check if prior auth is needed.
  Action: check_prior_auth(service_code="99213", plan_code="PPO")
  Observation: {"prior_auth_required": false}
  Thought: No prior auth needed. I can approve the claim.
  Final Answer: Claim approved. Copay: $25. No prior authorization required.
  ```
- **Difficulty:** Medium

---

### Q3
- **Question:** What is the difference between orchestration and choreography in multi-agent systems?
- **Level:** 12–15 Yrs | **Company:** GenAI Architect | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Orchestration:** central orchestrator agent directs sub-agents; knows the full plan; easier to debug; single point of failure
  - **Choreography:** agents react to events from each other; decentralized; more resilient; harder to trace
  - **When orchestration:** well-defined sequential workflow; auditability required (healthcare, finance)
  - **When choreography:** event-driven; dynamic; emergent behavior acceptable
  - LangGraph: orchestration via graph nodes; each node is an agent step; edges define flow
- **Difficulty:** Hard

---

### Q4
- **Question:** What is tool calling (function calling) in the context of AI agents? How do you design tools?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from pydantic import BaseModel, Field

  class CheckEligibilityInput(BaseModel):
      member_id: str = Field(description="The member's ID (format: M + 8 digits)")
      service_code: str = Field(description="CPT or HCPCS service code")
      service_date: str = Field(description="Date of service in YYYY-MM-DD format")

  async def check_eligibility(input: CheckEligibilityInput) -> dict:
      """
      Check if a member is eligible for a specific medical service on a given date.
      Returns: eligibility status, copay, deductible, and any restrictions.
      """
      result = await eligibility_service.check(
          input.member_id, input.service_code, input.service_date
      )
      return result.dict()
  ```
- **Tool design principles:** clear description (LLM reads this); strong input validation; return structured output; handle errors gracefully; idempotent where possible
- **Difficulty:** Medium

---

### Q5
- **Question:** What is agent memory? Describe the different types of memory an agent can have.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **In-context (working) memory:** current conversation + tool results in the prompt; ephemeral; limited by context window
  - **External short-term memory:** Redis/session store; conversation history per user; survives token limit
  - **Long-term memory:** vector store; semantic retrieval of past interactions; persists across sessions
  - **Procedural memory:** system prompt; agent's instructions, persona, and policies
  - **Episodic memory:** specific past events; can be retrieved for few-shot examples
  - **Semantic memory:** facts about the domain; knowledge base (RAG)
- **Difficulty:** Medium

---

### Q6
- **Question:** What is a multi-agent system? When would you use multiple agents instead of one?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Single agent: simpler, cheaper, easier to debug; limited by context window; single specialization
  - Multi-agent: parallel specialization; each agent excels at one task; can parallelize work
  - Use cases: research agent + writing agent + critic agent; different agents per domain (claims vs eligibility vs provider)
  - Patterns: supervisor/worker; peer-to-peer; hierarchical
  - Risk: complex coordination, harder to debug, higher cost
- **Difficulty:** Medium

---

### Q7
- **Question:** What is the role of a supervisor agent? How does it coordinate worker agents?
- **Level:** 12–15 Yrs | **Company:** GenAI Architect | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  # Supervisor decides which agent to call next
  supervisor_prompt = """
  You are a claims processing supervisor. Based on the current task and state,
  decide which specialist to call next:
  - eligibility_agent: check member eligibility
  - clinical_agent: review clinical criteria
  - adjudication_agent: make final claim decision
  - notification_agent: send decision to member/provider

  Current state: {state}
  Next agent (or FINISH):
  """
  ```
- **Answer Points:** Supervisor routes; workers execute; supervisor aggregates results; supervisor knows when to stop; LangGraph `StateGraph` with conditional edges is ideal for this
- **Difficulty:** Hard

---

### Q8
- **Question:** How do you implement human-in-the-loop (HITL) in an agentic workflow?
- **Level:** 8–12 Yrs | **Company:** Healthcare, Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Pause agent at decision points requiring human judgment
  - Persist workflow state (checkpoint) when paused
  - Human reviews and approves/modifies → workflow resumes
  - Use cases: high-value claims, uncertain diagnoses, compliance-required approvals
  - LangGraph: `interrupt_before` / `interrupt_after` at specific nodes
  ```python
  # LangGraph HITL
  from langgraph.checkpoint.sqlite import SqliteSaver
  from langgraph.types import interrupt

  def review_node(state: ClaimState) -> ClaimState:
      if state.amount > 50000:
          human_decision = interrupt(
              {"message": "High-value claim requires approval", "claim": state.claim}
          )
          state.approval = human_decision
      return state
  ```
- **Difficulty:** Hard

---

### Q9
- **Question:** How do you handle agent errors and prevent infinite loops?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Max iterations:** hard limit on agent loop count; raise error or return partial result
  - **Tool error handling:** catch tool exceptions; return error message to agent; agent can retry or choose alternate path
  - **Timeout:** per-step and total agent run timeouts
  - **Circuit breaker:** if same tool called 3x with same args, stop
  - **Fallback:** if agent can't complete task, escalate to human or return best-effort answer
  ```python
  class AgentRuntime:
      MAX_STEPS = 20
      STEP_TIMEOUT = 30.0

      async def run(self, task: str, state: AgentState) -> AgentResult:
          for step in range(self.MAX_STEPS):
              try:
                  action = await asyncio.wait_for(
                      self.llm.decide_next_action(state), timeout=self.STEP_TIMEOUT
                  )
                  if action.type == "finish":
                      return AgentResult(answer=action.result, steps=step)
                  state = await self.execute_action(action, state)
              except asyncio.TimeoutError:
                  logger.error(f"Agent step {step} timed out")
                  break
          return AgentResult(answer="Could not complete task", steps=step, error=True)
  ```
- **Difficulty:** Medium

---

### Q10
- **Question:** What is agent state? How do you design state for a complex agentic workflow?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from typing import Annotated, TypedDict
  from langgraph.graph import add_messages

  class ClaimProcessingState(TypedDict):
      # Inputs
      claim_id: str
      member_id: str
      claim_details: dict

      # Collected during execution
      eligibility_result: dict | None
      clinical_review: dict | None
      prior_auth_status: str | None

      # Decision tracking
      decision: str | None  # approved/denied/pending
      decision_reason: str | None
      flags: list[str]

      # Conversation
      messages: Annotated[list, add_messages]

      # Control
      next_step: str
      error: str | None
      iterations: int
  ```
- **Difficulty:** Medium

---

### Q11–Q20 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 11 | What is the Plan-and-Execute pattern? How does it differ from ReAct? | 8–12 Yrs | High | T1 |
| 12 | How do you implement agent observability? What do you log per step? | 8–12 Yrs | High | T1 |
| 13 | What is an agent trajectory? How do you replay it for debugging? | 8–12 Yrs | Medium | T2 |
| 14 | How do you implement a token-budget-aware agent that avoids exceeding limits? | 8–12 Yrs | High | T1 |
| 15 | What is the difference between a tool, a skill, and a sub-agent? | 8–12 Yrs | Medium | T2 |
| 16 | How do you implement agent self-reflection / self-critique? | 12–15 Yrs | Medium | T2 |
| 17 | How does an agent handle ambiguous user queries? | 8–12 Yrs | High | T1 |
| 18 | How do you implement an agent that can write and execute Python code? | 12–15 Yrs | Medium | T2 |
| 19 | What are the security risks of agentic AI systems? How do you mitigate them? | 12–15 Yrs | High | T1 |
| 20 | How do you implement rate limiting for an agent that calls external APIs? | 8–12 Yrs | High | T1 |

---

## SECTION 2: LangGraph & Agent Frameworks (Q21–Q55)

### Q21
- **Question:** What is LangGraph? How does it differ from LangChain for building agents?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - LangChain: chain-based; sequential; limited control flow
  - LangGraph: graph-based; nodes = steps; edges = control flow; supports cycles/loops
  - LangGraph features: stateful; built-in checkpointing; human-in-the-loop; streaming; branching
  - Ideal for: multi-agent workflows, long-running processes, conditional logic, HITL
  - LangGraph = first-class support for real production agentic systems
- **Difficulty:** Medium

---

### Q22
- **Question:** Build a simple LangGraph agent with two nodes: one that retrieves info and one that answers.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from langgraph.graph import StateGraph, END
  from typing import TypedDict

  class State(TypedDict):
      query: str
      context: str
      answer: str

  async def retrieve_node(state: State) -> State:
      chunks = await vector_db.search(state["query"], k=5)
      state["context"] = "\n".join(c.content for c in chunks)
      return state

  async def answer_node(state: State) -> State:
      response = await llm.ainvoke(
          f"Context: {state['context']}\n\nQuestion: {state['query']}"
      )
      state["answer"] = response.content
      return state

  # Build graph
  graph = StateGraph(State)
  graph.add_node("retrieve", retrieve_node)
  graph.add_node("answer", answer_node)
  graph.set_entry_point("retrieve")
  graph.add_edge("retrieve", "answer")
  graph.add_edge("answer", END)
  app = graph.compile()

  result = await app.ainvoke({"query": "What is the copay for PPO members?"})
  print(result["answer"])
  ```
- **Difficulty:** Medium

---

### Q23
- **Question:** How do you implement conditional edges in LangGraph?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  def route_after_triage(state: ClaimState) -> str:
      """Determine next step based on claim complexity."""
      if state.get("requires_clinical_review"):
          return "clinical_review"
      elif state.get("requires_prior_auth"):
          return "prior_auth"
      elif state.get("amount", 0) > 50000:
          return "human_review"
      else:
          return "auto_adjudicate"

  graph.add_conditional_edges(
      "triage",
      route_after_triage,
      {
          "clinical_review": "clinical_review",
          "prior_auth": "prior_auth",
          "human_review": "human_review",
          "auto_adjudicate": "adjudicate"
      }
  )
  ```
- **Difficulty:** Medium

---

### Q24
- **Question:** How do you implement checkpointing in LangGraph for long-running workflows?
- **Level:** 8–12 Yrs | **Company:** Enterprise GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from langgraph.checkpoint.postgres.aio import AsyncPostgresSaver

  async with AsyncPostgresSaver.from_conn_string(DB_URL) as checkpointer:
      graph = workflow.compile(checkpointer=checkpointer)

      # Start or resume a workflow
      config = {"configurable": {"thread_id": "claim_C12345"}}

      # First run
      result = await graph.ainvoke(initial_state, config=config)

      # Resume after human approval (days later)
      result = await graph.ainvoke(
          Command(resume={"approved": True, "reviewer": "Dr. Smith"}),
          config=config
      )
  ```
- **Answer Points:** State persisted to DB at each node; `thread_id` identifies the workflow instance; can resume from any checkpoint
- **Difficulty:** Hard

---

### Q25–Q55 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 25 | How do you implement a supervisor-worker multi-agent system in LangGraph? | 12–15 Yrs | High | T1 |
| 26 | How do you stream LangGraph agent output token by token in FastAPI? | 8–12 Yrs | High | T1 |
| 27 | How do you implement parallel tool execution in LangGraph? | 8–12 Yrs | High | T1 |
| 28 | How do you implement agent memory with LangGraph and Redis? | 8–12 Yrs | High | T1 |
| 29 | How do you implement a LangGraph sub-graph? When do you use it? | 8–12 Yrs | Medium | T2 |
| 30 | How does LangGraph handle cycles/loops? How do you prevent infinite loops? | 8–12 Yrs | High | T1 |
| 31 | What is LangSmith? How do you use it to trace and debug agent runs? | 8–12 Yrs | High | T1 |
| 32 | How do you implement a LangGraph workflow that retries failed steps? | 8–12 Yrs | Medium | T2 |
| 33 | How do you implement a LangGraph agent with timeout per step? | 8–12 Yrs | Medium | T2 |
| 34 | How do you test a LangGraph agent? What does a unit test look like? | 8–12 Yrs | High | T1 |
| 35 | How do you implement state reducers in LangGraph for list accumulation? | 8–12 Yrs | Medium | T2 |
| 36 | How do you implement a LangGraph workflow for claim prior authorization? | 12–15 Yrs | High | T1 |
| 37 | How do you implement a feedback node in LangGraph that routes based on LLM confidence? | 12–15 Yrs | Medium | T2 |
| 38 | How does `CrewAI` differ from LangGraph for multi-agent systems? | 8–12 Yrs | Medium | T2 |
| 39 | How does `AutoGen` differ from LangGraph? When would you choose AutoGen? | 8–12 Yrs | Medium | T2 |
| 40 | How do you implement tool calling with structured Pydantic models in LangGraph? | 8–12 Yrs | High | T1 |
| 41 | How do you implement a LangGraph agent that escalates to human on uncertainty? | 8–12 Yrs | High | T1 |
| 42 | How do you implement an agent that generates and validates its own output? | 8–12 Yrs | High | T1 |
| 43 | How do you deploy a LangGraph agent as a FastAPI service? | 8–12 Yrs | High | T1 |
| 44 | How do you implement concurrent LangGraph workflows for batch processing? | 12–15 Yrs | Medium | T2 |
| 45 | What is LangGraph Studio? How do you use it for agent development? | 8–12 Yrs | Low | T3 |
| 46 | How do you implement a LangGraph agent with role-based tool access? | 12–15 Yrs | Medium | T2 |
| 47 | How do you implement a LangGraph agent for enterprise IT helpdesk? | 8–12 Yrs | High | T1 |
| 48 | How do you implement message deduplication in a multi-agent LangGraph system? | 12–15 Yrs | Medium | T2 |
| 49 | What are LangGraph's built-in node types? (`ToolNode`, `MessagesState`, etc.) | 8–12 Yrs | High | T1 |
| 50 | How do you implement a LangGraph system where agents communicate via shared state? | 12–15 Yrs | Medium | T2 |
| 51 | How do you implement a LangGraph agent with RAG as a tool? | 8–12 Yrs | High | T1 |
| 52 | How do you implement error recovery in a multi-step LangGraph workflow? | 8–12 Yrs | High | T1 |
| 53 | How do you implement agent cost control (stop if spending > $1)? | 8–12 Yrs | Medium | T2 |
| 54 | How do you implement a LangGraph agent that writes to a database? | 8–12 Yrs | High | T1 |
| 55 | How do you implement a LangGraph workflow with approval gates for HIPAA compliance? | 12–15 Yrs | High | T1 |

---

## SECTION 3: Production Agentic AI (Q56–Q100)

### Q56
- **Question:** How do you design an agentic system for healthcare prior authorization?
- **Level:** 12–15 Yrs | **Company:** Healthcare GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Intake agent:** parse PA request (PDF/fax), extract clinical info, ICD/CPT codes
  - **Eligibility agent:** verify member eligibility, plan coverage for requested service
  - **Clinical criteria agent:** match against clinical guidelines (evidence-based criteria)
  - **Prior auth tool:** query payer PA database for rules
  - **Decision agent:** synthesize findings → approve/deny/request more info
  - **HITL gate:** high-value or ambiguous cases → medical director review
  - **Notification agent:** generate decision letter for member and provider
  - **Audit agent:** log all decisions with reasoning for compliance
  - Implemented in LangGraph with PostgreSQL checkpointing; HIPAA-compliant
- **Difficulty:** Hard

---

### Q57
- **Question:** How do you implement guardrails for an agentic system that has access to write operations?
- **Level:** 12–15 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Read-only by default:** agents should read and reason; separate approval step for writes
  - **Tool-level permissions:** each tool annotated with risk level; HITL required for HIGH-risk tools
  - **Dry-run mode:** agent can simulate write actions without executing
  - **Audit log:** every tool call logged with args, user, timestamp, result
  - **Blast radius limits:** max records agent can modify in one run; max dollar value
  - **Reversibility:** prefer reversible operations; maintain undo capability
- **Difficulty:** Hard

---

### Q58–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 58 | How do you implement agent testing? What does an integration test for an agent look like? | 8–12 Yrs | High | T1 |
| 59 | How do you implement agent observability with Langfuse? | 8–12 Yrs | High | T1 |
| 60 | How do you implement cost attribution per agent step in a multi-agent system? | 8–12 Yrs | Medium | T2 |
| 61 | How do you handle a tool that takes > 30 seconds in an async agent? | 8–12 Yrs | High | T1 |
| 62 | How do you implement a long-running agent job that can be polled for status? | 8–12 Yrs | High | T1 |
| 63 | How do you implement agent versioning (v1 vs v2 of the same workflow)? | 12–15 Yrs | Medium | T2 |
| 64 | How do you implement A/B testing for two agentic workflows? | 12–15 Yrs | High | T1 |
| 65 | How do you implement a multi-agent system where agents compete for best answer? | 12–15 Yrs | Medium | T2 |
| 66 | How do you implement a RAG agent that knows when NOT to retrieve? | 8–12 Yrs | High | T1 |
| 67 | How do you implement prompt caching for a multi-step agent with repeated system prompts? | 8–12 Yrs | Medium | T2 |
| 68 | How do you implement agent failover (primary agent fails → backup agent)? | 12–15 Yrs | Medium | T2 |
| 69 | How do you implement a code-writing agent with sandboxed execution? | 12–15 Yrs | Medium | T2 |
| 70 | How do you implement a web-browsing agent in Python? | 12–15 Yrs | Low | T3 |
| 71 | How do you implement agent memory that prioritizes recent and important events? | 12–15 Yrs | Medium | T2 |
| 72 | How do you prevent prompt injection in tool outputs fed back to the agent? | 12–15 Yrs | High | T1 |
| 73 | How do you implement a multi-agent debate for higher quality decisions? | 12–15 Yrs | Medium | T2 |
| 74 | How do you implement an agent that learns from its mistakes across sessions? | 12–15 Yrs | Medium | T2 |
| 75 | What is the role of a planner vs executor in a two-stage agent system? | 8–12 Yrs | High | T1 |
| 76 | How do you implement a document processing agent that handles 1000 docs in parallel? | 12–15 Yrs | High | T1 |
| 77 | How do you implement graceful degradation when an LLM agent is unavailable? | 8–12 Yrs | High | T1 |
| 78 | How do you implement an agent that validates its own tool call arguments? | 8–12 Yrs | Medium | T2 |
| 79 | How do you implement a semantic router that routes queries to specialized agents? | 8–12 Yrs | High | T1 |
| 80 | How do you implement a Python agent that uses database tools safely? | 8–12 Yrs | High | T1 |
| 81 | How do you implement HITL approval with WebSocket notifications to reviewers? | 12–15 Yrs | Medium | T2 |
| 82 | How do you handle race conditions in a multi-agent shared state system? | 12–15 Yrs | Medium | T2 |
| 83 | How do you implement a claims fraud detection agent? | 12–15 Yrs | High | T1 |
| 84 | How do you implement a medical coding agent that assigns ICD/CPT codes? | 12–15 Yrs | High | T1 |
| 85 | How do you implement an agent that generates structured reports with citations? | 8–12 Yrs | High | T1 |
| 86 | How do you implement an agentic pipeline for processing FHIR data? | 12–15 Yrs | Medium | T2 |
| 87 | What is the Model Context Protocol (MCP)? How does it relate to agent tools? | 8–12 Yrs | High | T1 |
| 88 | How do you implement an agent that can call other agents as tools? | 12–15 Yrs | High | T1 |
| 89 | How do you implement reproducible agent runs for compliance auditing? | 12–15 Yrs | High | T1 |
| 90 | How do you implement a Python agent that works offline (no external API)? | 8–12 Yrs | Medium | T2 |
| 91 | How do you implement an agent that generates and validates SQL queries? | 8–12 Yrs | High | T1 |
| 92 | How do you implement a feedback collection node in a LangGraph agent? | 8–12 Yrs | Medium | T2 |
| 93 | How do you implement context-aware routing (route based on conversation state)? | 12–15 Yrs | Medium | T2 |
| 94 | How do you implement an agent that uses structured outputs for every step? | 8–12 Yrs | High | T1 |
| 95 | How do you implement a self-evaluating agent that scores its own responses? | 12–15 Yrs | Medium | T2 |
| 96 | How do you implement agent governance (policy enforcement across all agent actions)? | 12–15 Yrs | High | T1 |
| 97 | How do you implement a Python agent that processes and summarizes 100-page reports? | 8–12 Yrs | High | T1 |
| 98 | How do you implement a drug interaction checking agent for clinical decision support? | 12–15 Yrs | High | T1 |
| 99 | How do you implement an agent capable of operating within a time budget? | 12–15 Yrs | Medium | T2 |
| 100 | Design a multi-agent system for healthcare claims auto-adjudication: intake, eligibility, clinical review, HITL approval, decision, notification — fully auditable, HIPAA-compliant, scalable to 100K claims/day. | 12–15 Yrs | High | T1 |
