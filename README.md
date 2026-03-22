<div align="center">

<img src="ejentum_banner.png" width="100%" />

[![Website](https://img.shields.io/badge/ejentum.com-0B1121?style=for-the-badge&logo=googlechrome&logoColor=3b82f6)](https://ejentum.com)
[![Docs](https://img.shields.io/badge/Documentation-0B1121?style=for-the-badge&logo=readthedocs&logoColor=10b981)](https://ejentum.com)
[![API](https://img.shields.io/badge/Logic_API_v1-0B1121?style=for-the-badge&logo=lightning&logoColor=f59e0b)](https://ejentum.com)

</div>

---

## The Problem

LLM failures in production are not information problems — they are **structural reasoning problems.** Models reverse causality, confabulate timelines, collapse categories, and hallucinate without self-awareness. These failures persist regardless of how much context you retrieve.

RAG solves the information gap. Nothing solves the reasoning gap. That's what we built.

## What is RA²R?

**Reasoning Ability-Augmented Retrieval** retrieves cognitive operations — not information — and injects them into an LLM's context at inference time.

| | RAG | RA²R |
|---|---|---|
| **Retrieves** | Documents, facts, context | Reasoning procedures, suppression signals, cognitive scaffolds |
| **Augments** | What the model *knows* | How the model *thinks* |
| **Payload** | Variable (chunks) | ~200 tokens (structured protocol) |
| **Integration** | Embedding + vector DB for your data | One API call — we handle retrieval |

```bash
curl -X POST "https://ejentum-main-ab125c3.zuplo.app/logicv1/" \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "your task description", "mode": "single"}'
```

---

## What Changes in Practice

**Query:** *"Why did our conversion rate drop 40% after the checkout redesign?"*

**Without injection:**
> *"The conversion rate likely dropped due to several factors including user interface changes, loading speed issues, and potential bugs in the new checkout flow. I recommend A/B testing the old and new designs to identify the specific cause."*

Lists plausible guesses. No causal mechanism. No falsifiable claim.

**With RA²R injection:**
> *"Isolating root cause. The checkout redesign introduced mandatory account creation — the most significant change in the deployment diff. Prior analytics show guest checkout accounted for the majority of conversions. Mechanism: mandatory account creation added a friction barrier at the conversion-critical step. I need to verify: does the percentage of users who previously used guest checkout correlate with the 40% drop? If yes, this is the primary cause. If not, I should look at secondary factors. I am suppressing the impulse to list multiple surface-level factors and instead tracing one causal chain to its root."*

Traces a single causal chain. Identifies a mechanism. States what would falsify it. Explicitly suppresses the tendency to list surface-level factors.

---

## How It Works

Each of 311 abilities is a **structured cognitive protocol** — not a prompt template.

Every injection contains: a procedural scaffold the model follows step-by-step, suppression directives that block named failure modes, an execution graph that encodes reasoning structure, and a falsification test to verify the ability was actually applied.

**The core mechanism:** Each ability persists in the context window as a structurally distinctive token sequence. The transformer's attention mechanism continues to reference it across subsequent reasoning steps — functioning as a **persistent cognitive scaffold** that prevents reasoning degradation over extended chains. Multiple abilities create compound scaffolds where suppression signals stack and gate conditions cross-reference.

**Why suppression matters more than amplification:** Telling a model *"find the root cause"* increases the probability of good output. Telling it *"reject any output that exhibits `symptom_treatment_bias`"* eliminates an entire class of failure. One adds signal; the other removes noise. In our testing, the gap is consistent.

---

## 311 Abilities Across 6 Reasoning Dimensions

Every LLM reasoning failure maps to one of six dimensions. Each dimension targets a specific structural failure mode:

| Dimension | Count | Structural Failure |
|---|---|---|
| **Causality** | 52 | Reverses causal direction, treats correlation as causation, stops at surface-level explanations |
| **Temporal** | 51 | Confabulates timelines, reverses event sequences, leaks future data into past analysis |
| **Spatial** | 51 | Violates physical constraints, ignores topology, produces impossible configurations |
| **Simulation** | 52 | Drifts counterfactuals back toward training distribution, collapses hypothesis spaces prematurely |
| **Abstraction** | 51 | Merges distinct categories, over-generalizes, loses resolution at classification boundaries |
| **Metacognition** | 54 | Hallucinates without recognition, maintains uniform confidence regardless of actual reliability |

The right ability is retrieved automatically via the query you provide. No manual selection.

**Quality standard:** Every ability passes three tests — it must be a cognitive operation (not domain knowledge), LLM-executable (no external tools required), and domain-agnostic (applicable across subject areas without modification).

---

## Measured Results

All results below are from internal evaluation against **Claude Opus 4.6** — a frontier model with extended chain-of-thought. We improved a model that already reasons well.

### Beyond-Reasoning Benchmark (strongest evidence)

140 tasks across 7 behavioral signals, tested under 3 conditions: no injection (A), single-injection (B), multi-injection (C).

| Signal | Baseline | With Injection | Delta |
|---|---|---|---|
| **Structure** (organized multi-step reasoning) | 0.588 | 0.812 | **+22.5%** |
| **Precision** (correct formulas, quantified claims) | 0.735 | 0.762 | +2.8% |
| **Epistemic behavior** (multi-framework reasoning) | — | B: -4.4% / C: +1.9% | Recovery pattern |

Key finding: **Single-injection narrows the model onto one analytical framework** (tunnel vision). Multi-injection recovers breadth through competing suppression signals. This pattern replicated across two independent blind evaluations.

### Hard Reasoning Tasks

110 tasks from BIG-Bench Hard, MuSR, and CausalBench in two-stage blind evaluation:

| Metric | Result |
|---|---|
| Overall correctness | **+7.1pp** (69.7% → 76.8%) |
| Hardest subset (multi-step abductive reasoning) | **20% → 60%** |

### What we haven't proven yet

- **Cross-model validation.** Current testing is on Claude Opus 4.6 only. Multi-model benchmarks are in progress.
- **External peer review.** All evidence is generated internally. Independent replication is pending.
- **Suppression vs. amplification ablation.** We observe that suppression outperforms amplification consistently, but we have not published a controlled ablation isolating the two.
- **Head-to-head vs. well-crafted system prompts.** We have not benchmarked RA²R against optimized hand-written prompts on the same task set.

We publish our limitations because we take the work seriously.

---

## Injection Modes

| Mode | Abilities Retrieved | Use Case | Payload |
|---|---|---|---|
| **Single** | 1 | Direct tasks with a clear reasoning requirement | ~2,000 chars |
| **Multi** | 4 | Complex tasks requiring multiple analytical lenses | ~4,000 chars |

Multi-mode retrieves four abilities that work together: the best match for your query, its prerequisite, a reinforcing ability, and a competing analytical lens. Suppression signals are deduplicated across all four.

**When to use which:** Start with single. If your tasks involve multi-dimensional reasoning where tunnel vision is a risk, switch to multi.

---

## Applicable Domains

RA²R abilities are domain-agnostic by design — they encode reasoning procedures, not domain knowledge. We have documented failure patterns and ability mappings across 15 verticals:

Software Engineering · Financial Services · Legal Tech · Healthcare · Multi-Agent Orchestration · Cybersecurity · RAG & Retrieval Systems · Logistics & Supply Chain · Customer Service · Algorithmic Trading · Manufacturing & Digital Twins · Insurance & Actuarial · Life Sciences · Autonomous Research · Robotics

Each vertical has specific documented failure patterns (e.g., temporal leakage in backtesting, causal reversal in root-cause analysis, category collapse in medical diagnosis) with corresponding ability chains. Full documentation at [ejentum.com](https://ejentum.com).

---

## API

- **One endpoint:** `POST /logicv1/`
- **Zero LLM inference cost** on our side — retrieval only
- **Rate limit:** 100 req/min per key
- **Graceful degradation:** if API is unreachable, your agent continues on native reasoning
- **Schema stability:** `/logicv1/` guaranteed stable with additive changes only; 12-month minimum support window after any new version
- **Edge-delivered** with DDoS protection and request-level authentication

---

## Pricing

| | Free | Lite | Pro | Enterprise |
|---|---|---|---|---|
| **Price** | Free | €10/mo | €20/mo | Custom |
| **Calls** | 100 total | 10,000/mo | 100,000/mo | Custom |
| **Mode** | Single | Single | Single + Multi | All |
| **Rate** | — | 100/min | 100/min | Custom |

No card required for free tier. Start testing in 30 seconds.

---

## What Ejentum Is Not

- **Not a prompt library.** Each ability is a structured protocol with execution topology, not a text snippet you copy-paste.
- **Not a knowledge base.** We do not add domain facts. Use RAG for information retrieval; use RA²R for reasoning structure.
- **Not model-level.** No weights, activations, or fine-tuning. We operate entirely at the context layer.
- **Not a guarantee.** LLMs are probabilistic. Suppression reduces failure rates; it does not eliminate them.
- **Not magic.** Retrieval precision depends on query quality. Vague queries produce vague results.

---

<div align="center">

Based in Greece

</div>
