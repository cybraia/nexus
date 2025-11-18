# 🚀 Building an Intelligent Multi-Agent System on Google Cloud Platform with Gemini: Nexus

In today’s rapidly evolving AI landscape, building intelligent applications often requires more than just a single large language model (LLM). It demands sophisticated **orchestration**, **specialized agents**, and robust cloud infrastructure. This post delves into the architecture and implementation of **Nexus**, an intelligent multi-agent system designed to dynamically understand, process, and act upon complex user requests, all powered by Google’s **Gemini models** and deployed entirely on **Google Cloud Platform (GCP)**.

-----

## 🤖 Meet Nexus: Your Intelligent Event Planner

The project, **“Nexus,”** is designed to be your ultimate intelligent assistant for social planning and event management. Nexus aims to:

  * **Plan Events:** From casual meetups to tech events, Nexus understands your event needs and helps coordinate all the details.
  * **Create Events:** Once planned, Nexus can formalize and create the event within the platform, handling invitations and setup.
  * **Manage Social Circles:** It leverages social context to check past attendees, personalize communications, and understand group dynamics.
  * **Personalize Experiences:** By understanding user preferences and social graphs, Nexus offers tailored recommendations and experiences.


### The Vision: Why Multi-Agent for Nexus?

The goal was to create a system capable of handling diverse tasks, from event planning to social profiling, in a highly autonomous yet controlled manner. This led us to a **multi-agent approach** where:

  * **Specialized Agents** excel at specific tasks (e.g., social context, platform interaction).
  * An **Orchestrator Agent** manages complex workflows, delegating tasks and integrating results.
  * **Large Language Models (Gemini)** serve as the **“brains”** for reasoning and decision-making within each agent.

This distributed intelligence makes the system more **modular, scalable, and maintainable**.

-----

## 🏗️ High-Level Architecture

The architecture divides into four key domains: **Clients/Actors**, **Agents**, **AI/Data Sources**, and **GCP Infrastructure**. This separation ensures clear responsibilities and leverages GCP’s managed services.

-----

## 💻 Implementation Details: Diving into the Stack

### 1\. The Brains: Gemini Models on Vertex AI

The agents derive their intelligence from Google’s Gemini models, specifically `gemini-2.0-flash`, hosted on **Vertex AI**.

  * **Advanced Reasoning:** Crucial for understanding complex prompts.
  * **Tool Use (Function Calling):** Gemini's ability to determine when and how to call external functions (tools) is fundamental for interacting with data and other agents.
  * **Efficiency:** The **"flash"** variants provide an optimal balance of performance and cost.

### 2\. Building Blocks: Google Agent Development Kit (ADK)

The **Google Agent Development Kit (ADK)** is the primary framework for constructing the agents, defining their core behavior, instruction sets, and state management.


### 3\. Agent-to-Agent (A2A) Communication Protocol

A cornerstone of the system is the **A2A Communication Protocol**, implemented via the `a2a-python` library, providing a standardized way for agents to discover, communicate, and collaborate.

  * **Delegation & Orchestration:** The Orchestrator Agent delegates sub-tasks to specialized agents (e.g., fetching user relationships).
  * **Implementation Detail:** Communication uses a **JSON-RPC standard over HTTP**.

<!-- end list -->

```python
class SocialAgent:
  """An agent that handles social profile analysis."""

  # ... (agent definition)

  skill = AgentSkill(
                id="social_profile_analysis",
                name="Analyze social profile",
                description="""
                Using a provided list of names, this agent synthesizes the social profile information by analyzing posts, friends, and events.
                It delivers a comprehensive single-paragraph summary for individuals, and for groups, identifies commonalities...
                """,
                tags=["nexus"],
                examples=["Can you tell me about Dev and Riya?"],
    )
  # ...
```

### 4\. Orchestration: Vertex AI Agent Engine

