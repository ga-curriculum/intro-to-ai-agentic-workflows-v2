<h1>
  <span class="headline">Intro to AI Agentic Workflows</span>
  <span class="subhead">Emerging Trends in Agentic AI</span>
</h1>

## Learning Objectives
By the end of this lesson, you will be able to:
- **Describe** recent advancements in AI agent design and workflows.
- **Explain** how MCP (Model Context Protocol) standardizes how agents connect to tools and external systems.
- **Implement** and test emerging agentic AI techniques, including MCP tool integration, reflection agents, and multi-agent collaboration.
- **Describe** how the A2A (Agent-to-Agent) protocol enables cross-vendor agent interoperability.
- **Evaluate** the impact of feedback loops and self-improving mechanisms in AI agent workflows.

## Introduction
Agentic AI systems are evolving rapidly, moving beyond custom one-off implementations toward **open standards** that enable interoperability, **self-improving mechanisms**, and scalable **multi-agent collaboration**. Unlike traditional AI workflows, these newer approaches allow agents to connect to any tool through standardized protocols, collaborate across frameworks, and refine their responses over time. This lesson explores three key trends shaping the future of agentic AI:

1. **MCP (Model Context Protocol)** - A standardized protocol for connecting agents to tools, data sources, and external systems.
2. **Reflection Agents** - Agents that evaluate past outputs and refine their responses.
3. **Multi-Agent Collaboration + A2A** - Agents working together across frameworks and vendors through the Agent-to-Agent protocol.

> **Note on LangChain code:** You will still see `AgentType.ZERO_SHOT_REACT_DESCRIPTION` in LangChain examples throughout this module. This is LangChain's internal implementation name for its default agent executor — not the concept being taught here. The focus of this lesson is on MCP and A2A as the emerging open standards for agent design.

---

## Trend 1: MCP — Model Context Protocol
In early agentic systems, every tool connection was custom-built: a developer had to write bespoke code to connect an agent to a database, an API, or a file system. This worked for small projects but did not scale. **MCP (Model Context Protocol)** solves this by defining a universal standard interface between an AI agent (the client) and any external tool or data source (the server).

Think of MCP as **USB-C for AI agents** — a single standardized connector that works with any compliant tool, regardless of who built it.

<div class="mermaid">
graph LR;
    A[AI Agent - MCP Client] -->|Standard Protocol Request| B[MCP Server: Database];
    A -->|Standard Protocol Request| C[MCP Server: Search API];
    A -->|Standard Protocol Request| D[MCP Server: File System];
    B -->|Structured Response| A;
    C -->|Structured Response| A;
    D -->|Structured Response| A;
</div>

### How MCP Works
An MCP server exposes **three types of resources** an agent can interact with:

| Resource Type | Description | Example |
|---|---|---|
| **Tools** | Functions the agent can call to take actions | Run a database query, send an email |
| **Resources** | Data sources the agent can read | Customer records, documents, logs |
| **Prompts** | Pre-defined prompt templates the server provides | Standard report formats, analysis templates |

The agent (MCP client) discovers what a server offers, then calls the appropriate resource — all through the same standardized interface.

### Conceptual MCP Example

```python
# MCP server definition (the tool provider)
# In a real implementation this would use the MCP SDK (e.g., pip install mcp)

class CustomerDataMCPServer:
    """
    An MCP-compliant server exposing customer data tools.
    Any MCP-compatible agent can connect to and use these tools
    without custom integration code.
    """

    def list_tools(self):
        """Advertises available tools to any connecting agent."""
        return [
            {
                "name": "get_customer_profile",
                "description": "Retrieves a customer profile by ID.",
                "parameters": {"customer_id": "string"}
            },
            {
                "name": "get_purchase_history",
                "description": "Returns the last N purchases for a customer.",
                "parameters": {"customer_id": "string", "limit": "integer"}
            }
        ]

    def call_tool(self, tool_name: str, parameters: dict):
        """Executes the requested tool and returns a structured response."""
        if tool_name == "get_customer_profile":
            return {"customer_id": parameters["customer_id"], "name": "Jane Doe", "tier": "Gold"}
        if tool_name == "get_purchase_history":
            return {"purchases": ["Item A", "Item B", "Item C"]}
        return {"error": "Tool not found"}


# MCP client (the agent side)
class MCPAgentClient:
    """
    An agent that connects to any MCP server and uses its tools
    without needing to know how they are implemented.
    """

    def __init__(self, server: CustomerDataMCPServer):
        self.server = server

    def run(self, customer_id: str):
        # Step 1: Discover available tools
        tools = self.server.list_tools()
        print(f"Available tools: {[t['name'] for t in tools]}")

        # Step 2: Call a tool using the standard interface
        profile = self.server.call_tool("get_customer_profile", {"customer_id": customer_id})
        print(f"Customer Profile: {profile}")

        history = self.server.call_tool("get_purchase_history", {"customer_id": customer_id, "limit": 3})
        print(f"Purchase History: {history}")


# Run the agent
server = CustomerDataMCPServer()
agent = MCPAgentClient(server)
agent.run("CUST-001")
```

### Why MCP Matters
- **Interoperability**: Any MCP-compliant agent works with any MCP-compliant tool — no custom glue code required.
- **Ecosystem growth**: Tool builders publish MCP servers once; all agents benefit automatically.
- **Security**: MCP defines clear boundaries — the agent can only do what the server exposes.
- **Standardization**: Reduces the fragmentation of bespoke tool integrations across teams and vendors.

### Discussion Questions
- How does standardizing tool connections change the way organizations think about building and scaling AI agents?
- What security considerations arise when an agent can dynamically discover and call tools it has never seen before?

---

