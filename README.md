# eClerx — AI / GenAI Engineer Training

A 10-day, 40-lab corporate training program preparing CDAC graduates at **eClerx** for deployment as AI / GenAI Engineers at financial-services clients. The program covers prompt engineering, LangChain, LangGraph, RAG, agentic systems, AWS Bedrock, observability, and production deployment.

Each lab is a self-contained Jupyter notebook (~1.5 hours of trainee time) with a preserved business scenario, numbered task sections, a final **Conclusion & Key Takeaways** cell, a **Stretch Exercise (Optional)** section, and — for technically-dense labs — an **Interview Preparation** section with 6–8 questions and answer sketches.

---

## Repository structure

Modules ship as folders at the repository root. New modules are added as the program builds out.

```
eClerx-GenAI-Training/
├── Module 1 - Python for GenAI & LLM Foundations/    ← Day 1 (4 labs)
│   ├── Lab_1.1_Hello_LLM_Client.ipynb
│   ├── Lab_1.2_Robustness_Patterns.ipynb
│   ├── Lab_1.3_Structured_Outputs_Multi_Provider.ipynb
│   └── Lab_1.4_Transformer_Architecture_Tokenization.ipynb
├── Module 2 - Prompt Engineering with LangChain/      (coming soon — Day 2, 5 labs)
├── Module 3 - LangChain Deep Dive/                    (coming soon — Day 3, 3 labs)
├── …
└── Module 10 - Production Deployment & Capstone/      (coming soon — Day 10, 3 labs)
```

| # | Module | Day | Labs |
|---|---|---|---|
| 1 | Python for GenAI & LLM Foundations | 1 | 4 |
| 2 | Prompt Engineering with LangChain | 2 | 5 |
| 3 | LangChain Deep Dive | 3 | 3 |
| 4 | RAG Foundations | 4 | 4 |
| 5 | Advanced RAG & Evaluation | 5 | 4 |
| 6 | Claude API & AWS Bedrock | 6 | 4 |
| 7 | Agentic AI with LangGraph | 7 | 4 |
| 8 | Multi-Agent Systems | 8 | 5 |
| 9 | Observability, Evaluation & Responsible AI | 9 | 4 |
| 10 | Production Deployment & Capstone | 10 | 3 |

---

## Module 1 — Python for GenAI & LLM Foundations (available now)

| Lab | Topic | Type |
|---|---|---|
| 1.1 | Hello-LLM Client — multi-provider API patterns (OpenAI, Anthropic, Gemini, Groq, AWS Bedrock) | Applied |
| 1.2 | Robustness — retries, exponential backoff, fallback providers | Applied |
| 1.3 | Structured outputs with Pydantic + multi-provider comparison | Applied |
| 1.4 | Transformer architecture, tokenization, attention | Dense (with interview prep) |

---

## Running the labs

The notebooks are written to run in a **local Python virtual environment**. They are not Colab-pinned.

### 1. Set up a venv

```bash
python -m venv .venv
# Windows (PowerShell)
.venv\Scripts\Activate.ps1
# Windows (Git Bash) / macOS / Linux
source .venv/bin/activate
```

### 2. Install dependencies

Each notebook lists its required packages in a commented `pip install` cell at the top. Install once per venv — typical Module 1 dependencies:

```bash
pip install \
    "langchain>=1.0" "langchain-core>=1.0" "langchain-aws>=0.2" \
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
