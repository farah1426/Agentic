# Smart Library Assistant — Capstone Project

## Building Agentic AI Systems — SDAIA Academy

**Trainees:** Farah Al-Hamed, Lana Al-Mulhem, Layan Al-Nasser
**Programme:** SDAIA Academy — Building Agentic AI Systems
**Track:** A — Supervisor + workers

## Project Description

An AI librarian agent for an academic library, built to demonstrate all
eight core patterns from the course:

1. **Agent fundamentals** — structured output (`with_structured_output`)
   and real tools that read/write live catalog and account data.
2. **Multi-agent routing** — a supervisor node classifies each request
   with structured output and routes to one of two independent worker
   subagents (`catalog_agent`, `account_agent`).
3. **RAG pipeline** — book notes are loaded, split, embedded, and stored
   in a vector store for semantic search, verified with a
   verbatim-answer sanity check.
4. **Context & state management** — short-term conversation state
   (checkpointer + thread_id) is kept separate from long-term memory
   (a genuine LangGraph `Store`), proven with a real cross-thread test.
5. **Human-in-the-loop** — reserving a rare/special-collections book
   requires librarian approval via a real `interrupt()` /
   `Command(resume=...)` cycle.
6. **Functional API & error handling** — a `@task`/`@entrypoint`
   workflow with a real `RetryPolicy` (Retry strategy) and a Fallback
   strategy, both proven with captured output.
7. **Workflow pattern** — an Evaluator-Optimizer loop critiques and
   refines book recommendations against the user's stated constraints.
8. **LangSmith observability** — full tracing enabled, with a write-up
   of an actual latency bottleneck found in the trace.

## How to Run

1. Open `Building_Agentic.ipynb` in Google Colab.
2. Add your API keys to Colab Secrets (🔑 icon):
   - `OPENROUTER_API_KEY` — from https://openrouter.ai/keys
   - `LANGSMITH_API_KEY` — from https://smith.langchain.com (Settings → API Keys)
3. Runtime → Run all, top to bottom.

## Project Structure

- `Building_Agentic.ipynb` — the full capstone notebook, all 8 sections
- `.gitignore` — excludes secrets and generated files
- `README.md` — this file
