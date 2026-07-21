# Top 100 MCP (Model Context Protocol) Interview Questions
> Focus: 8–15 Years | Non-FAANG | GenAI Companies, Enterprise AI, Platform Engineers
> Sources: Anthropic MCP Docs, LinkedIn, Reddit, Medium, GenAI Interview Reports (2024–2025)

---

## SECTION 1: MCP Fundamentals (Q1–Q25)

### Q1
- **Question:** What is the Model Context Protocol (MCP)? Why was it created?
- **Level:** 8–12 Yrs | **Company:** All GenAI companies | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Open standard introduced by Anthropic (November 2024) for connecting AI models to external context sources
  - Problem it solves: every AI tool/integration requires custom implementation; no standard way to connect LLMs to data sources, tools, and services
  - MCP provides: unified protocol for LLMs to access databases, APIs, file systems, services via standardized client-server architecture
  - Analogy: "USB-C for AI" — one protocol, many compatible tools
  - JSON-RPC 2.0 based; transport-agnostic (stdio, HTTP/SSE)
  - Supported by: Claude Desktop, Cline, Cursor, VS Code Copilot, many GenAI tools
- **Difficulty:** Easy

---

### Q2
- **Question:** What are the three core primitives in MCP? Explain each.
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **Tools:** Functions the LLM can call; execute code, call APIs, write to databases; controlled by client (requires approval or auto-approved)
  - **Resources:** Data the LLM can read; files, DB records, documents; like GET requests; read-only data access; identified by URI
  - **Prompts:** Pre-defined prompt templates; reusable workflows; exposed by server to guide LLM behavior; user-selectable
- **Additional:**
  - **Sampling:** MCP server can request the LLM to generate text (server-initiated LLM calls)
  - **Roots:** Inform server about which file/directory paths are relevant for the session
- **Difficulty:** Medium

---

### Q3
- **Question:** What is an MCP server? What is an MCP client? How do they communicate?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **MCP Server:** exposes tools, resources, prompts; runs as a separate process; stateless or stateful
  - **MCP Client:** LLM host (Claude Desktop, AI agent); connects to one or more servers; discovers capabilities; calls tools/reads resources
  - **Communication:**
    - JSON-RPC 2.0 over stdio (local servers) or HTTP with SSE (remote servers)
    - Lifecycle: initialize → capabilities negotiation → operation (tool calls, resource reads) → shutdown
  - **Transport options:** `stdio` (subprocess, local), `SSE` (HTTP remote), `WebSocket` (bidirectional)
- **Difficulty:** Medium

---

### Q4
- **Question:** How do you build a simple MCP server in Python using the `mcp` SDK?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from mcp.server import Server
  from mcp.server.stdio import stdio_server
  from mcp import types

  server = Server("claims-mcp-server")

  @server.list_tools()
  async def list_tools() -> list[types.Tool]:
      return [
          types.Tool(
              name="check_eligibility",
              description="Check if a member is eligible for a medical service",
              inputSchema={
                  "type": "object",
                  "properties": {
                      "member_id": {"type": "string", "description": "Member ID"},
                      "service_code": {"type": "string", "description": "CPT code"}
                  },
                  "required": ["member_id", "service_code"]
              }
          )
      ]

  @server.call_tool()
  async def call_tool(name: str, arguments: dict) -> list[types.TextContent]:
      if name == "check_eligibility":
          member_id = arguments["member_id"]
          service_code = arguments["service_code"]
          result = await eligibility_service.check(member_id, service_code)
          return [types.TextContent(type="text", text=str(result))]
      raise ValueError(f"Unknown tool: {name}")

  async def main():
      async with stdio_server() as (read_stream, write_stream):
          await server.run(read_stream, write_stream, server.create_initialization_options())

  if __name__ == "__main__":
      import asyncio
      asyncio.run(main())
  ```
- **Difficulty:** Medium

---

### Q5
- **Question:** How do you expose resources in an MCP server? What is a resource URI?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  @server.list_resources()
  async def list_resources() -> list[types.Resource]:
      return [
          types.Resource(
              uri="claims://member/{member_id}/history",
              name="Member Claim History",
              description="Historical claims for a member",
              mimeType="application/json"
          ),
          types.Resource(
              uri="policies://formulary/2024",
              name="2024 Drug Formulary",
              description="Complete drug formulary for plan year 2024",
              mimeType="text/plain"
          )
      ]

  @server.read_resource()
  async def read_resource(uri: str) -> types.ReadResourceResult:
      if uri.startswith("claims://member/"):
          member_id = uri.split("/")[3]
          history = await db.get_member_claim_history(member_id)
          return types.ReadResourceResult(
              contents=[types.TextContent(type="text", text=json.dumps(history))]
          )
      raise ValueError(f"Unknown resource: {uri}")
  ```