## Trend 2: Reflection Agents
Reflection agents improve by **analyzing their past outputs** and adjusting their approach over time. This technique is inspired by human learning, where iterative feedback improves future decision-making.

### Implementing a Reflection Agent

```python
from langchain.chat_models import ChatOpenAI
from langchain.agents import initialize_agent, AgentType
from langchain.tools import Tool

llm = ChatOpenAI(model="gpt-4", temperature=0.5)

reflection_memory = {}

def reflection_tool(query: str, response: str):
    """Stores and refines agent responses based on feedback."""
    if query in reflection_memory:
        reflection_memory[query].append(response)
    else:
        reflection_memory[query] = [response]
    return f"Updated knowledge for: {query}"

# Note: ZERO_SHOT_REACT_DESCRIPTION is LangChain's internal agent executor name.
reflection_agent = initialize_agent(
    tools=[Tool(name="ReflectionMemory", func=reflection_tool, description="Stores and improves responses.")],
    llm=llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True
)

# Test reflection mechanism
response = reflection_agent.run("What is machine learning?")
print(response)
```

---

## Trend 3: Multi-Agent Collaboration + A2A Protocol
Instead of using **one generalist AI agent**, multi-agent systems distribute tasks among specialized agents, improving efficiency and scalability. In early implementations, multi-agent systems required all agents to be built in the same framework. The **A2A (Agent-to-Agent) Protocol** removes this constraint.

### What is A2A?
A2A is an open protocol developed by Google (with broad industry adoption) that standardizes how agents **discover**, **communicate with**, and **delegate tasks to** other agents — regardless of what framework or vendor built them.

<div class="mermaid">
graph TD;
    A[Orchestrator Agent - LangChain] -->|A2A Protocol| B[Research Agent - CrewAI];
    A -->|A2A Protocol| C[Analysis Agent - AutoGen];
    A -->|A2A Protocol| D[Report Agent - Custom];
    B -->|Structured Result| A;
    C -->|Structured Result| A;
    D -->|Final Report| E[User];
</div>

Each agent publishes an **Agent Card** — a standardized description of its capabilities, inputs, and outputs — that other agents can discover and use to delegate tasks appropriately.

### Multi-Agent Collaboration: LangChain Example

```python
from langchain.chat_models import ChatOpenAI
from langchain.agents import initialize_agent, AgentType
from langchain.tools import Tool

llm = ChatOpenAI(model="gpt-4", temperature=0.5)

def data_retriever(query: str):
    return f"Retrieved information for: {query}"

def data_analyzer(query: str):
    return f"Analyzing data for: {query}"

retriever = Tool(name="Retriever", func=data_retriever, description="Fetches relevant data.")
analyzer = Tool(name="Analyzer", func=data_analyzer, description="Analyzes fetched data.")

# Note: ZERO_SHOT_REACT_DESCRIPTION is LangChain's internal agent executor name.
multi_agent = initialize_agent(
    tools=[retriever, analyzer],
    llm=llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True
)

response = multi_agent.run("Summarize recent advancements in AI ethics.")
print(response)
```

### Extending to A2A: Conceptual Cross-Framework Example

```python
# Conceptual A2A interaction — not a runnable example.
# In production, A2A uses HTTP + JSON schemas for agent discovery and task delegation.

class AgentCard:
    """
    Published by each agent so others can discover its capabilities.
    Equivalent to an API spec, but for agents.
    """
    def __init__(self, name, capabilities, input_schema, output_schema):
        self.name = name
        self.capabilities = capabilities
        self.input_schema = input_schema
        self.output_schema = output_schema


# A research agent built in CrewAI publishes its Agent Card
research_agent_card = AgentCard(
    name="ResearchAgent",
    capabilities=["web_search", "document_summarization"],
    input_schema={"query": "string"},
    output_schema={"summary": "string", "sources": "list"}
)

# An orchestrator agent (built in LangChain) discovers the research agent
# and delegates a task to it using the A2A protocol
class OrchestratorAgent:
    def __init__(self, available_agents: list):
        self.agents = {agent.name: agent for agent in available_agents}

    def delegate(self, task: str, agent_name: str):
        """Delegates a task to a named agent via A2A protocol."""
        if agent_name not in self.agents:
            return "Agent not found."
        agent = self.agents[agent_name]
        print(f"Delegating '{task}' to {agent.name} via A2A")
        print(f"Agent capabilities: {agent.capabilities}")
        # In a real A2A system, this sends an HTTP request to the agent's endpoint
        return f"Task '{task}' completed by {agent.name}"


orchestrator = OrchestratorAgent(available_agents=[research_agent_card])
result = orchestrator.delegate("Summarize recent AI ethics research", "ResearchAgent")
print(result)
```

### Why A2A Matters
- **Framework independence**: A LangChain orchestrator can delegate to a CrewAI specialist agent without either needing to know how the other is built.
- **Vendor neutrality**: Organizations can mix best-in-class agents from different providers.
- **Scalability**: New specialist agents can be added to a workflow by publishing their Agent Card — no changes to the orchestrator required.

### Discussion Questions
- How does A2A change the way an organization might buy or build AI agent components?
- What governance challenges arise when an orchestrator agent can autonomously delegate to agents it has never been explicitly configured to use?

---

## Coding Challenge: Experimenting with Emerging Trends
**Goal:** Extend one of the above implementations to explore a trend further.

**Tasks:**
1. Extend the MCP server example to add a **third tool** (e.g., `get_customer_support_tickets`) and update the client to call it.
2. Expand the reflection agent by **retrieving and incorporating stored responses** when the same query is asked a second time.
3. Extend the A2A orchestrator to support **two agents** and write logic to choose which agent to delegate to based on the task description.
