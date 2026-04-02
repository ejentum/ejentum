<div align="center">

<img src="ejentum_banner.png" width="100%" />

[![Website](https://img.shields.io/badge/ejentum.com-0B1121?style=for-the-badge&logo=googlechrome&logoColor=ff4d6a)](https://ejentum.com)
[![Docs](https://img.shields.io/badge/Documentation-0B1121?style=for-the-badge&logo=readthedocs&logoColor=10b981)](https://ejentum.com/docs)
[![Benchmarks](https://img.shields.io/badge/Benchmarks-0B1121?style=for-the-badge&logo=github&logoColor=f59e0b)](https://github.com/ejentum/benchmarks)
[![Examples](https://img.shields.io/badge/Examples-0B1121?style=for-the-badge&logo=github&logoColor=a78bfa)](https://github.com/ejentum/examples)
[![Paper](https://img.shields.io/badge/Research_Paper-0B1121?style=for-the-badge&logo=arxiv&logoColor=e74c3c)](https://github.com/ejentum/benchmarks/blob/main/research/paper/under_pressure.pdf)

</div>

---

Your agent reaches the right answer 87% of the time. It also stops at the first plausible explanation, never checks its own assumptions, and projects uniform confidence regardless of how uncertain the reasoning actually is. These failures don't throw exceptions. They ship to production inside fluent, confident text.

**Ejentum exists because the reasoning gap is structural, not informational.** RAG solved what models know. Nobody solved how they think. We built for that.

**RA²R** (Reasoning Ability-Augmented Retrieval) retrieves engineered cognitive operations -- not documents, not facts -- and injects them into an LLM's context at inference time. 311 abilities across six reasoning dimensions. One API call. The model's next output reflects a different reasoning structure before the first token generates.

---

## The Logic API

Your agent sends a task description. The Logic API returns a structured reasoning scaffold:

- **Negative Gate** -- the failure pattern to avoid, stated as a concrete scenario the model recognizes
- **Suppression Signals** -- named cognitive shortcuts to block (e.g., `surface_level_stop`, `single_variable_fixation`)
- **Reasoning Topology** -- the execution structure: steps, decision gates, loops, exit conditions
- **Falsification Test** -- the verification criterion the model checks its output against

The scaffold is injected into the agent's system prompt before the task. The suppression signals are the core mechanism. They constrain the model's output space multiplicatively -- each named failure mode eliminates an entire branch of incorrect reasoning. In our testing, suppression consistently outperforms amplification alone.

```bash
curl -X POST "https://ejentum-main-ab125c3.zuplo.app/logicv1/" \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "Why did deployment fail after the config change?", "mode": "single"}'
```

**Two modes:**

| Mode | What it returns | Tokens | Best for |
|------|----------------|--------|----------|
| **Ki** | One engineered cognitive operation | ~500 | Focused, single-domain tasks. Highest signal density per token. |
| **Haki** | Four synergized operations with compound suppression | ~900 | Complex, cross-domain analysis. Merged vectors block failure modes single scaffolds can't reach. |

Retrieval under 1 second. Deterministic. No LLM inference on our side. If the API is unreachable, your agent continues on native reasoning. Enhancement, not dependency.

---

## Measured Results

Three independent benchmarks. Two-stage blind protocol: separate generation and evaluation, the judge never saw which condition produced which output. Source data for every number is in the [benchmarks repo](https://github.com/ejentum/benchmarks).

### EjBench -- 180 custom professional tasks

Tasks designed to break in production: counter-intuitive causal chains, cross-metric contradictions, epistemic state tracking. 7-factor blind rubric. Claude Opus 4.6 at maximum effort.

| Factor | Baseline | With Scaffold | Change |
|--------|----------|--------------|--------|
| Composite | 0.621 | 0.722 | **+10.1pp** |
| Self-Monitoring | 0.94/3.0 | 1.81/3.0 | **+92%** |
| Verification | 1.50/3.0 | 2.16/3.0 | **+45%** |
| Alternative Consideration | 1.37/3.0 | 1.85/3.0 | **+35%** |
| Correctness | 2.60/3.0 | 2.49/3.0 | -0.11 |

Correctness dipped. We report it. The agent spends more attention on self-monitoring and verification, occasionally trading accuracy for caution. On a 3-point scale where baseline is already at 2.60, the trade-off is small. On production deployments where trust matters, it may be worth it. On deployments where raw accuracy is all that counts, it may not. That's your call, not ours.

### BBH / CausalBench / MuSR -- 70 published academic tasks

Tasks designed by independent research teams with no knowledge of RA²R. Same blind protocol.

| Factor | Baseline | With Scaffold | Change |
|--------|----------|--------------|--------|
| Composite | 0.476 | 0.684 | **+20.8pp** |
| Self-Monitoring | 0.74/3.0 | 1.73/3.0 | **+134%** |
| Verification | 0.96/3.0 | 1.77/3.0 | **+84%** |
| Correctness | 2.19/3.0 | 2.33/3.0 | **+6%** |

Ki dominated on focused tasks. Haki degraded correctness (-0.12) on single-domain problems. The reversal is clean: focused tasks need focused scaffolds, complex tasks need compound suppression. We published both directions, not just the flattering one.

### ARC-AGI-3 -- the world's only unbeaten benchmark

Interactive reasoning: an agent dropped into an unknown game with no instructions, no rules, no objective. Frontier model performance on ARC-AGI-3: 0.26%. Both our conditions scored RHAE 0.0. Neither cleared Level 0.

The evidence is not in game outcome. It's in what happened to the reasoning process over 25 sequential steps:

| Metric | Baseline | Augmented |
|--------|----------|-----------|
| Memory decay slope | -0.005 (degrading) | +0.014 (improving) |
| Scaffold half-life | 0 | 24 steps (entire game) |
| Reasoning depth trend | 0.86 | 10.50 (12.2x) |
| Stuck episodes | 2 | 1 (50% fewer) |

Baseline reasoning quality collapsed over time. Augmented reasoning quality grew. The scaffold persisted for 24 steps without re-injection. This is the strongest direct evidence for the [Cognitive Scaffolding Thesis](https://ejentum.com/blog/cognitive-scaffolding-thesis): abilities act as persistent attention anchors that compound across extended execution chains.

Full reports, 2,161 data files, raw traces, generation outputs, judgment scores, and system prompts: **[ejentum/benchmarks](https://github.com/ejentum/benchmarks)**

---

## Six Reasoning Dimensions

The 311 abilities are not a prompt library. Each is a structured protocol with an execution topology, suppression vectors, amplification signals, and graph edges connecting it to other abilities. They are organized by class of cognitive failure, not by subject matter.

| Dimension | Abilities | The failure it addresses |
|-----------|----------|--------------------------|
| **Causality** | 52 | The model reverses the direction of causation. Treats correlation as mechanism. |
| **Temporal** | 51 | The model compresses time. Confabulates timelines. Anchors to optimistic durations. |
| **Spatial** | 51 | The model violates boundaries. Routes through walls. Ignores structural constraints. |
| **Simulation** | 52 | The model can't hold a counterfactual. The real world bleeds into the hypothetical. |
| **Abstraction** | 51 | The model collapses categories. Specifics absorb into generalities. Metaphor becomes mechanism. |
| **Metacognition** | 54 | The model doesn't know it's wrong. Confidence is uniform. Errors reinforce errors. |

Browse all 311: [ejentum.com/abilities](https://ejentum.com/abilities)

---

## Integrate

One REST endpoint. No SDK. Works with anything that can POST and inject text into a prompt.

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

## Unexpected Findings

We built scaffolds to prevent reasoning shortcuts. We did not build them to teach new reasoning modalities. But during ARC-AGI-3 testing, two behaviors emerged that we did not engineer:

**Spontaneous domain shift.** At step 15 of a 25-step spatial navigation game, the scaffolded agent stopped reasoning in natural language and switched to symbolic mathematical notation. It defined formal variables, computed coordinates algebraically, and reasoned about movement vectors. The scaffold's suppression signal (`start_end_only_thinking`) constrained a failure mode. The agent found its own solution to that constraint. The suppression was the pressure. The math was the agent's adaptation.

**Emergent query evolution.** The agent's queries to the Logic API improved systematically over 25 steps -- from generic ("help me reason about this game") to spatially precise ("wall barrier at x=29-33 for rows 30-39 blocks rightward movement") to metacognitive ("robot appears stuck after 5 turns"). Nobody instructed query format or specificity. The agent learned to be a better user of reasoning infrastructure through practice, not instruction.

These are observations from traced data, not marketing claims. The full step-by-step traces are in the [benchmarks repo](https://github.com/ejentum/benchmarks/tree/main/arc-agi-3). We report them because they suggest something about how suppression signals interact with model behavior that we don't fully understand yet.

Read the full analysis: [What Happened When an LLM Taught Itself Symbolic Math](https://ejentum.com/blog/arc-agi-3-emergent-behaviors)

---

## What We Know, What We Don't

**What the data demonstrates:**
- Structured reasoning injection produces measurable behavioral changes across three independent benchmarks, two model sizes, and both single-turn and multi-step settings.
- The effect is primarily on reasoning quality (self-monitoring, verification, alternative consideration), not raw correctness. Correctness improved on focused external tasks (+7.1pp) and held flat on complex custom tasks.
- Scaffolds persist across extended execution chains (half-life: 24 steps) and compound rather than decay.
- Suppression signals work even when the retrieved ability comes from a mismatched domain (retrieval precision was 38%; improvements persisted).

**What we haven't proven:**
- **Cross-model generalization.** All benchmarks are on Claude (Opus 4.6 for single-turn, Sonnet 4.6 for ARC-AGI-3). We expect the mechanism generalizes -- suppression operates on properties of transformer attention, not model-specific behaviors -- but expectation is not evidence. Multi-model testing is in progress.
- **Ablation of suppression vs. amplification.** We observe that suppression consistently outperforms amplification. We have not published a controlled ablation isolating the two mechanisms with matched token budgets.
- **Human evaluation at scale.** Our evaluations use a two-stage blind protocol with LLM-as-judge. Generation and scoring are separate stages. The evaluator never sees condition labels. This mitigates but does not eliminate the possibility of systematic bias. Human evaluation on a subset would strengthen the evidence.
- **Statistical power on sub-analyses.** The spatial navigation regression rests on 5 tasks. The ARC-AGI-3 study is n=1 per condition. The headline numbers are robust; the sub-group findings are indicative.

We publish limitations because the work should be evaluated on what it demonstrates, not what we believe it will demonstrate. Every claim traces to a data file. Every negative finding is in the reports. The [benchmarks repo](https://github.com/ejentum/benchmarks) contains 2,161 files so you can verify independently.

---

## Repositories

| Repo | What's inside |
|------|--------------|
| **[benchmarks](https://github.com/ejentum/benchmarks)** | 2,161 files. Full reports, raw action traces, 1,390 generation outputs, 747 judgment scores, system prompts, aggregated metrics. Three benchmark suites. CC BY 4.0. |
| **[docs](https://github.com/ejentum/docs)** | 15 documentation pages. Quickstart to advanced method. Same content as [ejentum.com/docs](https://ejentum.com/docs). |
| **[examples](https://github.com/ejentum/examples)** | 22 integration examples. Python, TypeScript, curl, MCP server, n8n workflow, skill files, no-code guides. MIT. |

---

<div align="center">

**Ejentum.** Reasoning infrastructure for agentic AI.

Where RAG retrieves what the model should know, RA²R retrieves how it should think.

[ejentum.com](https://ejentum.com) · [documentation](https://ejentum.com/docs) · [blog](https://ejentum.com/blog) · [info@ejentum.com](mailto:info@ejentum.com)

</div>
