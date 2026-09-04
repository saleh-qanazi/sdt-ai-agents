# 1. Framework Overview

## 1.1 What an agent is here

An AI agent in this framework is a **task-specific, prompt-specified process built on a large language model and governed by a researcher-defined specification.**

The model is used strictly as an execution engine for a bounded task. The researcher defines the task, selects the inputs, establishes the rules, and specifies the expected output. The agent does not set its own goals, does not decide independently what information to use, and does not develop its own categories. It is not autonomous in the sense used in reinforcement learning. A closer analogy is a specialised operator working from a precise brief: the researcher writes the specification that governs behaviour, and the model executes within it.

This definition applies identically to all three agents. What differs between them is the nature of the task, not the mechanism.

## 1.2 The general workflow

Every agent in this framework passes through the same four-part cycle.

```
  RESEARCHER DEFINES THE METHOD          MODEL EXECUTES        HUMAN OVERSIGHT
  ┌──────────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
  │ 1 Specification  │→ │ 2 Development│→ │ 3 Configured │→ │ 4 Validation │
  │                  │  │              │  │   agent      │  │              │
  │ seven components │  │ write spec   │  │ bounded      │  │ check against│
  │ + parameters and │  │ test on      │  │  inputs      │  │  validation  │
  │ operational      │  │  sample data │  │ execute      │  │  procedure   │
  │ limits           │  │ refine and   │  │  procedure   │  │ confirm,     │
  │                  │  │  stabilise   │  │ structured   │  │  correct, or │
  │                  │  │              │  │  outputs     │  │  re-run      │
  └──────────────────┘  └──────────────┘  └──────────────┘  └──────────────┘
          ▲                                                          │
          └──────── feedback to specification when refinement ───────┘
                             is required
```

**Stage 1 — Specification.** The researcher writes and fixes a seven-component specification, plus task parameters and operational limits. See [`02-specification-schema.md`](02-specification-schema.md).

**Stage 2 — Development.** The specification is developed through structured prompt engineering: written, tested on small samples, refined, and stabilised. It is documented before the agent is used on the full dataset. No hand-written program defines the analytical procedure; the specification is the procedure.

**Stage 3 — Configured agent.** Once stabilised, the specification is implemented as a self-contained browser-based application that stores the instruction logic and interface in a single file and connects to an LLM through an API using a user-supplied key. Bounded inputs enter; structured, source-linked outputs leave. This turns the method into a reusable research instrument.

**Stage 4 — Validation.** Outputs are checked against the defined validation procedure. The researcher confirms, corrects, or re-runs. Repeated runs are used to evaluate variability. Feedback can refine the specification, which is then versioned.

## 1.3 The three agents and their division of labour

The three agents are separated by the **type of judgement** each task requires. This separation is deliberate and load-bearing: it is what allows different levels of reliability to be assigned to different fields in the final dataset.

### Agent 1 — Data Preparation

**Rule-based, non-interpretive, instruction-governed.** Operates on fixed researcher-defined rules: a keyword taxonomy and explicit filtering criteria. It transforms source records into prepared records.

It does **not** interpret social meaning, assign sentiment, or make social judgements. Its outputs are structurally auditable and every decision traces back to an explicit rule.

Tasks: keyword identification and consolidation, relevance filtering, cleaning and normalisation, platform-specific preparation, cross-platform integration.

→ [`agents/01-data-preparation-agent/`](../agents/01-data-preparation-agent/)

### Agent 2 — Processing and Social Interpretation

**Interpretive, evidence-bound.** Receives prepared records and produces structured social interpretations across four analytical dimensions.

Because semantic interpretation is involved, this agent operates under a stricter evidence regime than Agent 1: every inferred field must carry a justification note recording the evidence used, and a confidence value expressing the strength of that evidence. Where evidence is insufficient, the field remains unclear or is flagged for review rather than completed through unsupported inference.

Tasks: profile classification; activities and issues classification; emotion and sentiment classification; contextual, spatial, temporal, and multimedia annotation.

