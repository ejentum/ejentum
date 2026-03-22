<div align="center">

<img src="ejentum_banner.png" width="100%" />

[![Website](https://img.shields.io/badge/ejentum.com-0B1121?style=for-the-badge&logo=googlechrome&logoColor=3b82f6)](https://ejentum.com)
[![Docs](https://img.shields.io/badge/Documentation-0B1121?style=for-the-badge&logo=readthedocs&logoColor=10b981)](https://ejentum.com)
[![API](https://img.shields.io/badge/Logic_API_v1-0B1121?style=for-the-badge&logo=lightning&logoColor=f59e0b)](https://ejentum.com)

</div>

---

## What is RA²R?

**Reasoning Ability-Augmented Retrieval** retrieves cognitive operations — not information — and injects them into an LLM's context at inference time.

| | RAG | RA²R |
|---|---|---|
| **Retrieves** | Documents, facts, context | Reasoning procedures, suppression signals, cognitive scaffolds |
| **Augments** | What the model *knows* | How the model *thinks* |
| **Payload** | Variable (chunks) | ~200 tokens (structured protocol) |
| **Requires** | Embedding + vector DB for your data | One API call — we handle retrieval |

One call. No fine-tuning. No model changes. No prompt engineering.

```bash
curl -X POST "https://ejentum-main-ab125c3.zuplo.app/logicv1/" \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "your task description", "mode": "single"}'
```

---

## What Gets Injected

Each ability is a **20-field cognitive protocol** — not a prompt template. The injection payload contains:

| Field | Purpose |
|---|---|
| `prompt_override` | 5-step procedural scaffold with explicit suppression directives |
| `graph_payload` | 4 control surfaces: amplification, suppression, cognitive_style, reasoning_elasticity |
| `reasoning_topology` | Machine-readable DAG encoding how the ability executes (S-nodes, G-gates, M-nodes) |
| `synergies` | Directed graph edges: `requires`, `enhances`, `fallback` |
| `suppression` | Named failure modes to block (e.g., `symptom_treatment_bias`, `surface_level_stop`) |
| `falsification_test` | Verification criterion — how to check if the ability was actually applied |

**The asymmetry principle:** Suppression is multiplicative; amplification is additive. Telling a model *"never treat correlation as causation"* eliminates an entire class of failure. Telling it *"find the root cause"* only increases the probability.

---

## 311 Abilities Across 6 Reasoning Dimensions

Every reasoning failure maps to one of six dimensions. Abilities are retrieved via hybrid dense+sparse vector search — no manual dimension selection.

| Dimension | Count | Failure Addressed | Example Ability |
|---|---|---|---|
| **Causality** | 52 | Direction-of-causation errors, correlation-as-causation | CA-007 Bayesian Updater, CA-031 Root Cause Miner |
| **Temporal** | 51 | Timeline confabulation, sequence reversal, temporal leakage | TE-003 Causality Enforcer, TE-004 Deadlock Breaker |
| **Spatial** | 51 | Topology violations, boundary failures, physical impossibilities | SP-001 Topology Validator, SP-006 Containment Auditor |
| **Simulation** | 52 | Counterfactual collapse, model-reality gap, hypothesis failure | SI-008 Multi-Agent Synergy Optimizer, SI-041 EVI Simulator |
| **Abstraction** | 51 | Category collapse, over-generalization, semantic blur | AB-005 Invariant Sentinel, AB-019 Ontological Boundary Guard |
| **Metacognition** | 54 | Hallucination spirals, bias blindness, confidence miscalibration | MC-016 Cognitive Mode Switcher, MC-004 Confidence Calibrator |

**Quality standard:** Every ability passes a 3-litmus-test — it must be a cognitive operation (not domain knowledge), LLM-executable (no external tools), and domain-agnostic (works across subject areas). 96 of 311 (31%) were rewritten during purification to meet this bar.

---

## Reasoning Topology

Each ability carries a DAG (directed acyclic graph) that encodes its execution structure using three node types:

- **S-nodes** — Sequential action steps
- **G-gates** — Decision checkpoints that branch execution
- **M-nodes** — Meta-cognitive reflection points where the LLM can pause, observe its own reasoning, and abandon the graph for freeform reflection before re-entering

84 of 311 abilities (27%) include M-nodes. These are the abilities that give the model explicit permission to stop and ask *"is my approach actually working?"*

**23 topology types** organized into 5 families:

| Family | Function | Examples |
|---|---|---|
| Control Flow | How execution moves | Gated Pipeline, Conditional Branch, Fork-Join Compare |
| Structural | How information is organized | Accumulate-Classify-Decide, Convergence Funnel, Chain of Custody Audit |
| Verification | How truth is tested | Bidirectional Verification, Adversarial Self-Attack, Constraint Net |
| Reference Frame | How perspective is managed | Abstraction Climb-and-Descend, Dialectical Spiral |
| Meta-Level | How reasoning monitors itself | Watchdog Monitor, Emergence Detection |

---

## Measured Results

Tested against **Claude Opus 4.6** (frontier model with extended chain-of-thought). RA²R improved a model that already reasons well.

| Benchmark | Tasks | Result |
|---|---|---|
| BIG-Bench Hard + MuSR + CausalBench | 110 tasks, blind evaluation | **+7.1pp** correctness (69.7% → 76.8%) |
| Multi-step abductive reasoning | Hardest subset | **20% → 60%** correctness |
| Beyond-Reasoning Benchmark | 140 tasks × 3 conditions | **+22.5%** structure signal (multi-injection) |
| Reasoning Topology Blind Eval | 5 professional domains | **+7.8 points** mean on 40-point scale (+25.7%) |
| Independent Validation (v6) | 5 new domains, no hints | **+7.0 lift**, 5/5 domain wins |
| M-node Impact (MC-016) | Metacognitive task | **+17 point** improvement |
| Tool delivery vs system prompt | Same abilities, different delivery | **+7.0 lift** for tool-based delivery |

**Honest boundaries:**
- External peer review pending
- Cross-model validation in progress (current testing on Claude Opus 4.6 only)
- Suppression reduces failure rates — it does not guarantee zero failures

---

## Injection Modes

| Mode | Abilities | What You Get | Payload Size |
|---|---|---|---|
| **Single** (Ki) | 1 | Pure signal: suppression + amplification + cognitive style + falsification test | ~2,000 chars |
| **Multi** (Haki) | 4 | PRIMARY + DEPENDENCY + AMPLIFIER + ALTERNATIVE with compound suppression and merged vectors | ~4,000 chars |

Multi-mode retrieves 4 abilities that work together: the best match, its prerequisite, a reinforcing ability, and a competing analytical lens. Suppression vectors are deduplicated and merged across all four.

---

## Industry Applications

Validated failure patterns across 15 verticals with specific ability mappings:

<table>
<tr>
<td width="33%"><b>Software Engineering</b><br/><sub>32 connected abilities — forward-scanning stops, cross-service dependency tracing, review completeness</sub></td>
<td width="33%"><b>Financial Services</b><br/><sub>30 abilities — temporal leakage in backtests, correlation-as-causation in credit models, censored data bias</sub></td>
<td width="33%"><b>Legal Tech</b><br/><sub>23 abilities — normative vs descriptive confusion, jurisdictional semantic drift, contradictory obligations</sub></td>
</tr>
<tr>
<td><b>Healthcare</b><br/><sub>30 abilities — mutually exclusive symptoms, contraindication gaps, dosage interaction modeling</sub></td>
<td><b>Multi-Agent Systems</b><br/><sub>27 abilities — local optimization blindness, inter-agent trust, error propagation through swarms</sub></td>
<td><b>Cybersecurity</b><br/><sub>27 abilities — premature attack surface enumeration, defensive-only perspective, static threat models</sub></td>
</tr>
<tr>
<td><b>RAG & Retrieval</b><br/><sub>27 abilities — semantic drift between query and docs, equal retrieval effort allocation, multi-hop drift</sub></td>
<td><b>Logistics</b><br/><sub>26 abilities — clearance violations, container nesting, shared dock allocation conflicts</sub></td>
<td><b>Autonomous Research</b><br/><sub>25 abilities — confirmation bias, citation authority fallacy, unidentified confounds</sub></td>
</tr>
</table>

<details>
<summary>More verticals</summary>

- **Customer Service** (24 abilities) — surface-level issue acceptance, premature resolution
- **Algorithmic Trading** (30 abilities) — indicator reversal, survivorship bias, nonlinear slippage
- **Manufacturing & Digital Twins** (26 abilities) — conservation law violations, coupled variable independence
- **Insurance & Actuarial** (29 abilities) — tail risk collapse, adverse selection, catastrophe correlation
- **Life Sciences** (29 abilities) — publication bias, binding-to-therapeutic conflation, subpopulation harm
- **Robotics** (25 abilities) — boundary integrity, inertia-ignoring trajectories, multi-robot deadlocks

</details>

---

## Architecture

```
Client → Zuplo Gateway (Cloudflare edge) → n8n Orchestrator → Qdrant Vector DB
              ↓                                    ↓                    ↓
         Auth + Rate Limit              Gemini Embedding 2        agent_logic_v2_gemini
         DDoS Protection               (3072 dimensions)         Hybrid dense + sparse
         Request Logging                Branch: single/multi     311 ability collection
```

- **Zero LLM inference cost** for retrieval — embeddings only
- **Rate limit:** 100 req/min per key (429 + Retry-After on exceed)
- **Graceful degradation:** if API is unreachable, agent continues on native reasoning
- **Versioning:** `/logicv1/` schema guaranteed stable; additive changes only; 12-month minimum support after v2

---

## Pricing

| | Free | Ki (Lite) | Haki (Pro) | Enterprise |
|---|---|---|---|---|
| **Price** | Free | €10/mo | €20/mo | Custom |
| **Calls** | 100 total | 10,000/mo | 100,000/mo | Custom |
| **Mode** | Single | Single | Single + Multi | All |
| **Rate** | — | 100 req/min | 100 req/min | Custom |
| **Card Required** | No | Yes | Yes | — |

---

## What Ejentum Is Not

- **Not a prompt library.** Each ability is a 20-field structured protocol with topology, not a text snippet.
- **Not a knowledge base.** We don't add domain facts — use RAG for information retrieval.
- **Not model-level.** No weights, activations, or fine-tuning. Operates entirely at the prompt layer.
- **Not a guarantee.** LLMs are probabilistic. Suppression reduces failure rates; it doesn't eliminate them.

---

<div align="center">

**Built with** `Qdrant` · `Gemini Embeddings` · `n8n` · `Zuplo` · `Cloudflare` · `TypeScript` · `React`

Based in Greece

</div>
