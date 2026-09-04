# SDT AI Agents

**A researcher-specified, reproducible agent suite for building a Social Digital Twin (SDT) from social media traces, institutional documents, and urban spatial data.**

This repository publishes the full specification of three AI agents used to collect, prepare, interpret, and validate social data for an urban Social Digital Twin. It contains the prompt specifications, input and output schemas, processing rules, validation procedures, and reproducibility protocol required for an independent researcher to inspect, repeat, or adapt the method.

The agents are not autonomous systems. Each is a bounded, task-specific process in which a large language model (LLM) is used strictly as an execution engine for a procedure that the researcher defines in advance. The specification is the method; the model executes within it.

---

## What is published here

| Component | Function | Nature of the task |
|---|---|---|
| **Agent 1 — Data Preparation** | Keyword derivation, relevance filtering, cleaning, normalisation, cross-platform integration | Rule-based, non-interpretive, instruction-governed |
| **Agent 2 — Processing and Social Interpretation** | Structured social interpretation across four analytical dimensions (profile; activities and issues; emotion; context) | Interpretive, evidence-bound, justification-required |
| **Agent 3 — Validation** | Reproducibility, agreement, and internal-coherence assessment of the outputs of Agents 1 and 2 | Mixed: deterministic checks plus interpretive flagging |

Each agent has its own directory containing a specification, its operational prompts, its input/output schemas, and its validation procedure.

**The prompts are published in full, as runnable specifications rather than summaries.** Each states its role, goal, bounded inputs, ordered procedural steps, rules and constraints, output contract, and validation procedure, together with every controlled vocabulary enumerated, the exact derivation rule for every mechanical field, edge-case handling, and error behaviour. Eleven operational prompts are published across the three agents.

---

## Repository structure

```
sdt-ai-agents/
├── README.md                          this file
├── LICENSE                            terms of use (permission required)
├── CITATION.cff                       machine-readable citation
├── docs/
│   ├── 01-framework-overview.md       how the three agents fit together
│   ├── 02-specification-schema.md     the seven-component prompt schema
│   ├── 03-reproducibility.md          what reproducibility means here, and how to test it
│   ├── 04-ethics-and-safeguards.md    data governance and ethical constraints
│   ├── 05-reference-implementation.md a documented reference run, with figures
│   └── 06-glossary.md                 controlled vocabulary
├── agents/
│   ├── 01-data-preparation-agent/
│   │   ├── README.md                  role, architecture, processing stages
│   │   ├── specification.md           the seven-component specification
│   │   ├── prompts/                   the four operational prompts, verbatim
│   │   ├── schemas/                   input and output field definitions
│   │   └── validation.md              quality control and auditability
│   ├── 02-social-interpretation-agent/   (same structure)
│   └── 03-validation-agent/              (same structure)
└── assets/figures/                    workflow and architecture diagrams
```

---

## Quick start

1. Read [`docs/01-framework-overview.md`](docs/01-framework-overview.md) to understand how the three agents relate.
2. Read [`docs/02-specification-schema.md`](docs/02-specification-schema.md) — every prompt in this repository follows the same seven-component structure.
3. Read [`docs/03-reproducibility.md`](docs/03-reproducibility.md) **before** running anything. It states what can and cannot be expected to reproduce.
4. Go to the agent you need under [`agents/`](agents/), and read its `README.md` before its prompts.

### Field status governs how results may be reported

Every field in the enriched output carries a status, and the four statuses have sharply different measured reliability. This distinction is the single most important thing to carry into any downstream use.

| Status | How produced | Measured reproducibility |
|---|---|---|
| Observed | Copied unchanged from the source record | 100% |
| Derived | Computed mechanically by a documented rule | 100% |
| Inferred — structural | Rule-based inference within a controlled vocabulary | 100% |
| Inferred — semantic | Contextual interpretation of meaning | 44.3%–86.3% |

A claim resting on an inferred-semantic field is not the same kind of claim as one resting on a derived field.

Running the agents requires an LLM API account. The reference implementation uses Anthropic Claude Opus models with a user-supplied API key, executed from a self-contained browser-based application. The specifications are written independently of any execution platform and can be run through another interface, provided the inputs, rules, and output schema are preserved.

---

## Reproducibility statement

Reproducibility in this repository does **not** mean determinism. The underlying language model is probabilistic, and identical inputs will not always yield byte-identical outputs. What is reproducible is the **procedure**, which rests on four published properties:

1. **A documented specification.** Every prompt is published in full, so the exact definition of each task can be examined.
2. **A fixed output schema.** Results from different runs are structurally comparable.
3. **Traceability from output to source.** Every processed record retains its original identifier, raw text, and provenance.
4. **Empirically measured variability.** Output variance is tested through repeated runs and reported, rather than assumed away.

Measured variance for the reference implementation is reported in [`docs/03-reproducibility.md`](docs/03-reproducibility.md). In summary: observed, mechanically derived, and structurally inferred fields reproduce at 100%; semantic classification varies, with stakeholder classification at 86.3% raw agreement and sentiment classification between 44.3% and 58.8% depending on the comparator used.

---

## Scope and limits

These agents were developed and tested on a single urban case study: a social housing district undergoing a multi-year renewal programme, with a corpus drawn from Instagram, X/Twitter, and TikTok in French. The specifications are designed to be transferable — the keyword taxonomy, spatial reference tables, stakeholder framework, and contextual documents are all replaceable parameters — but transferability has not been empirically tested on a second case. Users adapting this method to another context should re-run the validation procedures in [`agents/03-validation-agent/`](agents/03-validation-agent/) rather than assume the reported reliability figures carry over.

Known limits are stated plainly in each agent's `README.md` and consolidated in [`docs/03-reproducibility.md`](docs/03-reproducibility.md).

---

## Licence and permission

This repository is publicly readable so that the method can be inspected and verified. **It is not open source.** Reuse, adaptation, redistribution, or incorporation into other work requires written permission from the author. See [`LICENSE`](LICENSE).

Citation and quotation for the purposes of academic review, criticism, and verification are permitted without prior permission, with attribution.

---

## Citation

See [`CITATION.cff`](CITATION.cff) for the machine-readable record. If you are citing a specific state of this method, cite the archived release rather than the repository's current state, since the repository may change.

---

## Contact

Saleh Qanazi — TVES Laboratory, Université de Lille
ORCID: [0000-0002-8860-9667](https://orcid.org/0000-0002-8860-9667)
Email: saleh.qanaze@najah.edu

Requests for permission to use, questions about the specifications, and reports of errors should be directed to the author.