- **Answer Points:** Resource URIs are scheme-based; LLM reads resources like reading documents; server returns content; mimeType hints at format
- **Difficulty:** Medium

---

### Q6
- **Question:** How do you expose prompts in an MCP server?
- **Level:** 8–12 Yrs | **Company:** GenAI | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  @server.list_prompts()
  async def list_prompts() -> list[types.Prompt]:
      return [
          types.Prompt(
              name="analyze_claim",
              description="Analyze an insurance claim for approval or denial",
              arguments=[
                  types.PromptArgument(name="claim_id", description="The claim ID", required=True),
                  types.PromptArgument(name="urgency", description="urgent or routine", required=False)
              ]
          )
      ]

  @server.get_prompt()
  async def get_prompt(name: str, arguments: dict | None) -> types.GetPromptResult:
      if name == "analyze_claim":
          claim_id = arguments.get("claim_id")
          claim = await db.get_claim(claim_id)
          return types.GetPromptResult(
              description="Claim analysis workflow",
              messages=[
                  types.PromptMessage(
                      role="user",
                      content=types.TextContent(
                          type="text",
                          text=f"Analyze claim {claim_id}: {json.dumps(claim)}"
                      )
                  )
              ]
          )
  ```
- **Difficulty:** Medium

---

### Q7
- **Question:** What transport mechanisms does MCP support? When do you use `stdio` vs HTTP/SSE?
- **Level:** 8–12 Yrs | **Company:** All GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - **stdio transport:** server runs as child process; communicate via stdin/stdout; local only; most common for desktop integrations (Claude Desktop, Cursor)
  - **HTTP + SSE (Server-Sent Events):** server runs as HTTP service; client connects via HTTP; server pushes events via SSE; supports remote/network access
  - **When stdio:** local tools, file access, local DB; lower latency; no network overhead
  - **When HTTP/SSE:** multi-user server; cloud-hosted; accessible from multiple clients; scales horizontally
  - **WebSocket:** bidirectional; not in v1 spec but under consideration
- **Difficulty:** Medium

---

### Q8
- **Question:** How do you implement an MCP server with HTTP + SSE transport in Python?
- **Level:** 8–12 Yrs | **Company:** Enterprise GenAI | **Frequency:** Medium | **Tier:** T2
- **Answer:**
  ```python
  from mcp.server.sse import SseServerTransport
  from fastapi import FastAPI
  from fastapi.responses import HTMLResponse
  import uvicorn

  app = FastAPI()
  sse = SseServerTransport("/messages/")

  @app.get("/sse")
  async def handle_sse(request):
      async with sse.connect_sse(request.scope, request.receive, request._send) as streams:
          await mcp_server.run(
              streams[0], streams[1],
              mcp_server.create_initialization_options()
          )

  @app.post("/messages/")
  async def handle_messages(request):
      await sse.handle_post_message(request.scope, request.receive, request._send)

  if __name__ == "__main__":
      uvicorn.run(app, host="0.0.0.0", port=8000)
  ```
- **Difficulty:** Hard

---

### Q9
- **Question:** How does MCP capability negotiation work during initialization?
- **Level:** 8–12 Yrs | **Company:** Senior GenAI | **Frequency:** Medium | **Tier:** T2
- **Answer Points:**
  - Client sends `initialize` request with: protocol version, client info, client capabilities
  - Server responds with: supported protocol version, server info, server capabilities
  - Capabilities declared: `tools` (can list/call), `resources` (can list/read), `prompts` (can list/get), `logging`, `experimental`
  - After handshake: client sends `initialized` notification; operation phase begins
  - If version mismatch: server rejects or downgrades; client should handle gracefully
  - Version negotiation ensures forward/backward compatibility
- **Difficulty:** Medium

---

### Q10
- **Question:** What is the MCP `sampling` capability? How does it work?
- **Level:** 12–15 Yrs | **Company:** GenAI Architect | **Frequency:** Low | **Tier:** T3
- **Answer Points:**
  - Allows MCP server to request LLM completions from the client
  - Server sends `sampling/createMessage` request with messages + model preferences
  - Client (LLM host) performs the LLM call and returns result to server
  - Use case: server-side AI processing where server needs LLM capabilities for complex data analysis
  - Security: client controls which LLM is used; human approval can be required
- **Difficulty:** Hard

---

### Q11–Q25 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 11 | What is the `mcp` Python SDK? How do you install and get started? | 5–8 Yrs | High | T1 |
| 12 | How does MCP handle errors? What error codes are defined? | 8–12 Yrs | Medium | T2 |
| 13 | How do you implement input validation for MCP tool arguments? | 8–12 Yrs | High | T1 |
| 14 | What is the MCP `roots` capability? How does it scope file access? | 8–12 Yrs | Medium | T2 |
| 15 | How do you handle authentication in an MCP server? | 8–12 Yrs | High | T1 |
| 16 | How does MCP differ from OpenAI function calling / tool calling? | 8–12 Yrs | High | T1 |
| 17 | How do you test an MCP server in isolation? | 8–12 Yrs | High | T1 |
| 18 | What is the difference between MCP tools and MCP resources semantically? | 8–12 Yrs | High | T1 |
| 19 | How do you implement logging from an MCP server back to the client? | 8–12 Yrs | Medium | T2 |
| 20 | How do you implement a stateful MCP server that maintains session context? | 8–12 Yrs | Medium | T2 |
| 21 | How do you implement MCP resource subscriptions for real-time updates? | 12–15 Yrs | Low | T3 |
| 22 | What are the security considerations for an MCP server exposed over HTTP? | 8–12 Yrs | High | T1 |
| 23 | How do you implement pagination for large resource lists in MCP? | 8–12 Yrs | Medium | T2 |
| 24 | What is the MCP `progress` notification? How do you use it for long-running tools? | 8–12 Yrs | Medium | T2 |
| 25 | How do you version an MCP server API without breaking existing clients? | 12–15 Yrs | Medium | T2 |

---

## SECTION 2: Building Production MCP Servers (Q26–Q60)

### Q26
- **Question:** Build an MCP server that exposes a healthcare claims database as tools and resources.
- **Level:** 12–15 Yrs | **Company:** Healthcare GenAI | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from mcp.server import Server
  from mcp.server.stdio import stdio_server
  from mcp import types
  import asyncpg

  server = Server("healthcare-claims-mcp")
  db_pool: asyncpg.Pool = None

  @server.list_tools()
  async def list_tools():
      return [
          types.Tool(name="get_member_claims",
              description="Retrieve all claims for a member",
              inputSchema={"type": "object",
                           "properties": {"member_id": {"type": "string"},
                                          "status": {"type": "string", "enum": ["pending","approved","denied"]}},
                           "required": ["member_id"]}),
          types.Tool(name="submit_claim",
              description="Submit a new insurance claim",
              inputSchema={"type": "object",
                           "properties": {"member_id": {"type": "string"},
                                          "service_code": {"type": "string"},
                                          "amount": {"type": "number"},
                                          "service_date": {"type": "string"}},
                           "required": ["member_id","service_code","amount","service_date"]}),
          types.Tool(name="update_claim_status",
              description="Update the status of an existing claim",
              inputSchema={"type": "object",
                           "properties": {"claim_id": {"type": "string"},
                                          "status": {"type": "string"},
                                          "reason": {"type": "string"}},
                           "required": ["claim_id","status"]})
      ]

  @server.call_tool()
  async def call_tool(name: str, arguments: dict):
      async with db_pool.acquire() as conn:
          if name == "get_member_claims":
              query = "SELECT * FROM claims WHERE member_id=$1"
              args = [arguments["member_id"]]
              if "status" in arguments:
                  query += " AND status=$2"
                  args.append(arguments["status"])
              rows = await conn.fetch(query, *args)
              return [types.TextContent(type="text", text=json.dumps([dict(r) for r in rows], default=str))]

          elif name == "submit_claim":
              claim_id = f"C{uuid.uuid4().hex[:8].upper()}"
              await conn.execute(
                  "INSERT INTO claims(claim_id,member_id,service_code,amount,service_date,status) VALUES($1,$2,$3,$4,$5,'pending')",
                  claim_id, arguments["member_id"], arguments["service_code"],
                  arguments["amount"], arguments["service_date"]
              )
              return [types.TextContent(type="text", text=f"Claim submitted: {claim_id}")]

          raise ValueError(f"Unknown tool: {name}")
  ```
