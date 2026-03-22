<div align="center">

<img src="ejentum_banner.png" width="100%" />

[![Website](https://img.shields.io/badge/ejentum.com-0B1121?style=for-the-badge&logo=googlechrome&logoColor=3b82f6)](https://ejentum.com)
[![API](https://img.shields.io/badge/RA²R_API-0B1121?style=for-the-badge&logo=lightning&logoColor=f59e0b)](https://ejentum.com)

</div>

---

### What is RA²R?

**Reasoning Ability-Augmented Retrieval** — a new paradigm that sits alongside RAG, not against it.

- **RAG** augments what the model *knows* (information retrieval)
- **RA²R** augments how the model *thinks* (cognitive operation retrieval)

One API call. ~200 tokens injected. No fine-tuning. No model changes.

```bash
curl -X POST "https://api.ejentum.com/logicv1/" \
  -H "Authorization: Bearer YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query": "your task description", "mode": "single"}'
```

---

### 311 Engineered Reasoning Abilities

Every ability is a 20-field cognitive protocol — not a prompt template.
Each one carries suppression directives, reasoning topology (DAG), and synergy mappings.

<table>
<tr>
<td width="33%">

**Causality** `52 abilities`<br/>
<sub>Root cause isolation, mechanism tracing, causal direction enforcement</sub>

</td>
<td width="33%">

**Temporal** `51 abilities`<br/>
<sub>Chronological strictness, timeline modeling, sequence validation</sub>

</td>
<td width="33%">

**Spatial** `51 abilities`<br/>
<sub>Topology validation, boundary enforcement, structural reasoning</sub>

</td>
</tr>
<tr>
<td>

**Simulation** `52 abilities`<br/>
<sub>Counterfactual modeling, systems dynamics, hypothesis testing</sub>

</td>
<td>

**Abstraction** `51 abilities`<br/>
<sub>Category enforcement, ontological boundaries, semantic precision</sub>

</td>
<td>

**Metacognition** `54 abilities`<br/>
<sub>Self-monitoring, contradiction detection, confidence calibration</sub>

</td>
</tr>
</table>

---

### Why Not Just a Better Prompt?

System prompts are static, monolithic, and expensive.
A 5,000-token system prompt × 10,000 daily calls × $0.03/1K tokens = **~$1,500/month in reasoning tokens alone.**

RA²R delivers ~200 tokens of *structured cognitive operations* — dynamic, retrieved per-task, with built-in suppression of known failure modes.

> **Key insight:** Suppression is multiplicative; amplification is additive.
> *"Never treat correlation as causation"* restructures reasoning more than *"find the root cause."*

---

### Built With

`Qdrant` · `Gemini Embeddings` · `n8n` · `Zuplo` · `TypeScript` · `React` · `Tailwind CSS`

---

<div align="center">
<sub>Based in Greece · Building the reasoning layer for autonomous agents</sub>
</div>
