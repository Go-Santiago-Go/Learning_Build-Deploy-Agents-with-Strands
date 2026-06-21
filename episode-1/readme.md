# S1E1: The Anatomy of an Agent

These are distilled notes from [Build & Deploy Agents with Strands | S1E1 | The Anatomy of an Agent](https://www.youtube.com/watch?v=HFLZT01UVqc), where AWS experts break down how to actually build AI agents using the Strands Agents SDK and Bedrock Agentcore. This is a three-part series taught by AWS Experts Raf Lopes and Morgan Willis.

Follow along with the [official repo](https://github.com/morganwilliscloud/strands-livestream-samples) provided by Morgan Willis.

---

## Learning Objectives

1. Understand the difference between AI models, harnesses, and agents, and how the Strands Agents SDK provides the primitives to build your own agent harness.
2. Learn how to configure model choices, apply prompt engineering techniques, create local tools, and connect to remote MCP servers using the Strands Agents SDK.

### Who Should Attend?

Developers, AI/ML Engineers, Solutions Architects, Cloud Engineers, Technical Decision Makers

---

## Understanding AI Agents ([0:36](https://www.youtube.com/watch?v=HFLZT01UVqc&t=36s) - [9:49](https://www.youtube.com/live/HFLZT01UVqc?si=9EDJIe5XkdyJSLQ7&t=589))

### What is an Agent?

An AI Agent is an AI-driven program that can autonomously or semi-autonomously achieve tasks by interacting with the real world.

While a baseline Large Language Model (LLM) is strictly limited to static text-in and text-out operations, an agent combines that raw inference capability with external capabilities to run in a continuous evaluation loop. The model alone can only touch words, tokens, and inference — it cannot interact with the world. The harness is what makes that possible.

#### The Historical Definition

Traditionally, an agent was defined as a simple combination of elements running on an evaluation loop:

$$\text{Agent} = \text{LLM} + \text{Tools} + \text{Loop(s)}$$

#### The Modern Definition

As developer tooling has matured post-Q1 2026, the industry has shifted to a cleaner abstraction:

$$\text{Agent} = \text{Model} + \text{Harness}$$

The model provides the brain (reasoning and token generation), while the harness provides the body (the surrounding application infrastructure). Just as a human is the combination of brain and body, an agent is the combination of model and harness.

---

## Deep Dive: What is a Harness? ([6:49](https://www.youtube.com/watch?v=HFLZT01UVqc&t=409s))

The harness is the surrounding application environment that allows your agent to safely run, interact, and execute tasks. It is best understood as two distinct operational layers:

#### Layer 1: The Orchestration Code (Deterministic Logic)

This is the deterministic code you write *around* the model to manage its lifecycle and execute its decisions. It includes:

- **Tool Execution:** Interfacing with external systems when the model requests a tool.
- **Context & Memory Management:** Dynamically pulling relevant data from short-term conversation context or long-term storage.
- **Workflow Control:** Structuring multi-step execution flows, handling lifecycle hooks, and implementing guardrails.

#### Layer 2: The Application Infrastructure (Compute & Operations)

An agent cannot function out in the open; it requires a robust operational backend to execute actions safely:

- **Secure Execution Sandboxes:** Isolated compute environments where the agent can run code natively without risking host infrastructure.
- **Compute Resources:** Hosting environments to run the agent applications continuously.
- **Observability & Operations:** Centralized logging, token tracking, error handling, failure recovery, security management, and identity controls.

<img width="850" height="564" alt="image" src="https://github.com/user-attachments/assets/5d17c0d1-eca1-4a64-bcec-ed4e71755769" />

---

### How an Agent Operates: The Perception-Action Loop ([8:11](https://www.youtube.com/watch?v=HFLZT01UVqc&t=491s))

An agent system continuously cycles through its environment using its harness to achieve its target. Input comes in, the model determines if it needs tools (looping back and forth as needed), and then asks: "Am I done?" If no, it reasons again and runs the full loop. Whenever it determines the task is complete, it produces the final output.

```text
[User Goal] ➔ ( Plan ) ➔ ( Select Tool ) ➔ ( Call Tool/API ) ➔ ( Evaluate Result ) ➔ [Achieved?]
                 ▲                                                         │
                 └──────────────────────── No ──────────────────────────────┘
```

> [!TIP]
> **Key Takeaway**
>
> An agent is an AI-driven program that can perceive its environment, interact with the world via tools, and perform tasks autonomously or semi-autonomously using a looping logic.

### The Real Differentiator: Model vs. Harness ([9:10](https://www.youtube.com/watch?v=HFLZT01UVqc&t=550s))

As the AI landscape matures, raw intelligence is becoming a commoditized utility. Industry-leading models (like those from Anthropic) are widely accessible to everyone, meaning you can no longer compete purely on the choice of your underlying LLM.

The true differentiator between a basic "AI wrapper" and a production-grade agent application comes down to the quality of the harness:

- **The Commodity:** Anyone can send a prompt to an API. The baseline model provides the exact same core reasoning and token generation capabilities to all developers.
- **The Competitive Advantage:** The ultimate utility, reliability, and capability of your agent are entirely defined by how well your harness is engineered.

Your custom orchestration logic, secure sandboxing, memory management, and tool integration are what actually transform a generic text model into a highly specialized, autonomous system.

> [!NOTE]
> **The App-Layer Evolution: Amazon Quick**
>
> While frameworks like the Strands Agents SDK and Bedrock AgentCore provide the infrastructure code (the "harness") for engineers to build agents from scratch, AWS launched Amazon Quick (released April 28, 2026) as the out-of-the-box application layer. Replacing Amazon Q Business, Quick is an autonomous enterprise workspace that connects to Slack, databases, and internal tools via MCP, letting non-technical teams deploy and monitor pre-built autonomous agents without writing custom orchestration code.

---

## Building AI vs. Building *with* AI ([10:02](https://www.youtube.com/watch?v=HFLZT01UVqc&t=602s))

A critical distinction the instructors draw early on — one that causes widespread confusion:

- **Building *with* AI agents** means using existing AI-powered tools (e.g., Kiro, Copilot, Claude Code) to accelerate your own development work. You are a *consumer* of agents. The output is your work product, not an agent itself.
- **Building *AI agents*** means writing code using a framework like the Strands Agents SDK to construct a custom agent harness from scratch. The output *is* the agent. Examples: a custom customer service bot, a workflow automation engine, a knowledge-base chatbot.

> Not every application created with AI needs to be an AI application. And you can build AI applications without using another agent to help you code them.

The key signal for "building AI agents" is the word **custom** — you are writing code, defining tools, constructing the orchestration logic, and choosing your own model. Frameworks like Strands are what make this possible.

---

## RAG and the Agentic Harness ([14:45](https://www.youtube.com/watch?v=HFLZT01UVqc&t=885s))

Retrieval-Augmented Generation (RAG) serves as a critical mechanism for connecting an agent to proprietary, private, or real-time data sources that were not part of the underlying foundation model's static training data.

#### How RAG Fits into the Harness

Within the modern agent architecture ($\text{Agent} = \text{Model} + \text{Harness}$), RAG operates directly as a collection of specialized tools managed by the harness:

- **Overcoming Model Limitations:** Base foundation models have strict knowledge cutoff dates and zero inherent awareness of your internal corporate wikis, databases, or private files.
- **Tool-Driven Retrieval:** Instead of attempting to force an entire corporate database into a model's limited context window, the harness wraps data access points into semantic search tools.
- **The Execution Flow:** When an agent encounters a query requiring specialized internal knowledge, the model instructs the harness to execute a RAG query tool. The harness performs a semantic search against a vector database, extracts the relevant document snippets, and feeds them back into the model's context window so it can generate an accurate, data-backed response.

> [!NOTE]
> RAG is definitively part of the harness layer. You do not retrain the model — you augment it with tools that query your data sources at runtime. This is the same pattern used for memory: the agent extracts facts from a conversation in the background, stores them in a vector or key-value store, and retrieves them semantically in future turns.

---

## The Strands Agents SDK Framework ([16:15](https://www.youtube.com/live/HFLZT01UVqc?si=LuF8dfDDQnfnjMXk&t=974))

### Introduction to the Strands Agents SDK [[16:17](https://www.youtube.com/watch?v=HFLZT01UVqc&t=977s)]

The [Strands Agents SDK]((https://strandsagents.com/)) is an open-source agent harness framework built by AWS. It provides developers with the essential software primitives required to construct a custom agent application backend from scratch. It has both a **Python SDK** and a **TypeScript SDK**.

- **The Problem It Solves:** AWS originally developed the framework internally because engineering teams were running into significant friction when trying to force AI models to follow highly rigid, deterministic, and tightly hard-coded workflows.
- **The Model-Driven Paradigm Shift:** Traditional frameworks (like LangChain or LangGraph) typically require developers to map out strict, explicit conditional code paths (do Step A ➔ if X, do Step B ➔ else do Step C). Strands completely flips this approach by leveraging a model-driven architecture.
- **How It Orchestrates:** Instead of scripting a step-by-step flowchart, developers supply the agent with a high-level goal, an operational boundaries prompt, and a collection of functional tools. The framework then trusts the core reasoning capability of the underlying model to dynamically evaluate the environment, select the appropriate tools, adapt to friction, and chart its own execution path to resolve the objective.

---

## Initial Prototyping: Building a Stateless Agent [[17:30](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1050s)]

At its most fundamental level, the Strands Agents SDK allows you to spin up an operational agent loop in just a few lines of code. However, a baseline agent with no tools or memory acts as a purely stateless text generator.

### Minimal Python Implementation

To build a vanilla agent that hooks into the default execution loop, you instantiate the Agent primitive and pass a direct user query:

```python
from strands import Agent

# 1. Instantiate a default agent
agent = Agent()

# 2. Execute a single-turn query inside the loop
result = agent("Explain the core components of an agent execution loop.")
print(result)
```

### How the Stateless Loop Executes

When you call the agent instance directly with a prompt, the framework abstracts the underlying architecture behind a simple input/output interface:

```text
[ User Prompt ]
        │
        ▼
┌───────────────┐
│  Agent Loop   │ ◄──── Stateless (No conversation history preserved)
└───────┬───────┘
        │
        ▼
┌───────────────┐
│   LLM Brain   │ ────► Runs raw token inference
└───────┬───────┘
        │
        ▼
 [ Final Output ]
```

- **Stateless by Default:** This basic configuration is entirely ephemeral. The three-line execution does not preserve context or state. If you ask a follow-up question in a subsequent command, the agent will have no memory of the prior interaction.
- **The "Is It Done?" Evaluation:** Even in a vanilla state, the framework enters an evaluation loop. The model analyzes the input, determines if it can resolve the request immediately without external resources, and outputs the final tokens.

> [!NOTE]
> The default model is **Claude Sonnet 4** (`us.anthropic.claude-4-sonnet`) via Amazon Bedrock. This can be verified on the Strands Agents SDK website, though it is subject to change as newer models are released.

---

### When is "Done" Determined? ([22:11](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1331s))

The agent uses its own internal reasoning capability — powered by the model — to decide when a task is complete. This means the "done" condition is **subjective**, not deterministic. This is an important architectural consideration:

- Use AI agents for tasks that require subjectivity, complex decision trees, or scenarios where defining every possible code execution path as explicit if/else logic would be impractical.
- Do **not** use AI agents for fully deterministic, rule-based tasks that can be cleanly handled by standard code.

---

### Swapping Model Providers ([24:06](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1446s))

The Strands Agents SDK is entirely model-agnostic. While it leverages Amazon Bedrock orchestration by default, you can seamlessly swap out the underlying "brain" to use alternative remote APIs or local developer environments. You can even run different models on different agents simultaneously within the same application — for example, routing simpler queries to a cheaper model and complex reasoning to a more powerful one.

```python
import os
from strands import Agent
from strands.models import BedrockModel, AnthropicModel, OpenAIModel

# Example 1: Explicitly defining an AWS Bedrock Model Configuration
# Uses ambient AWS credentials (e.g., ~/.aws/credentials or environment variables)
bedrock_agent = Agent(
    model=BedrockModel(
        model_id="us.anthropic.claude-3-5-sonnet-20241022-v2:0",
        max_tokens=1024,
        params={"temperature": 0.7},
    )
)

# Example 2: Swapping to an Anthropic Direct API Endpoint (Requires ANTHROPIC_API_KEY)
anthropic_agent = Agent(
    model=AnthropicModel(
        client_args={"api_key": os.environ["ANTHROPIC_API_KEY"]},
        model_id="claude-sonnet-4-20250514",
        max_tokens=1024,
        params={"temperature": 0.7},
    )
)

# Example 3: Swapping to an OpenAI Endpoint (Requires OPENAI_API_KEY)
openai_agent = Agent(
    model=OpenAIModel(
        client_args={"api_key": os.environ["OPENAI_API_KEY"]},
        model_id="gpt-4o",
        max_tokens=1024,
        params={"temperature": 0.7},
    )
)
```

> [!NOTE]
> Examples 2 and 3 do **not** require an AWS account — only the respective provider API key. Anthropic and OpenAI endpoints are hit directly.

### Prototyping Locally with Ollama ([25:53](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1553s))

For local prototyping, testing, or cost-free experimentation, you can route the agent's reasoning loop to a local compute model using Ollama. This is particularly useful when learning, since it removes any concern about cloud billing.

#### 1. Terminal Setup

First, ensure Ollama is running on your local machine and pull an open-weights frontier model (such as gemma4 or llama3 variants):

```bash
# Start the local execution server
ollama serve

# In a separate tab, pull the target model weights
ollama pull gemma4
```

#### 2. Code Configuration

Pass the local OllamaModel provider into the agent harness instance:

```python
from strands import Agent
from strands.models.ollama import OllamaModel
from strands_tools import calculator, current_time

# 1. Initialize your local Ollama model
ollama_model = OllamaModel(
    host="http://localhost:11434",
    model_id="llama3.1"
)

# 2. Attach tools to your Agent
agent = Agent(
    model=ollama_model,
    tools=[calculator, current_time]
)

# 3. Interact with your agent
response = agent("What time is it, and what is 5432 * 9876?")
print(response)
```

> [!WARNING]
> **Production Testing Caveat:** While local models are excellent for debugging harness mechanics and tool-dispatch structural code, different models handle JSON schema tool calling and formatting constraints differently. Always validate your harness code using your final production model (e.g., Claude Sonnet) before deploying infrastructure.

### Under the Hood: Default Infrastructure & Credentials [[00:23:17](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1397s)]

If you do not pass an explicit model provider to `Agent()`, the SDK defaults to a cloud-hosted ecosystem managed via Amazon Bedrock:

- **The Default Brain:** Out of the box, the SDK targets Claude Sonnet 4 (`us.anthropic.claude-4-sonnet`) via Bedrock.
- **Required AWS Credentials:** To run the default cloud-routed configuration, your local environment must have active AWS credentials configured (e.g., via `~/.aws/credentials` or your standard system environment variables like `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_DEFAULT_REGION`).
- **Model Access Authorization:** You must explicitly request and enable access to the target foundational model within the Amazon Bedrock Console in your designated deployment region to prevent access-denied runtime exceptions.

> [!NOTE]
> **AWS Bedrock Free Tier:** Bedrock falls under the AWS Free Tier. New AWS accounts receive up to $200 in credits. Once those credits are exhausted, billing begins. To avoid throttling errors, it is recommended to avoid the most in-demand models (e.g., Anthropic's Claude) during initial experimentation and instead explore other available Bedrock models.

---

## Memory & Data Handling ([20:00](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1200s) - [22:00](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1320s))

#### Augmentation vs. Training

You do not need to "train" a model to make it aware of private data. Instead, use techniques like Retrieval Augmented Generation (RAG) or memory storage to provide the agent with relevant facts.

#### Stateless by Default

The Strands SDK is stateless by default. To give an agent "memory" of a user (e.g., remembering a specific device), the developer must deliberately implement storage. The agent can extract facts from conversations in the background and store them, retrieving them later through the agentic loop.

#### Efficiency vs. Custom Training

While custom model training (e.g., on SageMaker) is possible, it is generally unnecessary for most use cases. Augmenting the model with external data and memory tools is significantly more efficient.

> [!TIP]
> **Key Takeaway**
>
> Building an AI agent requires shifting from rigid, hard-coded software paths toward model-driven orchestration. Rather than engineering complex conditional trees (if/else logic), developers provide a foundational model with a robust harness — allowing the system to dynamically plan, call deterministic tools, self-evaluate its results, and maintain state autonomously.
## Integrating Tools and Local Prototyping ([29:40](https://www.youtube.com/live/HFLZT01UVqc?si=gm9flYFt4tsrEWoP&t=1780))

### Why Tools Exist: The LLM Determinism Problem

Large Language Models (LLMs) are inherently probabilistic text-prediction engines. While they excel at reasoning and semantic understanding, they are fundamentally prone to hallucinations when tasked with deterministic logic, precise mathematical tracking, or string manipulation (such as accurately counting letters in a word).

A famous example: ask most models how many Rs are in the word "strawberry" and they return two — the correct answer is three. This is not a bug; it is a fundamental property of token-based prediction. Code, by contrast, is perfectly deterministic.

To build a reliable application, the harness must extend the model's action space by providing it with hard-coded, deterministic Python tools.

---

### Step-by-Step Tool Implementation [[00:31:15](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1875s)]

The Strands Agents SDK provides an elegant, decorator-driven approach to transforming vanilla Python functions into executable tools that the model can understand and dispatch.

#### Three Ways to Integrate Tools

The SDK supports three distinct tool integration patterns:

- **Custom Local Tools:** Functions decorated with `@tool` that you write and run locally.
- **Built-in Community Tools:** Pre-built tools shipped with the SDK (e.g., `calculator`, `current_time`, `http_request`).
- **Remote MCP Server Tools:** Tools hosted on an external server and consumed via the Model Context Protocol.

#### 1. Defining a Custom Tool with the `@tool` Decorator

You use the `@tool` decorator to register a Python function as an agent-callable tool. The framework relies heavily on **Python type hints** and **docstrings** to auto-generate the underlying JSON schema that is sent to the LLM. The model reads the docstring to understand when and how to use the tool.

> [!NOTE]
> If you do not provide a docstring, the SDK will infer the tool's purpose from its type hints alone. Providing a clear docstring is strongly recommended for complex tools.

```python
from strands_tools import tool

@tool
def calculator(a: float, b: float, operation: str) -> float:
    """
    A deterministic calculator tool to perform basic arithmetic operations.

    Args:
        a (float): The first numerical operand.
        b (float): The second numerical operand.
        operation (str): The operator to apply ('add', 'subtract', 'multiply', 'divide').

    Returns:
      The float point value resulting from the arithmatic operation
    """
    if operation == 'add':
        return a + b
    elif operation == 'subtract':
        return a - b
    elif operation == 'multiply':
        return a * b
    elif operation == 'divide':
        return a / b if b != 0 else float('inf')
    return 0.0
```

#### 2. Creating an Environmental Context Tool

Tools can also be used to feed real-time environmental context into the model's execution window, bypassing its static training data cutoff limitations.

```python
import datetime
from strands_tools import tool

@tool
def current_time() -> str:
    """
    Retrieves the current system date and time. Use this whenever the user asks
    about the current time, day, or date context.
    """
    return datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
```

### Attaching Tools to the Agent Harness [[00:32:41](https://www.youtube.com/watch?v=HFLZT01UVqc&t=1961s)]

Once your functions are decorated, you register them inside the `Agent` instantiation by passing them into the `tools` array primitive. You can also pass in a **system prompt** at this stage to define the agent's persona and behavioral boundaries.

```python
from strands import Agent
from strands.models.ollama import OllamaModel
from strands_tools import calculator, current_time

# Initialize your local or cloud model provider
ollama_model = OllamaModel(
    host="http://localhost:11434",
    model_id="llama3.1"
)

# Bind the tools directly to the agent's harness space
agent = Agent(
    model=ollama_model,
    system_prompt="You are a personal assistant. Be professional, warm, and helpful in all interactions.",
    tools=[calculator, current_time]
)

# Execute an evaluation query that forces parallel tool selection
response = agent("What time is it right now, and what is 5432 * 9876?")
print(response)
```

### The Behind-the-Scenes Tool Calling Loop [[00:34:05](https://www.youtube.com/watch?v=HFLZT01UVqc&t=2045s)]

When the agent executes a complex query requiring tools, the interaction between the model and the harness shifts into a multi-turn negotiation loop:

```text
[ User Query ] → "What time is it, and what is 5432 * 9876?"
        │
        ▼
┌───────────────┐
│   LLM Brain   │ → Recognizes it cannot compute math/time natively.
└───────┬───────┘ → Returns a "Tool Call Request" payload with arguments.
        │
        ▼
┌───────────────┐
│ Agent Harness │ → Intercepts the payload and executes python functions locally.
└───────┬───────┘ → Captures outputs: ("15:42:00", 53646432).
        │
        ▼
┌───────────────┐
│   LLM Brain   │ → Evaluates the data snippets injected into its context.
└───────┬───────┘ → Synthesizes the final, hallucination-free response.
        │
        ▼
[ User Sees ] → "The current time is 3:42 PM, and 5432 * 9876 equals 53,646,432."
```

- **Parallel Dispatch:** Modern frontier models can request multiple tool calls simultaneously in a single turn. The harness will execute both `current_time()` and `calculator()` concurrently before feeding the results back to the model, significantly reducing total latency.
- **Inspecting Agent Messages:** You can dump the full `agent.messages` context to see every tool call request, tool result, and token count — invaluable for debugging and understanding the full agent loop in action.

> [!TIP]
> **Key Takeaway**
>
> LLMs are prone to hallucinations with logic tasks; providing deterministic code-based tools significantly improves accuracy and reliability.

---

## Remote MCP Server Integration ([40:33](https://www.youtube.com/live/HFLZT01UVqc?si=XYzexample&t=2433))

### What is MCP? [[00:41:01](https://www.youtube.com/watch?v=HFLZT01UVqc&t=2461s)]

The **Model Context Protocol (MCP)** is an open standard that defines a uniform way for agents to discover and invoke tools hosted on external servers. It has been described as "the USB-C of AI tool integration" — a single, standardized plug that works regardless of which agent framework or tool server you are using.

[MCP Documentation on AWS](https://awslabs.github.io/mcp/)

#### Local Tools vs. MCP: When to Use Each

| Scenario | Recommended Approach |
|---|---|
| Single agent using a small set of custom tools | Local `@tool` decorator — no MCP needed |
| Multiple agents that need to share the same tool set | Host tools on an MCP server |
| Consuming third-party tools someone else built | Connect to their public MCP server |
| Tools require a persistent server process or remote hosting | Streamable HTTP MCP client |

#### Why MCP Matters: The Shared Tool Registry

Without MCP, every agent that needs the same capability requires its own copy of the tool code. With an MCP server:

- A single tool update is instantly available to every connected agent.
- Tool authors can publish their servers publicly (e.g., on GitHub) for the entire community to consume.
- The agent harness does not need a bespoke integration per tool type — MCP standardizes discovery and invocation.

### The Two MCP Transport Methods [[00:45:16](https://www.youtube.com/watch?v=HFLZT01UVqc&t=2716s)]

The Strands SDK MCP client supports two distinct transport layers depending on where your tools are hosted. In both cases, `MCPClient` takes a `lambda` — a callable that returns the transport — so the connection is only opened when the context manager is entered, not at instantiation time.

#### Streamable HTTP (Remote Server)

Used when the MCP tools are hosted on a remote server endpoint. Pass a `lambda` that returns a `streamablehttp_client` pointed at the server URL. The optional `prefix` argument namespaces the server's tools to avoid naming collisions when using multiple MCP servers.

```python
import os
from strands.tools.mcp import MCPClient
from mcp import StdioServerParameters
from mcp.client.streamable_http import streamablehttp_client
from mcp.client.stdio import stdio_client

# Remote MCP server (Streamable HTTP) — AWS docs, best practices, architecture guidance
aws_knowledge = MCPClient(
    lambda: streamablehttp_client("https://knowledge-mcp.global.api.aws"),
    prefix="knowledge"
)
```

#### Standard I/O (Local Subprocess)

Used for MCP servers that run as a local process on your machine. Pass a `lambda` that returns a `stdio_client` wrapping a `StdioServerParameters` object. The SDK **automatically spins up the subprocess** for you. The `env` dict passes environment variables (AWS credentials, region, log level) directly to the spawned process.

```python
# Local MCP server (stdio via uvx) — real-time AWS pricing data
aws_pricing = MCPClient(
    lambda: stdio_client(StdioServerParameters(
        command="uvx",
        args=["awslabs.aws-pricing-mcp-server@latest"],
        env={
            "FASTMCP_LOG_LEVEL": "ERROR",
            "AWS_PROFILE": os.environ.get("AWS_PROFILE", "default"),
            "AWS_REGION": os.environ.get("AWS_REGION", "us-east-1"),
        }
    ))
)
```

> [!WARNING]
> **MCP Security Caveat:** When you connect to a remote MCP server, your agent's tool call payloads — including user queries and context — are transmitted to that server. Always verify that the MCP server URL is from a trusted source before connecting to it in a production environment.

### Live Demo: AWS Solutions Architect Agent [[00:43:47](https://www.youtube.com/watch?v=HFLZT01UVqc&t=2627s)]

The following example wires up an agent with two publicly available AWS MCP servers — one for real-time documentation lookups and one for live pricing data — to create a well-architected AWS assistant that never relies on stale training data.

```python
import os
from strands import Agent
from strands.tools.mcp import MCPClient
from mcp import StdioServerParameters
from mcp.client.streamable_http import streamablehttp_client
from mcp.client.stdio import stdio_client

# MCP Server 1: Real-time AWS documentation, API references, and architectural guidance
aws_knowledge = MCPClient(
    lambda: streamablehttp_client("https://knowledge-mcp.global.api.aws"),
    prefix="knowledge"
)

# MCP Server 2: Live AWS pricing data (avoids relying on outdated training data)
aws_pricing = MCPClient(
    lambda: stdio_client(StdioServerParameters(
        command="uvx",
        args=["awslabs.aws-pricing-mcp-server@latest"],
        env={
            "FASTMCP_LOG_LEVEL": "ERROR",
            "AWS_PROFILE": os.environ.get("AWS_PROFILE", "default"),
            "AWS_REGION": os.environ.get("AWS_REGION", "us-east-1"),
        }
    ))
)

SYSTEM_PROMPT = """
You are an AWS Solutions Architect. Help design and build well-architected solutions on AWS.
Use the available tools to provide accurate and up-to-date guidance on AWS services,
architecture patterns, and pricing. When a user asks about cost, use the pricing tools
to give real numbers. Always cite relevant documentation when possible.
"""

architect_agent = Agent(
    system_prompt=SYSTEM_PROMPT,
    tools=[aws_knowledge, aws_pricing]
)

response = architect_agent("What is the cost to run a t3.medium EC2 instance in us-east-1?")
print(response)
```
> [!TIP]
> **Key Takeaway**
>
> MCP decouples tool implementation from agent implementation. You can build highly capable agents by composing publicly available MCP servers — without writing a single line of tool code yourself.

---

## Building with AI vs. Building AI ([48:53](https://www.youtube.com/live/HFLZT01UVqc?si=example&t=2933))

A key conceptual distinction the instructors draw at the end of the session:

- **Building *with* AI agents** means using existing AI-powered tools like Kiro, Cursor, or GitHub Copilot to accelerate your own development workflow. You load your codebase, give it an instruction, and the tool's internal agents handle the execution.
- **Building *AI agents*** means using a framework like the Strands Agents SDK to construct the agent harness itself from scratch — the orchestration code, tool bindings, system prompts, and execution loop. This is what Strands is designed for.

You can use both simultaneously: use Kiro to *help you write* your Strands agent code, but the resulting agent is your own custom-built system.