- **Difficulty:** Hard

---

### Q27
- **Question:** How do you implement authentication/authorization in an MCP server?
- **Level:** 8–12 Yrs | **Company:** Enterprise | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - MCP v1 doesn't define auth in protocol; implement at transport layer
  - **stdio:** auth implicit (OS process permissions; launched by trusted client)
  - **HTTP/SSE:** implement Bearer token validation in HTTP middleware
  - Extract user context from auth token; pass to tool handlers for RBAC
  ```python
  from fastapi import Depends, HTTPException, Security
  from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

  security = HTTPBearer()

  def verify_token(credentials: HTTPAuthorizationCredentials = Security(security)):
      token = credentials.credentials
      try:
          payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
          return payload
      except jwt.InvalidTokenError:
          raise HTTPException(401, "Invalid token")

  @app.get("/sse")
  async def sse_endpoint(request: Request, user = Depends(verify_token)):
      # Pass user context to MCP server via request state
      ...
  ```
- **Difficulty:** Hard

---

### Q28–Q60 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 28 | How do you implement retry logic in an MCP tool? | 8–12 Yrs | High | T1 |
| 29 | How do you implement an MCP server that wraps an existing REST API? | 8–12 Yrs | High | T1 |
| 30 | How do you implement an MCP server for a vector database (Qdrant/pgvector)? | 8–12 Yrs | High | T1 |
| 31 | How do you implement an MCP server for a file system (read/write files)? | 8–12 Yrs | High | T1 |
| 32 | How do you implement an MCP server for a code execution sandbox? | 12–15 Yrs | Medium | T2 |
| 33 | How do you handle long-running MCP tools with progress notifications? | 8–12 Yrs | Medium | T2 |
| 34 | How do you implement a multi-tenant MCP server where each tenant has isolated data? | 12–15 Yrs | High | T1 |
| 35 | How do you implement rate limiting in an MCP server? | 8–12 Yrs | High | T1 |
| 36 | How do you implement an MCP server that aggregates multiple backend services? | 12–15 Yrs | High | T1 |
| 37 | How do you test MCP tool schemas for correctness? | 8–12 Yrs | High | T1 |
| 38 | How do you implement MCP tool versioning? | 8–12 Yrs | Medium | T2 |
| 39 | How do you implement an MCP server with connection pooling? | 8–12 Yrs | Medium | T2 |
| 40 | How do you implement caching for expensive MCP resource reads? | 8–12 Yrs | High | T1 |
| 41 | How do you implement audit logging for all MCP tool calls? | 8–12 Yrs | High | T1 |
| 42 | How do you implement a HIPAA-compliant MCP server for healthcare data? | 12–15 Yrs | High | T1 |
| 43 | How do you implement an MCP server that reads from Snowflake/BigQuery? | 8–12 Yrs | Medium | T2 |
| 44 | How do you implement an MCP server with structured output (not just text)? | 8–12 Yrs | High | T1 |
| 45 | How do you implement an MCP server for Kafka message publishing? | 8–12 Yrs | Medium | T2 |
| 46 | How do you implement graceful shutdown for an MCP server? | 8–12 Yrs | Medium | T2 |
| 47 | How do you implement an MCP server for Azure Blob Storage? | 8–12 Yrs | Medium | T2 |
| 48 | How do you implement an MCP server for S3? | 8–12 Yrs | Medium | T2 |
| 49 | How do you implement an MCP server for a SQL database with safe query execution? | 8–12 Yrs | High | T1 |
| 50 | How do you implement an MCP server for a LangGraph agent? | 12–15 Yrs | High | T1 |
| 51 | How do you implement an MCP server that chains multiple tools automatically? | 12–15 Yrs | Medium | T2 |
| 52 | How do you implement an MCP server for a RAG pipeline? | 12–15 Yrs | High | T1 |
| 53 | How do you implement health checks for an MCP server deployed in Kubernetes? | 8–12 Yrs | High | T1 |
| 54 | How do you implement an MCP tool that generates PDF reports? | 8–12 Yrs | Medium | T2 |
| 55 | How do you implement an MCP server for sending emails/notifications? | 8–12 Yrs | Medium | T2 |
| 56 | How do you implement dynamic tool discovery in an MCP server? | 12–15 Yrs | Low | T3 |
| 57 | How do you implement an MCP server that supports parallel tool execution? | 12–15 Yrs | Medium | T2 |
| 58 | How do you implement an MCP server for GitHub repository operations? | 8–12 Yrs | Medium | T2 |
| 59 | How do you implement an MCP server for Jira/ServiceNow ticket management? | 8–12 Yrs | Medium | T2 |
| 60 | How do you implement an MCP server for Salesforce CRM data? | 8–12 Yrs | Medium | T2 |

