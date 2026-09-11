<div align="center">

# LangChain ReAct Agent · Smart Customer Service

**An intelligent customer service system built on LangChain + ReAct paradigm + RAG retrieval-augmented generation, demonstrated with a robot vacuum cleaner scenario**

[![Python](https://img.shields.io/badge/Python-3.10+-blue)](https://www.python.org/)
&nbsp;
[![LangChain](https://img.shields.io/badge/LangChain-0.3-green)](https://www.langchain.com/)
&nbsp;
[![LangGraph](https://img.shields.io/badge/LangGraph-0.2-orange)](https://github.com/langchain-ai/langgraph)
&nbsp;
[![Streamlit](https://img.shields.io/badge/Streamlit-1.40-red)](https://streamlit.io/)
&nbsp;
[![License](https://img.shields.io/badge/License-MIT-yellow)](./LICENSE)

</div>

---

## Project Introduction

A **ReAct (Reasoning + Acting) Agent** built on the LangChain framework, integrating RAG retrieval-augmented generation, multi-tool calling, and dynamic prompt switching. Based on user intent, the system automatically determines the task type (knowledge Q&A / report generation), invokes the appropriate tools and knowledge base to complete reasoning, and visualizes the Agent's thinking and execution process in real time through a Streamlit streaming interface.

## Demo Preview

<div align="center">

<img src="assets/chat1.png" alt="Q&A interface" width="85%">

*Figure 1. General Q&A — Reply retrieved from the RAG knowledge base*

&nbsp;

<img src="assets/chat2.png" alt="Tool calling" width="85%">

*Figure 2. Agent tool calling — Real-time view of the reasoning and tool execution chain*

&nbsp;

<img src="assets/chat3.png" alt="Tool call details" width="85%">

*Figure 3. Tool call details — Visualization of multi-step reasoning and intermediate results*

&nbsp;


</div>

## Architecture

<div align="center">

```
User Input (Streamlit)
      │
      ▼
┌─────────────────────────────────────────┐
│            ReAct Agent                   │
│                                          │
│  ┌──────────┐    ┌──────────────────┐   │
│  │ Thought  │───→│     Action       │   │
│  │Reasoning │    │(Tool Call/ RAG)  │   │
│  └──────────┘    └────────┬─────────┘   │
│       ↑                   │              │
│       └─── Observation ◄──┘              │
│                                          │
│Middleware: Tool Monitor · Dynamic Prompt Switching
└─────────────────────────────────────────┘
      │                │              │
      ▼                ▼              ▼
┌──────────┐   ┌────────────┐  ┌──────────┐
│   RAG    │   │   Tools    │  │  Prompt  │
│  Chroma  │   │Weather/User│  │Dynamic   │
│ Vector DB│   │Data/Report │  │Switching/│
│          │   │            │  │Template  │
└──────────┘   └────────────┘  └──────────┘
```

</div>

### Core Features

| Feature | Description |
|---|---|
| **ReAct Paradigm** | Thought → Action → Observation loop. The Agent reasons autonomously and decides which tool to call. |
| **RAG Retrieval-Augmented Generation** | Chroma vector database + DashScope Embedding, MD5 file deduplication, supports mixed txt/pdf loading. |
| **Multi-Tool Calling** | Weather query / user location / external data retrieval / report context filling. The Agent selects tools on demand automatically. |
| **Dynamic Prompt Switching** | Middleware automatically switches between two System Prompts ("General Q&A" and "Report Generation") based on runtime context. |
| **Streaming Chat Interface** | Built with Streamlit, supports streaming word-by-word output, message history retention, and visible Agent reasoning process. |
| **Modular Architecture** | Independent modules for Agent / RAG / Model / Tools / Middleware, driven by YAML configuration. |

## Tech Stack

| Layer | Technology |
|---|---|
| LLM | Tongyi Qianwen (DashScope / ChatTongyi) |
| Agent Framework | LangChain + LangGraph |
| Vector Database | Chroma |
| Document Processing | PyPDF + RecursiveCharacterTextSplitter |
| Frontend | Streamlit |
| Configuration | YAML-driven (Agent / RAG / Chroma / Prompts) |

## Quick Start

### Environment Requirements

- **Python** ≥ 3.10
- **DashScope API Key**（[Alibaba Cloud Bailian](https://bailian.console.aliyun.com/) 申请）

### 1. Clone the Repository

```bash
git clone https://github.com/lhh737/LangChain-ReAct-Agent.git
cd LangChain-ReAct-Agent
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure API Key

Refer to `.env.example`，and set your Alibaba Cloud Bailian API Key:

```bash
# Linux / macOS
export DASHSCOPE_API_KEY="your-api-key"

# Windows (CMD)
set DASHSCOPE_API_KEY=your-api-key
```

> Apply for a key at[Alibaba Cloud Bailian Console](https://bailian.console.aliyun.com/)

### 4. Initialize the Knowledge Base (first run only)

```bash
python -c "from rag.vector_store import VectorStoreService; VectorStoreService().load_document()"
```

### 5. Launch the Application

```bash
streamlit run app.py
```

The browser will open automatically at  http://localhost:8501

### Validation

After startup, enter the following test questions in the chat box:

- *What are the main features of robot vacuum cleaners?* (RAG knowledge base Q&A)
- *What should I do if the robot fails to return to its charging dock?* (Troubleshooting)
- *Please generate a personalized usage report based on the user data* (Report generation + tool calling)

## Project Structure

```
LangChain-ReAct-Agent/
│
├── agent/                          # Agent core
│   ├── react_agent.py              #   ReAct Agent main logic (streaming execution)
│   └── tools/
│       ├── agent_tools.py          #   Tool functions (RAG retrieval / weather / user data / report)
│       └── middleware.py           #   Middleware (tool monitoring / dynamic prompt switching)
│
├── rag/                            # RAG retrieval-augmented generation
│   ├── vector_store.py             #   Chroma vector store · document loading · MD5 deduplication
│   └── rag_service.py              #   RAG retrieval → LLM summarization service
│
├── model/
│   └── factory.py                  # Model factory (ChatTongyi + DashScopeEmbedding)
│
├── config/                         # YAML configuration files
│   ├── agent.yml                   #   Agent behavior and tool configuration
│   ├── chroma.yml                  #   Vector store and retrieval parameters
│   ├── prompts.yml                 #   Prompt templates
│   └── rag.yml                     #   RAG model and parameters
│
├── prompts/                        # Prompt templates
│   ├── main_prompt.txt             #   General Q&A System Prompt
│   ├── rag_summarize.txt           #   RAG summarization Prompt
│   └── report_prompt.txt           #   Report generation System Prompt
│
├── utils/                          # Utility functions
│   ├── config_handler.py           #   YAML configuration loader
│   ├── file_handler.py             #   File parser (PDF/TXT)
│   ├── logger_handler.py           #   Logging manager
│   ├── path_tool.py                #   Path utilities
│   └── prompt_loader.py            #   Prompt loader
│
├── data/                           # Knowledge base documents (robot vacuum cleaner related)
├── assets/                         # Demo screenshots
├── app.py                          # Streamlit application entry point
├── requirements.txt
└── README.md
```

## Configuration

The project manages all configuration through YAML files under the `config/` directory:

| File | Description |
|---|---|
| `rag.yml` | Dialogue model name, Embedding model name |
| `chroma.yml` | Chroma persistence path, chunk size, retrieval Top-K, supported file types |
| `prompts.yml` | File paths of prompt templates for each scenario |
| `agent.yml` | Agent timeout, external data paths, etc. |

For the first run, simply make sure the **DashScope API Key is set** and there are knowledge base documents in the `data/` directory.
