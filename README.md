<div align="center">

<img src="ejentum_banner.png" width="100%" />

[![Website](https://img.shields.io/badge/ejentum.com-0B1121?style=for-the-badge&logo=googlechrome&logoColor=3b82f6)](https://ejentum.com)
[![Docs](https://img.shields.io/badge/Documentation-0B1121?style=for-the-badge&logo=readthedocs&logoColor=10b981)](https://ejentum.com)
[![API](https://img.shields.io/badge/Logic_API_v1-0B1121?style=for-the-badge&logo=lightning&logoColor=f59e0b)](https://ejentum.com)

</div>

---

## The Problem

LLM failures in production are not information problems — they are **structural reasoning problems.** Models reverse causality, confabulate timelines, collapse categories, and hallucinate without self-awareness. These failures persist regardless of how much context you retrieve.

RAG solves the information gap. The reasoning gap remains structurally unaddressed. That's what we built for.

---

## What is RA²R?

**Reasoning Ability-Augmented Retrieval** retrieves cognitive operations — not information — and injects them into an LLM's context at inference time.

| | RAG | RA²R |
|---|---|---|
| **Retrieves** | Documents, facts, context | Reasoning procedures, suppression signals, cognitive scaffolds |
| **Augments** | What the model *knows* | How the model *thinks* |
| **Payload** | Variable (chunks) | ~500–1,000 tokens (structured protocol) |
| **Integration** | Embedding + vector DB for your data | One API call — no data infrastructure needed |

---

## Integration

### API

```bash
curl -X POST "https://ejentum-main-ab125c3.zuplo.app/logicv1/" \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "your task description", "mode": "single"}'
```

The response returns a pre-rendered injection string. Inject it into your agent's context before the task prompt — that's the entire integration.

- **One endpoint:** `POST /logicv1/`
- **Zero LLM inference cost** on our side — retrieval only
- **Rate limit:** 100 req/min per key
- **Graceful degradation:** if API is unreachable, your agent continues on native reasoning
- **Schema stability:** additive changes only; 12-month minimum support window after any new version
- **Edge-delivered** with DDoS protection and request-level authentication

### Framework examples

<details>
<summary><b>LangChain (Python)</b></summary>

```python
import requests
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate

EJENTUM_URL = "https://ejentum-main-ab125c3.zuplo.app/logicv1/"
EJENTUM_KEY = "YOUR_API_KEY"

def get_reasoning_injection(task: str, mode: str = "single") -> str:
    try:
        response = requests.post(
            EJENTUM_URL,
            headers={
                "Authorization": f"Bearer {EJENTUM_KEY}",
                "Content-Type": "application/json"
            },
            json={"query": task, "mode": mode},
            timeout=2
        )
        response.raise_for_status()
        data = response.json()
        return f"[REASONING CONTEXT]\n{data[0][f'{mode}_logic']}\n[END REASONING CONTEXT]"
    except (requests.RequestException, KeyError, IndexError):
        return ""  # Graceful degradation

prompt = ChatPromptTemplate.from_messages([
    ("system", "{reasoning_context}\n\nYou are a senior analyst."),
    ("human", "{task}")
])

chain = (
    RunnablePassthrough.assign(
        reasoning_context=lambda x: get_reasoning_injection(x["task"])
    )
    | prompt
    | ChatOpenAI(model="gpt-4")
)

result = chain.invoke({"task": "Why did our supply chain costs spike in Q3?"})
```

</details>

<details>
<summary><b>CrewAI</b></summary>

```python
from crewai import Agent, Task, Crew

injection = get_reasoning_injection("Analyze root cause of production failures")

analyst = Agent(
    role="Production Analyst",
    goal="Identify the root cause of system failures",
    backstory=f"You are a production analyst.\n\n{injection}",
    llm=your_llm
)
```

In CrewAI, `backstory` is the system message. For multi-step crews, inject per task — each task may need a different reasoning dimension:

```python
tasks = [
    {"description": "Identify why production failed", "agent": analyst},     # → Causal
    {"description": "Estimate recovery timeline", "agent": planner},         # → Temporal
    {"description": "Draft incident report", "agent": writer}               # → Abstract
]

for task in tasks:
    injection = get_reasoning_injection(task["description"])
    task["agent"].backstory = f"{task['agent'].base_backstory}\n\n{injection}"
```

</details>

<details>
<summary><b>AutoGen</b></summary>

```python
import autogen

def get_ejentum_system_message(task: str, base_message: str, mode: str = "single") -> str:
    try:
        response = requests.post(
            "https://ejentum-main-ab125c3.zuplo.app/logicv1/",
            headers={
                "Authorization": "Bearer YOUR_API_KEY",
                "Content-Type": "application/json"
            },
            json={"query": task, "mode": mode},
            timeout=2
        )
        response.raise_for_status()
        data = response.json()
        injection = data[0][f"{mode}_logic"]
        return f"[REASONING CONTEXT]\n{injection}\n[END REASONING CONTEXT]\n\n{base_message}"
    except (requests.RequestException, KeyError, IndexError):
        return base_message

assistant = autogen.AssistantAgent(
    name="analyst",
    system_message=get_ejentum_system_message(
        "Investigate why model accuracy degrades after retraining",
        "You are a senior ML engineer."
    )
)
```

</details>

<details>
<summary><b>Anthropic Claude (tool_use)</b></summary>

```python
import anthropic

client = anthropic.Anthropic()

injection = get_reasoning_injection(
    "Evaluate tradeoffs between microservice and monolith architecture"
)

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    system=f"{injection}\n\nYou are a senior software architect.",
    messages=[
        {"role": "user", "content": "Should we split the billing service?"}
    ]
)
```

</details>

<details>
<summary><b>n8n</b></summary>

```
Webhook Trigger → HTTP Request (POST /logicv1/) → Set Node (extract injection) → AI Agent Node
```

**Set Node** extraction: `{{ $json[0].single_logic }}`

**AI Agent system message:**
```
[REASONING CONTEXT]
{{ $json.injection }}
[END REASONING CONTEXT]

You are a senior analyst.
```

</details>

### Injection modes

| Mode | Plan | Abilities | Use Case | Payload |
|---|---|---|---|---|
| **Single** | Ki | 1 | Direct tasks with a clear reasoning requirement | ~2,000 chars |
| **Multi** | Haki | 4 | Complex tasks requiring multiple analytical lenses | ~4,000 chars |

Multi (Haki) retrieves four abilities that work together: the best match for your query, its prerequisite, a reinforcing ability, and a competing analytical lens. Suppression signals are deduplicated across all four.

Start with Ki. Switch to Haki if your tasks involve multi-dimensional reasoning where single-framework tunnel vision is a risk.

### Delivery guidance

**Tool delivery > system prompt.** In our evaluation, delivering abilities as tool results (at the moment of task execution) produced a +7.0 point lift over injecting the same content into the system prompt. System prompts are processed once at conversation start and decay in attention over subsequent turns. Tool results arrive at the point of peak relevance — when the model is actively working on the task.

**Multi-turn agents:** Re-inject per turn. In conversations beyond ~5 turns, the scaffold from turn 1 has degraded from the model's active attention. Re-injection is not optional.

---

## What Changes in Practice

### Causality: Root cause isolation

**Query:** *"Why did our conversion rate drop 40% after the checkout redesign?"*

**Without injection:**
> *"The conversion rate likely dropped due to several factors including user interface changes, loading speed issues, and potential bugs in the new checkout flow. I recommend A/B testing the old and new designs to identify the specific cause."*

Lists plausible guesses. No causal mechanism. No falsifiable claim.

**With RA²R injection:**
> *"Isolating root cause. The checkout redesign introduced mandatory account creation — the most significant change in the deployment diff. Prior analytics show guest checkout accounted for the majority of conversions. Mechanism: mandatory account creation added a friction barrier at the conversion-critical step. I need to verify: does the percentage of users who previously used guest checkout correlate with the 40% drop? If yes, this is the primary cause. If not, I should look at secondary factors. I am suppressing the impulse to list multiple surface-level factors and instead tracing one causal chain to its root."*

Traces a single causal chain. Identifies a verifiable mechanism. States what would falsify it. *(Illustrative example from documentation)*

### Spatial: Gradient reasoning over static snapshots

**Without injection:**
> *"Disk usage is at 78% — we still have 22% headroom, so no action needed until it actually fills up."*

Treats the current value as sufficient. No projection. Reactive.

**With RA²R injection:**
> *"Project the breach: disk grew from 60% to 78% in 30 days, crossing 95% in approximately 28 days at this rate. Flag now — waiting for the limit means reactive firefighting. Schedule cleanup or capacity expansion before the threshold breach."*

Reasons about the slope, not just the position. Converts a static snapshot into a trajectory. *(From cognitive delta evaluation, SP-035 Threshold Breach Predictor)*

### Temporal: Delay anomaly detection

**Without injection:**
> *"The user clicked submit and the order appeared in the warehouse system 3 seconds later — that's normal propagation."*

Accepts the timing at face value.

**With RA²R injection:**
> *"Diagnose the timing: submit-to-warehouse normally takes 200ms via the event bus. A 3-second delay is 15x the expected propagation. Something absorbed 2.8 seconds — check queue depth, serialization overhead, and network partitions between the services."*

Flags the anomaly against expected baseline. Decomposes the gap into investigable components. *(From cognitive delta evaluation, TE-007 Delay Anomaly Detector)*

---

## How It Works

Each of 311 abilities is a **structured cognitive protocol** — not a prompt template, not a text snippet.

Every injection contains four components:
- A **procedural scaffold** the model follows step-by-step
- **Suppression directives** that block named failure modes (e.g., `symptom_treatment_bias`, `surface_level_stop`)
- An **execution graph** that encodes reasoning structure, including conditional branches and reflection checkpoints
- A **falsification test** that specifies how to verify the ability was actually applied

### Why suppression matters more than amplification

Telling a model *"find the root cause"* increases the probability of good output. Telling it *"reject any output that exhibits `symptom_treatment_bias`"* constrains an entire class of failure. One adds signal; the other removes noise. In our testing, the gap is consistent — though we have not yet published a controlled ablation isolating the two variables.

### Cognitive scaffolding

Our working hypothesis for *why* this works at the attention level: each ability persists in the context window as a structurally distinctive token sequence that the transformer continues to reference across subsequent reasoning steps — functioning as a **persistent cognitive scaffold.**

Multiple abilities create compound scaffolds where suppression signals stack and gate conditions cross-reference. This predicts — and we observe — that value compounds with task length: longer reasoning chains benefit more from scaffolding that prevents degradation.

This hypothesis is consistent with our benchmark data but has not been independently validated at the attention mechanism level.

### Meta-cognitive reflection

Some abilities include reflection checkpoints that give the model explicit permission to pause, observe whether its approach is working, and change course mid-reasoning. This is the difference between *"follow these steps"* and *"follow these steps, but stop if they're not working and rethink."*

---

## 311 Abilities Across 6 Reasoning Dimensions

Every LLM reasoning failure we've catalogued maps to one of six structural dimensions:

| Dimension | Count | Structural Failure |
|---|---|---|
| **Causality** | 52 | Reverses causal direction, treats correlation as causation, stops at surface-level explanations |
| **Temporal** | 51 | Confabulates timelines, reverses event sequences, leaks future data into past analysis |
| **Spatial** | 51 | Violates physical constraints, ignores topology, produces impossible configurations |
| **Simulation** | 52 | Drifts counterfactuals back toward training distribution, collapses hypothesis spaces prematurely |
| **Abstraction** | 51 | Merges distinct categories, over-generalizes, loses resolution at classification boundaries |
| **Metacognition** | 54 | Hallucinates without recognition, maintains uniform confidence regardless of actual reliability |

The right ability is retrieved automatically via the query you provide. No manual dimension selection.

### The 3-litmus-test

Every ability in the dataset must pass three criteria:

1. **Cognitive operation** — it describes a reasoning procedure, not domain knowledge. An ability that says "check for drug interactions" fails; one that says "verify mutual exclusivity of proposed constraints" passes.
2. **LLM-executable** — the model can follow it using only the information already in context. No external API calls, no tool use required.
3. **Domain-agnostic** — it works across subject areas without modification. The same ability that enforces causal direction in medical diagnosis enforces it in financial analysis.

We applied this standard retroactively and rewrote 31% of the dataset (96 of 311 abilities) that had accumulated domain-specific assumptions. The heaviest rewrites were in the Spatial dimension, where abilities had absorbed physics knowledge rather than remaining pure reasoning operations.

---

## Measured Results

All results below are from internal evaluation against **Claude Opus 4.6** — a frontier model with extended chain-of-thought. We chose to test against the strongest available model because improving a model that already reasons well is the harder claim.

### Beyond-Reasoning Benchmark

140 tasks across 7 behavioral signals, tested under 3 conditions: no injection (A), single-injection (B), multi-injection (C).

| Signal | Baseline (A) | Single (B) | Multi (C) |
|---|---|---|---|
| **Structure** (organized multi-step reasoning) | 0.588 | 0.800 (+0.213) | **0.812 (+0.225)** |
| **Precision** (correct formulas, quantified claims) | 0.735 | 0.762 (+0.028) | 0.762 (+0.028) |
| **Epistemic behavior** (multi-framework reasoning) | 0.000 (reference) | -0.044 (narrows) | **+0.019 (recovers)** |

Key finding: **single-injection narrows the model onto one analytical framework** (tunnel vision). Multi-injection recovers breadth through competing suppression signals. This pattern replicated across two independent blind evaluations.

Signals where we found no meaningful improvement (baseline already near ceiling): noise resilience, debiasing. Signal where injection trades off against another metric: token efficiency decreases as structure increases — the model produces longer, more organized output at the cost of conciseness.

### Hard Reasoning Tasks

110 tasks from BIG-Bench Hard, MuSR, and CausalBench in two-stage blind evaluation:

| Metric | Result |
|---|---|
| Overall correctness | **+7.1pp** (69.7% → 76.8%) |
| Hardest subset (multi-step abductive reasoning) | **20% → 60%** |

### What we haven't proven yet

- **Cross-model validation.** Current testing is on Claude Opus 4.6 only. Multi-model benchmarks are in progress.
- **External peer review.** All evidence is generated internally. Independent replication is pending.
- **Suppression vs. amplification ablation.** We observe that suppression outperforms amplification consistently, but have not published a controlled ablation isolating the two.
- **Head-to-head vs. optimized system prompts.** We have not benchmarked RA²R against well-crafted hand-written prompts on the same task set.

We publish our limitations because the work deserves to be evaluated on what it actually demonstrates, not what we believe it will demonstrate.

---

## Pricing

| | Free | Ki | Haki | Enterprise |
|---|---|---|---|---|
| **Price** | Free | €10/mo | €20/mo | Custom |
| **Calls** | 100 total | 10,000/mo | 100,000/mo | Custom |
| **Mode** | Single | Single | Single + Multi | All |
| **Rate** | — | 100/min | 100/min | Custom |

No card required for free tier.

### Measuring impact on your workload

We recommend a controlled evaluation before committing:

1. Run 50–100 representative tasks through your agent without injection
2. Run the same tasks with RA²R injection
3. Compare: task completion rate, reasoning chain quality (LLM-as-judge or human review), token cost per task

The delta is your ROI signal. If the improvement on your specific workload doesn't justify the cost, you'll know before paying.

---

## What Ejentum Is Not

- **Not a prompt library.** Each ability is a structured protocol with execution topology, not a text snippet you copy-paste.
- **Not a knowledge base.** We do not add domain facts. Use RAG for information retrieval; use RA²R for reasoning structure.
- **Not model-level.** No weights, activations, or fine-tuning. We operate entirely at the context layer.
- **Not a guarantee.** LLMs are probabilistic. Suppression reduces failure rates; it does not eliminate them.
- **Not magic.** Retrieval precision depends on query quality. Vague queries produce vague results.

---

<div align="center">

Engineering cognitive operations for agentic AI.

</div>
