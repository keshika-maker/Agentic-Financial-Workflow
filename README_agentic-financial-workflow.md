# Agentic Financial Data Workflow

A LangGraph state machine that turns raw financial metrics into validated, narrated
reports. Multi-tool agent with numeric cross-checks that eliminate transcription
errors. **Clean-room build** using mock data — no client systems.

> Reporting cycle pattern: from hours of manual assembly to a single orchestrated,
> self-validating pipeline.

## What it does

1. Pulls metrics from a **mock data source** (stands in for a BI REST API)
2. Runs an agent that selects tools: query → validate → narrate
3. Cross-checks every number against source before writing prose
4. Emits a structured report + a plain-English narrative

## Architecture

```
        ┌─────────────────────────────────────────┐
        │            LangGraph State Machine        │
        │                                           │
  START ─►  fetch_metrics ─► validate ─► narrate ─► END
        │        │             │           │        │
        │        ▼             ▼           ▼        │
        │   mock BI tool   Pydantic     LLM tool    │
        │                  + numeric                 │
        │                  cross-check               │
        └─────────────────────────────────────────┘
```

If validation fails, the graph loops back to `fetch_metrics` instead of producing a
confidently-wrong report.

## Tech stack

- **LangGraph** — stateful agent orchestration
- **LangChain** — multi-tool agent
- **Pydantic** — schema + numeric validation
- **OpenAI / Claude** — narrative generation (configurable)
- **Mock data layer** — JSON fixtures simulating a BI REST API

## Quickstart

```bash
git clone https://github.com/<you>/agentic-financial-workflow.git
cd agentic-financial-workflow
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
python run.py --period 2024-Q4
```

Output: a validated metrics table + a generated narrative in `out/`.

## Why the validation matters

LLMs hallucinate numbers. This pipeline **never lets the model invent figures** — the
narrate step can only reference values that passed the Pydantic + cross-check gate.
That design choice is the whole point: trustworthy automated reporting.

## Repo structure

```
graph/          LangGraph nodes + edges
tools/          fetch, validate, narrate tools
data/           mock financial fixtures
run.py          entrypoint
.env.example
```

## Roadmap

- [ ] Swap mock layer for a live BI REST connector
- [ ] Add a Streamlit front-end
- [ ] Trace each node's latency/cost

---

*Reference implementation. All data is synthetic.*