---

## SECTION 3: MCP Integration & Architecture (Q61–Q100)

### Q61
- **Question:** How do you integrate an MCP server into a LangGraph agentic workflow?
- **Level:** 12–15 Yrs | **Company:** GenAI Architect | **Frequency:** High | **Tier:** T1
- **Answer:**
  ```python
  from langchain_mcp_adapters.tools import load_mcp_tools
  from mcp import ClientSession, StdioServerParameters
  from mcp.client.stdio import stdio_client

  async def create_agent_with_mcp():
      # Connect to MCP server
      server_params = StdioServerParameters(
          command="python",
          args=["claims_mcp_server.py"],
          env={"DB_URL": DATABASE_URL}
      )

      async with stdio_client(server_params) as (read, write):
          async with ClientSession(read, write) as session:
              await session.initialize()

              # Load MCP tools as LangChain tools
              tools = await load_mcp_tools(session)

              # Create LangGraph agent with MCP tools
              llm = ChatOpenAI(model="gpt-4o").bind_tools(tools)
              # ... build LangGraph workflow using these tools
  ```
- **Difficulty:** Hard

---

### Q62
- **Question:** How does an MCP client discover and connect to multiple MCP servers?
- **Level:** 8–12 Yrs | **Company:** GenAI | **Frequency:** High | **Tier:** T1
- **Answer Points:**
  - Client config (e.g., `claude_desktop_config.json`) lists server definitions with command/args/env
  - Client starts each server process on startup; maintains separate connection per server
  - Tool names deduplicated with server prefix if needed
  - LLM sees aggregated tool list from all connected servers
  - Example config:
  ```json
  {
    "mcpServers": {
      "claims-db": {"command": "python", "args": ["claims_mcp.py"]},
      "eligibility-api": {"command": "python", "args": ["eligibility_mcp.py"]},
      "formulary": {"command": "python", "args": ["formulary_mcp.py"]}
    }
  }
  ```
