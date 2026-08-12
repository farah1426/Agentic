# Smart Library Assistant — Capstone Project

## Building Agentic AI Systems — SDAIA Academy

**Trainees:** Farah Al-Hamed, Lana Al-Mulhem, Layan Al-Nasser
**Programme:** SDAIA Academy — Building Agentic AI Systems
**Cohort:** August 9–13, 2026
**Track:** A — Supervisor + workers
**SDAIA Academy GitHub:** https://github.com/SDAIAAcademy

## Project Description

An AI librarian agent for an academic library, built to demonstrate all
eight core patterns from the course. The agent can recommend books by
meaning (not just keyword search), manage patron accounts and loans,
escalate rare-item reservations to a human librarian for approval, and
remember a patron's reading preferences across separate conversations.

## Rubric Sections Implemented

1. **Agent fundamentals** — structured output (`with_structured_output`
   + Pydantic) and real tools that read/write live catalog and account
   data — no hardcoded responses.
2. **Multi-agent routing (Track A: Supervisor + workers)** — a
   `supervisor_node` classifies each request with structured output
   (`RouteDecision`) and routes to one of two independent worker
   subagents (`catalog_agent`, `account_agent`) that don't know about
   each other.
3. **RAG pipeline** — book notes are loaded, split
   (`RecursiveCharacterTextSplitter`), embedded (HuggingFace), and
   stored in an `InMemoryVectorStore` for semantic search, verified with
   a verbatim-answer sanity check.
4. **Context & state management** — short-term conversation state
   (`InMemorySaver` + `thread_id`) is kept fully separate from long-term
   memory (`InMemoryStore`), proven with a real cross-thread test:
   a preference written in Thread A is correctly recalled from a
   brand-new Thread B.
5. **Human-in-the-loop** — reserving a rare/special-collections book
   requires librarian approval via a real `interrupt()` /
   `Command(resume=...)` cycle; both the pause and the resume are
   demonstrated with captured output.
6. **Functional API & error handling** — a `@task`/`@entrypoint`
   workflow implementing two error strategies with real proof: a
   `RetryPolicy` that retries past two simulated transient failures,
   and a Fallback path that activates when a primary task is designed
   to fail.
7. **Workflow pattern: Evaluator-Optimizer** — a generate → evaluate →
   refine loop that critiques book recommendations against the user's
   stated constraints (e.g. "no heavy math") and retries when the
   critique fails.
8. **LangSmith observability** — full tracing verified against a live
   LangSmith project (`smart-library-assistant-capstone`), with a
   write-up describing an actual latency bottleneck found in the trace
   (two sequential LLM calls, not retrieval, dominated run time).

## Technical Documentation

The project is a single Colab notebook (`Building_Agentic.ipynb`)
implementing 8 core agentic AI patterns in dependency order:

- **Data layer:** `BOOKS` and `USERS` — in-memory Python dicts
  simulating a library catalog (6 books, including 1 rare
  special-collections item) and 3 patron accounts with different
  subscription states.
- **RAG layer:** `vector_store` — an `InMemoryVectorStore` over
  HuggingFace `sentence-transformers/all-mpnet-base-v2` embeddings of
  each book's long-form notes, exposed via the `semantic_search()`
  helper.
- **Agent layer:** `catalog_agent`, `account_agent`, `memory_agent`,
  `hitl_agent` — four `create_agent` instances, each scoped to a
  distinct subset of tools.
- **Orchestration layer:** `routing_graph` — a `StateGraph` with a
  structured-output supervisor node routing between `catalog_agent`
  and `account_agent` via conditional edges.
- **Reliability layer:** `process_borrow_request` and
  `recommend_with_evaluation` — `@task`/`@entrypoint` Functional API
  workflows demonstrating Retry, Fallback, and the Evaluator-Optimizer
  workflow pattern.
- **Observability layer:** LangSmith tracing via `LangChainTracer`,
  verified against a live project.

## How to Run

1. Open `Building_Agentic.ipynb` in Google Colab.
2. Add your API keys to Colab Secrets (🔑 icon in the left sidebar):
   - `OPENROUTER_API_KEY` — from https://openrouter.ai/keys
   - `LANGSMITH_API_KEY` — from https://smith.langchain.com
     (Settings → API Keys, Personal Key)
3. Runtime → Restart and run all, top to bottom.
4. All demo cells print their own PASS/FAIL confirmation — check for
   `PASS` in the output of Sections 3, 4, 5, and 6.

## Notes & Honest Gaps

- Section 6's error-handling write-up implements the two most commonly
  taught strategies (Retry, Fallback) since the "Reliability" lesson
  content specifying the exact four strategies wasn't directly
  available while building this — see the note in Section 6 of the
  notebook.
- Section 7's evaluator occasionally produced internally inconsistent
  structured-output scores (a `score=1/5` paired with
  `meets_constraints=True`), most likely a limitation of the free-tier
  model used. The loop mechanism itself worked correctly regardless —
  documented honestly in the notebook rather than hidden.
