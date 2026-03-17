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

### 📁 `LangGraph/`

Experiments using the [LangGraph](https://github.com/langchain-ai/langgraph) framework by LangChain.

#### `buildingBlocks.ipynb`
Introduces the **fundamental building blocks** of LangGraph. Demonstrates:
- Defining graph state with `TypedDict` (a simple `graph_state: str` schema)
- Creating nodes (`node_1`, `node_2`, `node_3`) that read and update the shared state
- Writing a **conditional edge** function (`decide_mood`) that randomly routes execution to one of two branches
- Building and compiling a `StateGraph` with `START` → `node_1` → conditional branch → `END`
- Visualising the compiled graph with Mermaid and invoking it end-to-end

---

## Notes
- All notebooks use Python 3.13 with an `agents` conda environment.
- Models are served via the Hugging Face Inference API or third-party providers (Together AI, OpenAI).
- This is a living repository that grows as I continue to learn and build agentic workflows!
