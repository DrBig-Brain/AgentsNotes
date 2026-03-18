# Agentic AI Notes

Welcome to my repository for Agentic AI!

This repository serves as a personal knowledge base and workspace where I document my notes, experiments, and projects related to building and understanding Agentic AI systems.

---

## Repository Structure

### 📁 `dummy-agent/`

#### `dummy-agent.ipynb`
A from-scratch implementation of a simple agent **without any framework**. Uses the Hugging Face `InferenceClient` directly with `meta-llama/Llama-3.1-8B-Instruct` to manually build the think → act → observe loop. Demonstrates:
- Crafting a system prompt with a `get_weather` tool definition
- Stopping generation before hallucinated observations (`stop=["Observation:"]`)
- Injecting real tool outputs back into the conversation
- The fundamental mechanics of how an agent loop works under the hood

---

### 📁 `smolagents/`

Experiments using the [smolagents](https://github.com/huggingface/smolagents) framework by Hugging Face.

#### `basicCodeAgent.ipynb`
Introduces `CodeAgent` — an agent that writes and executes Python code as its action step. Uses `DuckDuckGoSearchTool` and `InferenceClientModel` (Qwen3-Next-80B) to search for party music recommendations for Wayne Manor.

#### `basicToolCallAgent.ipynb`
Introduces `ToolCallingAgent` — an agent that calls tools via structured JSON (as opposed to writing code). Uses `WebSearchTool` with `Mistral-7B-Instruct-v0.2` to search for Holi party music recommendations. Also demonstrates the difference in behaviour between `CodeAgent` and `ToolCallingAgent`.

#### `customTool.ipynb`
Shows how to create a **custom tool** using the `@tool` decorator. Defines a `suggest_menu(occasion)` tool that returns a menu based on the type of party (casual, formal, superhero, custom). The agent is then used to recommend a formal menu.

#### `importInTool.ipynb`
Demonstrates how to allow **additional Python imports** inside an agent's code sandbox using `additional_authorized_imports`. The agent uses the `datetime` module to calculate at what time a party will be ready given a list of preparation tasks and their durations.

#### `customKnowledgeBasedRag.ipynb`
Builds a **RAG (Retrieval-Augmented Generation) tool** using LangChain's `BM25Retriever` and `RecursiveCharacterTextSplitter`. Defines a `PartyPlanningRetrieverTool` that retrieves themed party planning ideas from a custom knowledge base and plugs it into a `CodeAgent` to answer questions about planning a superhero party at Wayne Manor.

#### `visualAgents.ipynb`
Explores **multimodal and visual agents**. Covers:
- Passing images to a `CodeAgent` backed by `gpt-4o` (OpenAI) to identify a comic character's costume
- Building browser automation tools (`search_item_ctrl_f`, `go_back`, `close_popups`) using Selenium/Helium
- Setting up step callbacks (`save_screenshot`) to capture browser state at each agent step

#### `MultiAgentSystem.ipynb`
Implements a full **multi-agent system** with a manager and a subordinate web agent. Key components:
- A custom `calculate_cargo_travel_time` tool using the Haversine formula
- A `web_agent` (`CodeAgent` with `Qwen2.5-Coder-32B-Instruct` + Google Search + webpage visiting) that browses the web
- A `manager_agent` (`CodeAgent` with `DeepSeek-R1`) that orchestrates the web agent
- A `check_reasoning_and_plot` callback using `gpt-4o` for multimodal validation of the final map plot
- End-to-end task: find Batman filming locations worldwide, compute cargo flight times from "Gotham" (NYC), and plot them alongside supercar factory locations

---

### 📁 `AgenticRag/`

A modular **Agentic RAG (Retrieval-Augmented Generation)** pipeline built with LangGraph and BM25 retrieval. The agent answers questions about gala guests by retrieving structured guest profiles from a HuggingFace dataset and reasoning over them in a ReAct loop.

#### `dataset.py`
Loads the guest invite list from the HuggingFace `agents-course/unit3-invitees` dataset and converts each entry into a LangChain `Document` with fields for name, relation, description, and email.

#### `reteriever.py`
Creates a **BM25Retriever** over the guest documents and wraps it in a LangChain `Tool` (`guest_info_retriever`) that accepts a query and returns the top 3 matching guest profiles.

#### `app.py`
Wires everything into a **LangGraph ReAct agent**. Key components:
- `ChatHuggingFace` backed by `Qwen2.5-Coder-32B-Instruct` with the `guest_info_retriever` tool bound
- An `AgentState` with message history and an `assistant` node that invokes the LLM
- A `ToolNode` for tool execution and `tools_condition` for conditional routing
- End-to-end graph: `START` → `assistant` ↔ `tools` → response
- Example query: retrieving information about the guest "Lady Ada Lovelace"

---

### 📁 `LangGraph/`

Experiments using the [LangGraph](https://github.com/langchain-ai/langgraph) framework by LangChain.

#### `buildingBlocks.ipynb`
Introduces the **fundamental building blocks** of LangGraph. Demonstrates:
- Defining graph state with `TypedDict` (a simple `graph_state: str` schema)
- Creating nodes (`node_1`, `node_2`, `node_3`) that read and update the shared state
- Writing a **conditional edge** function (`decide_mood`) that randomly routes execution to one of two branches
- Building and compiling a `StateGraph` with `START` → `node_1` → conditional branch → `END`
- Visualising the compiled graph with Mermaid and invoking it end-to-end

#### `mailClassificationGraph.ipynb`
Builds an **email classification and response pipeline** using LangGraph and `ChatOpenAI`. Defines an `EmailState` schema to track email metadata, spam status, category, and draft responses. Demonstrates:
- Creating nodes for each processing step: `read_email`, `classify_email`, `handle_spam`, `draft_response`, and `notify_mr_hugg`
- Using an LLM to classify incoming emails as spam or legitimate and categorise them (inquiry, complaint, thank you, etc.)
- Implementing a **conditional edge** (`route_email`) that routes spam to a discard handler and legitimate emails to a draft-response generator
- End-to-end graph: `START` → `read_email` → `classify_email` → spam/legitimate branch → `END`

#### `documentAnalysisGraph.ipynb`
Builds a **ReAct-style document analysis agent** using LangGraph's prebuilt `ToolNode` and `tools_condition`. Defines an `AgentState` with message history and an optional image file path. Demonstrates:
- A multimodal `extract_text` tool that encodes an image as base64 and sends it to `gpt-4o` for OCR-style text extraction
- A simple `divide` tool for arithmetic computations
- Binding tools to a `ChatOpenAI` model with `parallel_tool_calls=False` for sequential reasoning
- An `assistant` node that injects a system prompt (Alfred the butler persona) along with tool descriptions and the current image path
- Building a **ReAct loop**: `START` → `assistant` ↔ `tools` (conditional via `tools_condition`) → `END`
- Invoking the agent with both a pure computation task ("Divide 6790 by 5") and an image-based document analysis task

---

## Notes
- All notebooks use Python 3.13 with an `agents` conda environment.
- Models are served via the Hugging Face Inference API or third-party providers (Together AI, OpenAI).
- This is a living repository that grows as I continue to learn and build agentic workflows!