The overall workflow and coordination are managed by an **Orchestrator Agent** deployed on the **Vertex AI Agent Engine**, a managed service optimized for hosting orchestrating agents.

### 5\. Specialized Agents on Cloud Run

The individual ADK agents are deployed as independent microservices on **Cloud Run**:

  * **Planner Agent ADK**
  * **Social Agent ADK**
  * **MCP Client Agent ADK**


#### Implementation Detail: Agent Tooling and Function Calling

Custom Python functions (**tools**) are defined within the Planner Agent. Gemini's **Function Calling** capability, facilitated by the ADK, dynamically invokes these tools based on the user's prompt.

```python
def create_event(event_name: str, description: str, event_date: str, locations: list, attendee_names: list[str], base_url: str = BASE_URL):
    """
    Sends a POST request to the /events endpoint to create a new event registration.
    
    Args:
        event_name (str): The name of the event.
        # ... (other arguments)

    Returns:
        dict: The JSON response from the API if the request is successful.
    """
    url = f"{base_url}/events"
    # ... (API call logic)
```

### 6\. Data Foundation: Spanner as a Graph Database

**Spanner** is leveraged to manage complex social relationships (users, friendships, event attendance) due to its global consistency and scalability.

  * **Graph DDL & Queries:** We utilize Spanner’s **GRAPH DDL** and query features for modeling intricate relationships and performing efficient graph traversal.

> \*\*

### 7\. Platform Integration: Model Context Protocol (MCP)

The **Model Context Protocol (MCP)** enables agents to interact with Nexus’s existing application APIs in a standardized way.

  * The **MCP Server** (a Cloud Function) exposes the platform’s internal APIs as **“tools”** that the **MCP Client Agent** can call.

<!-- end list -->

```python
# MCP Server snippet
app = Server("adk-tool-mcp-server")

@app.list_tools()
async def list_tools() -> list[mcp_types.Tool]:
  """MCP handler to list available tools."""
  # Convert the ADK tool's definition to MCP format
  # ...
  return [mcp_tool_schema_event,mcp_tool_schema_post]
```

-----

## 🛠️ Step-by-Step Implementation Guide

### 1\. Foundation and Data Layer Setup

1.  **Set up Spanner Database**
2.  **Set up CI/CD Artifacts**
3.  **Deploy Application Core**
4.  **Deploy MCP Server**

### 2\. Specialized Agent Development & Deployment

1.  **Develop and Deploy the Planner Agent** (ADK + Cloud Run)
2.  **Develop and Deploy the Social Agent** (ADK + Spanner + Cloud Run)
3.  **Develop and Deploy the MCP Client Agent** (ADK + MCP Server endpoint + Cloud Run)

### 3\. Orchestration and Final Connection

1.  **Develop the Orchestrator Agent** (ADK)
2.  **Deploy the Orchestrator Agent** (Vertex AI Agent Engine)
3.  **Connect Nexus App to Orchestrator**
4.  **Final System Validation**

-----

## 🔮 What’s Next?

Future development will focus on:

### Security & User Management

  * **User Authentication (AuthN)**
  * **Agent Authorization (AuthZ)**

### Advanced Agent Capabilities

  * **Dedicated Personalization Agent**
  * **Persistent Agent Memory**

-----

## 👋 Connect with Me

This blog is published as a part of Build & Blog Marathon 2025.

| Platform | Link |
| :--- | :--- |
| **LinkedIn** | `https://www.linkedin.com/in/krishna-n-mehta/` |
| **Medium** | `https://medium.com/@27krishna2002` |
| **Github** | `https://github.com/cybraia` |

### References

  * [From Prototypes to Agents with ADK | Google Codelabs](https://codelabs.developers.google.com)
  * [MCP Toolbox for Databases](https://medium.com)
  * [Building Multi-Agent AI App with Google’s A2A (Agent2Agent) Protocol, ADK, and MCP - A Deep Dive](https://medium.com)