- **Difficulty:** Medium

---

### Q63–Q100 (Condensed)

| # | Question | Level | Frequency | Tier |
|---|----------|-------|-----------|------|
| 63 | How do you implement an MCP proxy that routes requests to multiple specialized servers? | 12–15 Yrs | High | T1 |
| 64 | How do you implement an MCP server registry for dynamic service discovery? | 12–15 Yrs | Medium | T2 |
| 65 | How does MCP compare to OpenAPI/Swagger as a tool interface standard? | 8–12 Yrs | High | T1 |
| 66 | How do you generate MCP tool schemas automatically from Pydantic models? | 8–12 Yrs | High | T1 |
| 67 | How do you implement MCP tools with streaming output? | 8–12 Yrs | Medium | T2 |
| 68 | How do you implement MCP resource templates with URI parameters? | 8–12 Yrs | Medium | T2 |
| 69 | How do you implement a Python MCP client that connects to an MCP server? | 8–12 Yrs | High | T1 |
| 70 | How do you implement circuit breaker for MCP tool calls? | 8–12 Yrs | Medium | T2 |
| 71 | How does MCP handle binary data (images, PDFs) in responses? | 8–12 Yrs | High | T1 |
| 72 | What is the `EmbeddedResource` type in MCP? How do you return file attachments? | 8–12 Yrs | Medium | T2 |
| 73 | How do you implement a Python MCP client for automated testing? | 8–12 Yrs | High | T1 |
| 74 | How do you implement MCP tool call validation to prevent prompt injection? | 12–15 Yrs | High | T1 |
| 75 | How do you implement a CRUD MCP server with full audit trail? | 8–12 Yrs | High | T1 |
| 76 | How do you implement an MCP server with role-based tool access? | 12–15 Yrs | High | T1 |
| 77 | How do you implement an MCP server that supports batch tool calls? | 12–15 Yrs | Medium | T2 |
| 78 | How do you monitor MCP server performance (latency, error rate, tool usage)? | 8–12 Yrs | High | T1 |
| 79 | How do you implement an MCP server for a legacy SOAP/XML service? | 8–12 Yrs | Low | T3 |
| 80 | How do you implement an MCP server for an event-driven system (publish events)? | 8–12 Yrs | Medium | T2 |
| 81 | How do you deploy an MCP server to AWS Lambda? | 8–12 Yrs | Medium | T2 |
| 82 | How do you deploy an MCP server to Kubernetes as a sidecar? | 12–15 Yrs | Medium | T2 |
| 83 | How do you implement a shared MCP server for a team of AI agents? | 12–15 Yrs | High | T1 |
| 84 | How do you implement an MCP server that integrates with OpenTelemetry? | 8–12 Yrs | Medium | T2 |
| 85 | How do you implement an MCP server for an insurance policy decision engine? | 12–15 Yrs | High | T1 |
| 86 | How do you implement an MCP server for member eligibility verification? | 12–15 Yrs | High | T1 |
| 87 | How do you implement an MCP server for medical coding (ICD/CPT lookup)? | 12–15 Yrs | High | T1 |
| 88 | How do you handle MCP server versioning in a microservices environment? | 12–15 Yrs | Medium | T2 |
| 89 | What is `fastmcp`? How does it simplify building MCP servers in Python? | 8–12 Yrs | High | T1 |
| 90 | How do you use `fastmcp` to build a healthcare MCP server with minimal boilerplate? | 8–12 Yrs | High | T1 |
| 91 | How do you implement an MCP server for a vector search engine? | 8–12 Yrs | High | T1 |
| 92 | How do you implement zero-downtime updates for a production MCP server? | 12–15 Yrs | Medium | T2 |
| 93 | How do you implement an MCP server that exposes a Python REPL safely? | 12–15 Yrs | Low | T3 |
| 94 | How do you implement an MCP gateway that enforces organizational policies? | 12–15 Yrs | High | T1 |
| 95 | How do you implement an MCP server with differential access per user role? | 12–15 Yrs | High | T1 |
| 96 | How do you implement a Python-based MCP test harness for contract testing? | 12–15 Yrs | Medium | T2 |
| 97 | How do you implement an MCP server for claim document generation (PDF output)? | 8–12 Yrs | Medium | T2 |
| 98 | How do you implement cost tracking per MCP tool call for chargebacks? | 12–15 Yrs | Medium | T2 |
| 99 | How do you implement an MCP server that acts as a bridge between LangGraph agents and enterprise systems? | 12–15 Yrs | High | T1 |
| 100 | Design a complete MCP server ecosystem for a healthcare AI platform: claims DB server, eligibility API server, formulary server, document server, and a policy gateway server — all with auth, RBAC, HIPAA compliance, and Kubernetes deployment. | 12–15 Yrs | High | T1 |
