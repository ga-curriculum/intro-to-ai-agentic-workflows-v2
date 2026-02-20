<h1>
  <span class="headline">Intro to AI Agentic Workflows</span>
  <span class="subhead">Foundations of Agentic Workflows</span>
</h1>

## Learning Objectives  
By the end of this lesson, you will be able to:  
- **Explain** the core components of AI agentic workflows.  
- **Understand** how structured frameworks enable AI autonomy.  
- **Recognize** key challenges and trade-offs in designing agentic systems.  

## Introduction  

AI **agentic workflows** go beyond traditional AI applications by incorporating **decision-making, iterative feedback loops, and multi-agent collaboration**. These workflows allow AI systems to **act autonomously**, interact with external environments, and **adapt dynamically** based on feedback.

Unlike static LLM-based applications, agentic AI systems are built using **structured frameworks** that define:  

- **Perception** – How an AI agent **interprets input** (e.g., user prompts, API data).  
- **Reasoning** – How the agent **processes information and makes decisions**.  
- **Action** – How the agent **executes tasks** (e.g., retrieving data, generating text). Emerging standards like **MCP (Model Context Protocol)** are defining a universal interface for how agents perform tool-based actions, enabling any compliant agent to work with any compliant tool.
- **Feedback Loops** – How the agent **evaluates and refines its performance** over time.  

<div class="mermaid">
graph TD;
    A[AI Agentic System]:::main -->|Receives Input| B(Perception):::perception;
    B -->|Processes Data| C(Reasoning):::reasoning;
    C -->|Makes Decisions| D(Action):::action;
    D -->|Executes Task| E[External Environment]:::environment;
    E -->|Provides Response| F(Feedback Loop):::feedback;
    F -->|Refines Strategy| C;
</div>

These components work together to enable **AI-driven automation, problem-solving, and collaboration** across different domains.

## **Key Concepts of Agentic Workflows**

| Concept | Description | Example |
| ------- | ----------- | ------- |
| **AI Agent** | A system that **perceives, reasons, and acts autonomously**. | A chatbot that **retrieves answers and refines responses** based on user feedback. |
| **Agentic Workflow** | A structured approach where agents **take actions, process feedback, and optimize results**. | A customer support AI that **revises solutions based on sentiment analysis**. |
| **Multi-Agent Collaboration** | AI agents working **together**, each with a specialized role. | One agent extracts data, another interprets it, and a third agent generates a summary. |
| **Feedback Loop** | A mechanism that **adjusts AI actions based on results**. | A recommendation system that **refines suggestions based on user clicks**. |

## **Quick Discussion: Analyzing an Agentic Workflow**
**Goal:** Identify **key components** in an existing agentic workflow.

**Instructions:**  
1. Open this example AI agent workflow in [LangChain](https://python.langchain.com/docs/tutorials/agents/).  
2. Analyze its structure:  
   - **What is the agent's goal?**  
   - **How does it process input?**  
   - **What actions does it take?**  
   - **Does it have a feedback loop?**  
3. As a group, write a **brief summary** (3-5 sentences) describing the workflow.  
4. Be prepared to discuss your summaires with the class! 