→ [`agents/02-social-interpretation-agent/`](../agents/02-social-interpretation-agent/)

### Agent 3 — Validation

**Quality control.** Assesses the reliability, reproducibility, and internal consistency of the outputs of Agents 1 and 2. It compares repeated runs, detects unstable classifications, checks internal coherence, and organises validation records.

It does **not** produce interpretations and does **not** make final correction decisions. Those remain the researcher's responsibility. Its function is to surface instability, not to resolve it.

Tasks: filtering-consistency assessment of Agent 1; reproducibility and agreement assessment of Agent 2; cross-dimension coherence testing.

→ [`agents/03-validation-agent/`](../agents/03-validation-agent/)

## 1.4 Data flow between agents

```
  Source and contextual data
  ├─ raw social media records (platform exports)
  ├─ institutional and project documents
  ├─ survey and qualitative material
  └─ spatial and environmental reference data
                    │
                    ▼
         ┌──────────────────────┐
         │  AGENT 1             │  keyword derivation → relevance filtering
         │  Data Preparation    │  → cleaning → harmonisation → integration
         └──────────────────────┘
                    │
                    ▼
  Prepared social records  (unified cross-platform dataset)
                    │
                    ▼
         ┌──────────────────────┐
         │  AGENT 2             │  profile · activities and issues
         │  Social Interpretation│  · emotion · context
         └──────────────────────┘
                    │
                    ▼
  Enriched social records  (four analytical dimensions, source-linked)
                    │
        ┌───────────┴───────────┐
        ▼                       ▼
  ┌──────────────┐      Spatial join tables
  │  AGENT 3     │      (record ↔ zone / street / facility)
  │  Validation  │              │
  └──────────────┘              ▼
        │              Application and analysis layer
        ▼
  Reproducibility tables · coherence results
  · correction summaries · technical validation report
```

Throughout, a provenance and versioning structure records processing runs, prompt versions, model versions, parameter profiles, evidence links, spatial anchors, and validation outcomes. This is what makes any individual output traceable back to the record and the run that produced it.

## 1.5 Storage model

Outputs are written to a persistent relational store organised in five families:

| Family | Contents | Written by | Read by |
|---|---|---|---|
| **A. Source and contextual data** | Raw social records; institutional, project and survey sources; source-file and media references | Collection process | Agent 1 |
| **B. Prepared social records** | Relevance and matched-keyword fields; cleaned and normalised text and timestamps; harmonised platform fields | Agent 1 | Agent 2 |
| **C. Derived social representations** | Profiles; activities and issues; sentiment and emotion; contextual interpretations | Agent 2 | Analysis layer, Agent 3 |
| **D. Spatial and environmental backbone** | Buildings, streets, parcels, facilities, public spaces, boundaries, addresses, environmental mappings | GIS pipeline | Agent 2 (spatial anchoring), analysis layer |
| **E. Provenance, relationships, versioning and validation** | Dataset and keyword-set versions; processing runs; evidence links; spatial anchors; validation and uncertainty attributes | All agents + human review | All |

External resources — multimedia files, raw exports, streamed 3D context — are referenced rather than stored, with their metadata retained in the database.

## 1.6 Human oversight is not optional

Every agent in this framework is executed by the researcher, on demand, with a defined input dataset, prompt version, model version, and parameter configuration. None runs autonomously in the background.

Three oversight points are structural rather than discretionary:

1. **Agent 1** produces a validation report at each stage; the researcher manually validates random samples to check relevance decisions, confirm meaning was preserved during cleaning, and verify keyword mapping.
2. **Agent 2** flags ambiguous, weakly supported, or sensitive interpretations for review; the researcher confirms, corrects, or rejects them before they enter analysis.
3. **Agent 3** flags contradictions and unstable cases; it does not correct them.

Where testing reveals unclear instructions, missing restrictions, or inconsistent outputs, the specification is revised as a **new version** before further execution. Prompts are not edited mid-run.
