## Oracle Agentic AI Foundations Course — Learning Notes

### Agent

An AI agent thinks and decides its next steps dynamically.

**AI Agent = LLM (brain) + Tools (hands) + Loop (iterative process)**

In the agentic execution loop, the agent:

1. **Perceive** — receive input or an observation  
2. **Reason** — select the next step  
3. **Act** — call a tool or respond to the user  
4. **Observe** — receive tool results or feedback  

The loop continues until the goal is achieved or a max iteration limit is reached.

### Agentic Reasoning Frameworks

- **Chain of Thought (CoT)** — breaks a problem into a sequential chain of intermediate reasoning steps before reaching a conclusion  
- **ReAct (Reasoning and Acting)** — interleaves reasoning with actions and observations; commonly used in production  
- **Tree of Thoughts (ToT)** — explores multiple reasoning branches at once, like a search tree  

### Agent Threat Model

- **Prompt injection** — an attacker hijacks the agent via crafted input or poisoned retrieved content  
- **Tool misuse** — the agent calls tools with wrong or dangerous arguments (e.g., unauthorized emails, destructive DB queries)  
- **Memory poisoning** — poisoned content stored in memory affects future behavior for that user, and potentially others if memory is shared or broadly retrieved  
- **Data exfiltration** — the agent is tricked into leaking sensitive internal data through tool calls or responses  
- **Runaway execution** — infinite loops or excessive API calls cause cost overruns and system strain  

### Defense in Depth

| Layer | When | What it covers |
| --- | --- | --- |
| **Input validation** | Pre-LLM | Treat all external/retrieved content as untrusted; PII detection; rate limiting |
| **LLM guardrails** | During processing | Safety system prompts; tool access controls; low-confidence outputs routed to human review |
| **Tool boundaries** | Action processing | Least privilege; input validation; sandboxing; human-in-the-loop |
| **Output filtering** | Post-LLM | PII screening; content policy (e.g., citations); relevance verification |
| **Observability** | Across all layers | Log traces, inputs, tool calls, outputs, errors, cost, latency |

Together, these layers form a solid guardrails architecture.

## MCP (Model Context Protocol)

MCP is an open standard that lets AI applications connect to external tools and data sources through a shared protocol — instead of building a custom integration for each service.

### Architecture

MCP has three main roles:

| Role | What it is | Examples |
| --- | --- | --- |
| **MCP Host** | The AI application that uses tools and context | Claude Desktop, VS Code, or any MCP-enabled app |
| **MCP Client** | A connection created by the host to talk to one server | One client per server |
| **MCP Server** | Exposes a specific capability (tools, resources, prompts) | Filesystem, GitHub API, Slack, database, etc. |

The **host** creates multiple **clients**, and each client connects to a dedicated **server**. That way, one app can use many tools at once while keeping each integration isolated.

### Transport Mechanisms

How the client and server communicate:

**STDIO (local)**  
`Client → stdin/stdout → Server`  
Best for local tools running on the same machine as the host.

**Streamable HTTP (cloud / remote)**  
`Client → POST / SSE → Server`  
The server runs as an HTTP service and supports standard auth (OAuth, tokens). Better suited for remote or cloud-hosted MCP servers.
