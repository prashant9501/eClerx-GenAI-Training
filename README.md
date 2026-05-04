# eClerx — AI / GenAI Engineer Training

A 10-day, 39-lab corporate training program preparing CDAC graduates at **eClerx** for deployment as AI / GenAI Engineers at financial-services clients. The program covers prompt engineering, LangChain, LangGraph, RAG, agentic systems, AWS Bedrock, observability, and production deployment.

Each module ships as a folder at the repo root containing:

- **Self-contained Jupyter notebooks** (~1.5 hours of trainee time each) with a preserved business scenario, numbered task sections, a final **Conclusion & Key Takeaways** cell, a **Stretch Exercise (Optional)** section, and — for technically-dense labs — an **Interview Preparation** section.
- **Reveal.js HTML decks** that introduce the concepts trainees need before the labs (no install required — open the `.html` file in a browser).

---

## Repository structure

```
eClerx-GenAI-Training/
├── Module 1 - Python for GenAI & LLM Foundations/    ← Day 1 (4 labs · 2 decks) ✅
│   ├── Lab_1.1_Hello_LLM_Client.ipynb
│   ├── Lab_1.2_Robustness_Patterns.ipynb
│   ├── Lab_1.3_Structured_Outputs_Multi_Provider.ipynb
│   ├── Lab_1.4_Transformer_Architecture_Tokenization.ipynb
│   ├── Module_1_Foundations_Labs_1-3.html             ← deck for Labs 1.1–1.3
│   └── Module_1_Part2_LLM_Internals_Lab_1-4.html      ← deck for Lab 1.4
├── Module 2 - Prompt Engineering with LangChain/      ← Day 2 (5 labs · 1 deck) ✅
│   ├── Lab_2.1_Crafting_Precise_Prompts.ipynb
│   ├── Lab_2.2_Foundational_Prompt_Patterns.ipynb
│   ├── Lab_2.3_Advanced_Prompting_CoT_ToT_CoVe.ipynb
│   ├── Lab_2.4_Output_Parsers_DeepDive.ipynb
│   ├── Lab_2.5_Prompt_Caching.ipynb
│   └── Module_2_Prompt_and_Context_Engineering.html
├── Module 3 - LangChain Deep Dive/                       ← Day 3 (2 labs · 1 deck) ✅
│   ├── Lab_3.1_LangChain_Fundamentals.ipynb
│   ├── Lab_3.2_Tool_Calling_Agents.ipynb
│   └── Module_3_LangChain_Deep_Dive.html
├── Module 4 - RAG Foundations/                           ← Day 4 (4 labs · 1 deck) ✅
│   ├── Lab_4.1_Document_Loaders_Chunking.ipynb
│   ├── Lab_4.2_Embeddings_Vector_Stores.ipynb
│   ├── Lab_4.3_RAG_LCEL_Pipeline.ipynb
│   ├── Lab_4.4_Bedrock_Knowledge_Bases.ipynb
│   └── Module_4_RAG_Foundations.html
├── Module 7 - Agentic AI with LangGraph/                 ← Day 7 (4 labs · 1 deck) ✅
│   ├── Lab_7.1_LangGraph_Routing.ipynb
│   ├── Lab_7.2_Parallelization_SendAPI.ipynb
│   ├── Lab_7.3_HITL_Checkpointing_Loan_Approval.ipynb
│   ├── Lab_7.4_Financial_Analyst_Agent.ipynb
│   ├── Module_7_LangGraph_Agentic_AI.html
│   └── policy_documents.zip                              ← Lab 7.4 runtime data (5 PDFs + meridian_wealth.db)
├── Module 8 - Multi-Agent Systems/                       ← Day 8 (5 labs · 1 deck · synthetic data) ✅
│   ├── Lab_8.1_LangGraph_Supervisor.ipynb
│   ├── Lab_8.2_Compliance_Report_Generator.ipynb
│   ├── Lab_8.3_Microsoft_Agent_Framework.ipynb
│   ├── Lab_8.4_CrewAI_Fintech.ipynb
│   ├── Lab_8.5_OpenAI_Agents_SDK_Banking.ipynb
│   ├── Module_8_Multi_Agent_Systems.html
│   ├── customers.csv, customers.json, orders.json,       ← Lab 8.1 ShopSmart data
│   │   products.json, tickets.json, policies.md
│   └── transactions.csv, sar_filings.csv,                ← Lab 8.2 compliance data
│       prior_findings.csv, regulatory_thresholds.json,
│       fincen_template.md, occ_template.md, state_template.md
├── …
└── Module 10 - Production Deployment & Capstone/         (coming soon — Day 10, 3 labs)
```

