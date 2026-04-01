<div align="center">

<img src="ejentum_banner.png" width="100%" />

[![Website](https://img.shields.io/badge/ejentum.com-0B1121?style=for-the-badge&logo=googlechrome&logoColor=ff4d6a)](https://ejentum.com)
[![Docs](https://img.shields.io/badge/Documentation-0B1121?style=for-the-badge&logo=readthedocs&logoColor=10b981)](https://ejentum.com/docs)
[![Benchmarks](https://img.shields.io/badge/Benchmarks-0B1121?style=for-the-badge&logo=github&logoColor=f59e0b)](https://github.com/ejentum/benchmarks)
[![Examples](https://img.shields.io/badge/Examples-0B1121?style=for-the-badge&logo=github&logoColor=a78bfa)](https://github.com/ejentum/examples)

</div>

---

**RA²R: Reasoning Ability-Augmented Retrieval.** Where RAG retrieves information, Ejentum retrieves reasoning. 311 engineered cognitive operations across six dimensions, optimized for agentic inference. One API call.

---

## The Logic API

Your agent sends a task description. The API returns a structured reasoning scaffold: a negative gate (the failure pattern to avoid), suppression signals (cognitive shortcuts to block), a reasoning topology (the execution structure to follow), and a falsification test (the verification criterion).

The scaffold is injected into the agent's context before generation. The model's next output reflects a different reasoning structure. No retraining. No fine-tuning. No prompt rewrite.

```bash
curl -X POST "https://ejentum-main-ab125c3.zuplo.app/logicv1/" \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "Why did deployment fail after the config change?", "mode": "single"}'
```

**Two modes:**

| Mode | What it returns | Tokens | Best for |
|------|----------------|--------|----------|
| **Ki** | One engineered cognitive operation | ~500 | Focused, single-domain tasks |
| **Haki** | Four synergized operations with compound suppression | ~900 | Complex, cross-domain analysis |

Retrieval under 1 second. No LLM inference on our side. Deterministic.

---

## Measured Results

Three independent benchmarks. Blind protocol. Negative findings included.

### EjBench (180 custom professional tasks)

| Factor | Baseline | With Scaffold | Change |
|--------|----------|--------------|--------|
| Composite | 0.621 | 0.722 | **+10.1pp** |
| Self-Monitoring | 0.94/3.0 | 1.81/3.0 | **+92%** |
| Verification | 1.50/3.0 | 2.16/3.0 | **+45%** |
| Correctness | 2.60/3.0 | 2.49/3.0 | -0.11 (reported) |

### BBH / CausalBench / MuSR (70 published academic tasks)

| Factor | Baseline | With Scaffold | Change |
|--------|----------|--------------|--------|
| Composite | 0.476 | 0.684 | **+20.8pp** |
| Self-Monitoring | 0.74/3.0 | 1.73/3.0 | **+134%** |
| Correctness | 2.19/3.0 | 2.33/3.0 | **+6%** |

### ARC-AGI-3 (interactive reasoning, 25 steps per condition)

Both conditions scored RHAE 0.0 (neither cleared Level 0). The evidence is in the reasoning process:

| Metric | Baseline | Augmented |
|--------|----------|-----------|
| Memory decay slope | -0.005 (degrading) | +0.014 (improving) |
| Scaffold half-life | 0 | 24 steps |
| Reasoning depth trend | 0.86 | 10.50 (12.2x) |

Full reports, raw traces, generation outputs, and judgment scores: **[ejentum/benchmarks](https://github.com/ejentum/benchmarks)**

---

## Six Reasoning Dimensions

| Dimension | Abilities | What it addresses |
|-----------|----------|-------------------|
| **Causality** | 52 | Direction-of-causation errors, correlation treated as cause |
| **Temporal** | 51 | Sequence errors, confabulated timelines, duration bias |
| **Spatial** | 51 | Topology failures, boundary violations, structural constraints |
| **Simulation** | 52 | Counterfactual collapse, failure to model consequences |
| **Abstraction** | 51 | Category errors, over-generalization, metaphor/mechanism confusion |
| **Metacognition** | 54 | Hallucination spirals, reasoning drift, bias blindness |

Browse all 311 abilities: [ejentum.com/abilities](https://ejentum.com/abilities)

---

## Integrate

The Logic API works with any system that can make an HTTP POST and inject text into a prompt.

| Environment | Example |
|-------------|---------|
| **Python** | [`basic.py`](https://github.com/ejentum/examples/blob/main/python/basic.py) |
| **LangChain / LangGraph** | [`langchain_tool.py`](https://github.com/ejentum/examples/blob/main/python/langchain_tool.py) |
| **OpenAI Agents SDK** | [`openai_agents_tool.py`](https://github.com/ejentum/examples/blob/main/python/openai_agents_tool.py) |
| **Claude Agent SDK** | [`claude_agent_sdk.py`](https://github.com/ejentum/examples/blob/main/python/claude_agent_sdk.py) |
| **MCP Server** (Cursor, Claude Code, Windsurf, Continue, Cline) | [`ejentum_server.py`](https://github.com/ejentum/examples/blob/main/mcp/ejentum_server.py) |
| **n8n** | [`ejentum_workflow.json`](https://github.com/ejentum/examples/blob/main/n8n/ejentum_workflow.json) |
| **TypeScript** | [`basic.ts`](https://github.com/ejentum/examples/blob/main/typescript/basic.ts) |
| **curl** | [`single.sh`](https://github.com/ejentum/examples/blob/main/curl/single.sh) |

All examples: **[ejentum/examples](https://github.com/ejentum/examples)**

---

## Pricing

| | Free | Ki | Haki | Enterprise |
|---|---|---|---|---|
| **Price** | Free | €19/mo | €49/mo | Custom |
| **Calls** | 100 total | 10,000/mo | 50,000/mo | Custom |
| **Mode** | Ki | Ki | Ki + Haki | All |
| **Rate** | 100/min | 100/min | 100/min | Custom |

No card required for free tier. [Get your API key](https://ejentum.com/dashboard).

---

## Limitations

- **One model family tested.** All benchmarks are on Claude (Opus 4.6 for single-turn, Sonnet 4.6 for ARC-AGI-3). Cross-model testing is planned, not completed.
- **LLM-as-judge.** Two-stage blind protocol: generation and evaluation are separate. The evaluator never saw which condition produced which output. Human evaluation would provide additional validation.
- **Suppression is not absolute.** LLMs are probabilistic. Suppression reduces failure rates; it does not eliminate them.
- **Retrieval precision depends on query quality.** Send the full task description, not a summary.

---

## Repositories

| Repo | What's inside |
|------|--------------|
| **[benchmarks](https://github.com/ejentum/benchmarks)** | 2,161 files. Reports, raw traces, generation outputs, judgment scores, system prompts. |
| **[docs](https://github.com/ejentum/docs)** | 15 documentation pages. Quickstart, API reference, integrations, method, benchmarks. |
| **[examples](https://github.com/ejentum/examples)** | 13 runnable integration examples. Python, TypeScript, curl, MCP, n8n, skill files. |

---

<div align="center">

Ejentum. Reasoning infrastructure for agentic AI.

[ejentum.com](https://ejentum.com) · [docs](https://ejentum.com/docs) · [blog](https://ejentum.com/blog) · [info@ejentum.com](mailto:info@ejentum.com)

</div>