| # | Module | Day | Labs | Status |
|---|---|---|---|---|
| 1 | Python for GenAI & LLM Foundations | 1 | 4 | ✅ Available |
| 2 | Prompt Engineering with LangChain | 2 | 5 | ✅ Available |
| 3 | LangChain Deep Dive | 3 | 2 | ✅ Available |
| 4 | RAG Foundations | 4 | 4 | ✅ Available |
| 5 | Advanced RAG & Evaluation | 5 | 4 | Coming soon |
| 6 | Claude API & AWS Bedrock | 6 | 4 | Coming soon |
| 7 | Agentic AI with LangGraph | 7 | 4 | ✅ Available |
| 8 | Multi-Agent Systems | 8 | 5 | ✅ Available |
| 9 | Observability, Evaluation & Responsible AI | 9 | 4 | Coming soon |
| 10 | Production Deployment & Capstone | 10 | 3 | Coming soon |

---

## Module 1 — Python for GenAI & LLM Foundations

| Lab | Topic | Type |
|---|---|---|
| 1.1 | Hello-LLM Client — multi-provider API patterns (OpenAI, Anthropic, Gemini, Groq, AWS Bedrock) | Applied |
| 1.2 | Robustness — retries, exponential backoff, fallback providers | Applied |
| 1.3 | Structured outputs with Pydantic + multi-provider comparison | Applied |
| 1.4 | Transformer architecture, tokenization, attention | Dense (with interview prep) |

**Decks:** `Module_1_Foundations_Labs_1-3.html` (GenAI basics → LLM evolution → image generation → prompt engineering → RAG → fine-tuning → multi-provider patterns) and `Module_1_Part2_LLM_Internals_Lab_1-4.html` (transformer · architecture families · tokenization · sampling · context window · API pricing).

---

## Module 2 — Prompt Engineering with LangChain

| Lab | Topic | Type |
|---|---|---|
| 2.1 | Crafting Precise Prompts with `ChatPromptTemplate` | Applied |
| 2.2 | Foundational patterns — Persona · N-Shot · Directional Stimulus · Template & Meta-Language + `FewShotChatMessagePromptTemplate` at scale | Applied |
| 2.3 | Advanced reasoning — Chain-of-Thought · Tree-of-Thoughts · Chain-of-Verification | Dense (with interview prep) |
| 2.4 | LangChain output parsers — `PydanticOutputParser`, `with_structured_output`, XML tagging, `OutputFixingParser` | Dense (with interview prep) |
| 2.5 | Prompt caching across providers — Anthropic `cache_control`, OpenAI automatic, Gemini explicit cache API | Dense (with interview prep) |

**Deck:** `Module_2_Prompt_and_Context_Engineering.html` covers all 5 labs plus the broader concepts of **context engineering** (vs prompt engineering) and **context management techniques** (truncation, running summary, hierarchical summary, sliding window, memory tiers, lost-in-the-middle).

---

## Module 3 — LangChain Deep Dive

| Lab | Topic | Type |
|---|---|---|
| 3.1 | LangChain Fundamentals — chains, **modern memory** (`RunnableWithMessageHistory`), LCEL composition, `.astream_events()` for streaming UIs | Dense (with interview prep) |
| 3.2 | Tool-calling agents with **LangChain v1 `create_agent`** — built-in tools (Tavily), custom `@tool` and `StructuredTool` patterns, real-world weather API, error handling | Dense (with interview prep) |

Lab 3.1 replaces the deprecated `ConversationBufferMemory` family with the LangChain v1 `RunnableWithMessageHistory` + `InMemoryChatMessageHistory` pattern, and introduces `.astream_events()` for streaming UIs (used later in Module 10's FastAPI deployment). Lab 3.2 replaces the legacy `AgentExecutor` orchestration with the v1 `create_agent` Runnable, and previews the LangGraph `StateGraph` re-implementation that comes in Module 7.

---

## Module 4 — RAG Foundations

| Lab | Topic | Type |
|---|---|---|
| 4.1 | Document loaders & chunking — PDF (PyPDF, PyMuPDF, Unstructured), CSV, JSON, Markdown, recursive vs semantic vs section-aware splitting, tiktoken-based chunking | Dense (with interview prep) |
| 4.2 | Embeddings & vector stores — OpenAI `text-embedding-3-small` vs Bedrock Titan v2, FAISS in-memory vs ChromaDB persistent, metadata filtering, indexing strategies | Dense (with interview prep) |
| 4.3 | End-to-end LCEL RAG pipeline with FAISS / ChromaDB + source citations (page numbers, document IDs); forward-reference to Lab 3.1's `RunnableWithMessageHistory` for conversational RAG | Applied |
| 4.4 | Amazon Bedrock Knowledge Bases — managed-RAG pipeline, `retrieve` vs `retrieve_and_generate`, when to choose managed vs self-hosted | Dense (with interview prep) |

**Deck:** `Module_4_RAG_Foundations.html` covers why RAG, document loaders & chunking trade-offs, embeddings & vector geometry, vector-store selection (FAISS/Chroma/OpenSearch), the end-to-end LCEL RAG flow, and the managed-vs-self-hosted Bedrock KB decision.

---

## Module 7 — Agentic AI with LangGraph

| Lab | Topic | Type |
|---|---|---|
| 7.1 | LangGraph routing — StateGraph mechanics, TypedDict state, conditional edges, reducers (`add_messages`); customer-support router on the ShopSmart scenario | Dense (with interview prep) |
| 7.2 | Parallelisation with the **Send API** — fan-out / fan-in over dynamic worker counts, latency analysis vs sequential, SecureBank multi-account analysis | Dense (with interview prep) |
| 7.3 | Human-in-the-loop & checkpointing — `interrupt_before`/`interrupt_after`, `MemorySaver` vs `SqliteSaver` vs `PostgresSaver`, time-travel replay; loan approval workflow | Dense (with interview prep) |
| 7.4 | Composing it all — Financial Analyst Agent integrating ReAct + RAG (5 wealth-management policy PDFs in `policy_documents.zip`) + SQL on `meridian_wealth.db` + web search | Applied |

**Deck:** `Module_7_LangGraph_Agentic_AI.html` covers when to graduate from `create_agent` to a `StateGraph`, the StateGraph topology (nodes / conditional edges / reducers), routing patterns, the Send API for dynamic parallelism, HITL with checkpointers across persistence tiers, and time-travel debugging.

**Runtime data — `policy_documents.zip`:** Lab 7.4's first cell unpacks this archive into the working directory. It contains 5 wealth-management policy PDFs (Asset Allocation, Client Suitability, Quarterly Reporting, Rebalancing Protocol, Risk Management) and a small `meridian_wealth.db` SQLite database the SQL agent queries. All synthetic — Meridian Wealth is fictional.

---

## Module 8 — Multi-Agent Systems

| Lab | Topic | Type |
|---|---|---|
| 8.1 | LangGraph **supervisor pattern** — `Command(goto=...)` handoffs, shared state, specialist routing; ShopSmart customer-support multi-agent | Dense (with interview prep) |
| 8.2 | **Orchestrator-Worker + Evaluator-Optimizer** with HITL — Send API for dynamic worker spawning, evaluator-as-LLM revision loops; SecureBank compliance report generator | Dense (with interview prep) |
| 8.3 | **Microsoft Agent Framework** (`autogen-agentchat ≥ 0.4`) — `AssistantAgent`, `RoundRobinGroupChat`, `SelectorGroupChat`, `Handoff`; AutoGen 0.2 → MAF lineage; banking use cases | Dense (with interview prep) |
| 8.4 | **CrewAI** — role/goal/backstory model, sequential vs hierarchical processes, fintech content pipeline | Applied |
| 8.5 | **OpenAI Agents SDK** — handoffs as functions, built-in input/output guardrails, tracing; SecureBank India banking concierge | Applied |

**Deck:** `Module_8_Multi_Agent_Systems.html` covers the four multi-agent frameworks side-by-side (LangGraph supervisor, MAF, CrewAI, OpenAI Agents SDK) plus the orchestrator-worker and evaluator-optimizer patterns, ending with a framework-selection decision matrix.

**Runtime data — synthetic banking + e-commerce assets (~4 MB total):** Lab 8.1 reads `customers.csv/json`, `orders.json`, `products.json`, `tickets.json`, `policies.md` for the ShopSmart support agent; Lab 8.2 reads `transactions.csv` (8K+ synthetic transactions), `sar_filings.csv`, `prior_findings.csv`, `regulatory_thresholds.json`, plus `fincen_template.md`, `occ_template.md`, `state_template.md` for the regulatory report generator. All entirely synthetic — patterned IDs (`CUST-000001`, `TXN-00000001`, `SAR-0001`), generic names, no real PII.

---

## Running the labs

Notebooks are written to run in a **local Python virtual environment** — they are not Colab-pinned.

### 1. Set up a venv

```bash
python -m venv .venv
# Windows (PowerShell)
.venv\Scripts\Activate.ps1
# Windows (Git Bash) / macOS / Linux
source .venv/bin/activate
```

### 2. Install dependencies

Each notebook lists its required packages in a commented `pip install` cell at the top. Install once per venv. Suggested baseline that covers Modules 1–2:

```bash
pip install \
    "langchain>=1.0" "langchain-core>=1.0" "langchain-aws>=0.2" \
    "langchain-anthropic>=0.3" "langchain-openai>=1.0" \
    openai anthropic google-genai groq boto3 \
    transformers torch sentencepiece protobuf tiktoken \
    matplotlib seaborn pandas pydantic python-dotenv
```

### 3. Configure API keys

Create a `.env` file at the repo root (it is git-ignored). Populate the keys you have access to:

```
OPENAI_API_KEY=sk-…
ANTHROPIC_API_KEY=sk-ant-…
GEMINI_API_KEY=…
GROQ_API_KEY=gsk_…
AWS_ACCESS_KEY_ID=…
AWS_SECRET_ACCESS_KEY=…
AWS_DEFAULT_REGION=us-east-1
```

Each notebook's API-key cell calls `dotenv.load_dotenv()` if `python-dotenv` is installed; otherwise it relies on shell-exported environment variables.

### 4. Launch Jupyter

```bash
jupyter lab
# or
jupyter notebook
```

Open the lab file from inside the relevant module folder.

### 5. Open a deck

Reveal.js decks are self-contained HTML files that load their dependencies from CDN. Just double-click the `.html` file (or open it in your browser). Use ←/→ keys to navigate, `f` for fullscreen, append `?print-pdf` to the URL for PDF export.

---

## Conventions used across all labs

- **Code style.** Functional Python first; classes only for Pydantic models, framework subclasses, and TypedDict states. Type hints on every function. f-strings only.
- **Models.** Claude Sonnet 4.5 / Haiku 4.5 as primary; comparison labs also use `gpt-5-mini`, `gemini-2.5-flash`, `llama-3.3-70b-versatile`. Embeddings via `text-embedding-3-small` (OpenAI) or `amazon.titan-embed-text-v2:0` (Bedrock).
- **LangChain v1.** All notebooks pin `langchain>=1.0`, `langchain-core>=1.0`, `langgraph>=1.0`, `langchain-aws>=0.2`, `langchain-anthropic>=0.3`. No deprecated `ConversationBufferMemory`, `langchain.text_splitter`, legacy `AgentExecutor`, or `pyautogen`.
- **No hardcoded secrets.** API keys are read from environment / `.env` only.
- **Scenarios preserved.** Each lab keeps its source notebook's business scenario (banking, e-commerce, healthcare, generic) — no domain re-skinning.

---

## License

For internal use within the eClerx training program. Not for external redistribution.
