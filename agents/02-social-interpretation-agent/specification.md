# Agent 2 — Specification

The Processing and Social Interpretation Agent follows the same seven-component specification structure as every other agent in this framework. The structure is unchanged; its content is adapted to an interpretive function.

Each of the four operational prompts carries its own full specification (see [`prompts/`](prompts/)). This document states the specification at agent level.

---

## 1 · Agent Role

An interpretive, evidence-bound component of the analysis layer. It transforms prepared social records into structured social interpretations.

It does not develop its own categories. The researcher defines the categories, permitted inputs, rules, expected outputs, and restrictions before execution.

## 2 · Agent Goal

To apply a predefined analytical schema consistently across a heterogeneous corpus, producing an enriched social layer in which every inferred attribute is accompanied by the evidence and confidence supporting it, and in which observed, derived, and inferred information remain distinguishable.

## 3 · Inputs (bounded)

| Input | Role |
|---|---|
| Prepared cross-platform dataset | Textual content and source information: platform, identifier, normalised timestamp, author, hashtags, place information, engagement indicators, matched keywords, source links |
| Multi-layer keyword taxonomy | Thematic classification support |
| Stakeholder and project framework | Actor category definitions |
| Institutional and project documents | Contextual grounding |
| Perception survey material | Citizen vocabulary and triangulation context |
| Spatial reference information | Facility, street, and zone tables for place interpretation |

Contextual resources are **selected and approved by the researcher**. The researcher therefore defines exactly what evidence is available to the agent.

## 4 · Procedural Steps (ordered)

| Stage | Operation |
|---|---|
| 1 | Input verification and source-record linkage |
| 2 | AI-assisted social interpretation across four dimensions |
| 3 | Interpretation control and traceability |
| 4 | Enriched record assembly and export |

Within Stage 2, four operational prompts apply the four analytical dimensions. **These prompts are not separate agents.** They are bounded interpretive tasks within the same processing workflow.

## 5 · Rules and Constraints

**Evidence**
- Where evidence does not support a classification, the field remains unclear or unavailable, or is flagged for review. It is **not** filled through unsupported inference.
- Every inferred field carries a justification note recording the specific evidence used.
- Every inferred field carries a confidence value expressing the strength of that evidence.
- No information may be introduced that is unsupported by the source record or approved contextual material.

**Analytical status**
- Observed, derived, and inferred attributes remain distinguishable in the output.
- Derived fields are computed mechanically and must not be adjusted on interpretive grounds.

**Restrictions on inference**
- Do not construct individual-level human twins.
- Demographic cues retained only where sufficient evidence exists; absence of evidence is represented as `unclear`, not filled by assumption.
- Institutional accounts receive no individual demographic classification.
- Residence and spatial classifications are not assigned at greater precision than the record supports.
- Sentiment and emotional signals are limited to what is expressed; they are not claims about internal psychological states.
- Do not assign negative sentiment merely because a problem is mentioned.

**Multi-label handling**
- Allow multiple values where a record legitimately contains several activities or issues.
- Do not force unsupported categories to fill a schema.

**Context separation**
- Distinguish issues expressed in the record from contextual explanations drawn from surveys or project documents. Contextual findings are triangulation material, not attributed personal motivation.

## 6 · Output Format

The enriched social record: **four dimension groups**, with observed, derived, and inferred fields, plus confidence, justification, and review information for inferred attributes.

| Dimension | Schema |
|---|---|
| Profile | [`schemas/dimension-1-profile.md`](schemas/dimension-1-profile.md) |
| Activities and issues | [`schemas/dimension-2-activities-and-issues.md`](schemas/dimension-2-activities-and-issues.md) |
| Emotions and sentiment | [`schemas/dimension-3-emotions-and-sentiment.md`](schemas/dimension-3-emotions-and-sentiment.md) |
| Contextual, spatial, temporal | [`schemas/dimension-4-context.md`](schemas/dimension-4-context.md) |

Record identifiers and processing information are retained so output connects to its source, spatial references, and processing history.

Quantitative summaries, qualitative interpretations, maps, and other analytical products are **not** outputs of this agent. They are produced from the enriched dataset in a subsequent analysis stage.

## 7 · Validation Procedure

Three check types, applied before outputs enter analysis:

| Check | Verifies |
|---|---|
| **Structural** | Required fields returned in expected format; controlled fields use permitted values |
| **Evidence** | Inferred attributes carry required justification; no unsupported information introduced |
| **Cross-field** | Dependent analytical fields do not contradict one another |

Records containing insufficient evidence, ambiguity, or sensitive inference are marked for researcher review. The researcher confirms, corrects, or rejects before use.

Full procedure: [`validation.md`](validation.md).

---

## Task parameters and operational limits

| Parameter | Reference implementation |
|---|---|
| Execution engine | Anthropic Claude Opus models, user-supplied API key |
| Interface | Self-contained single-file browser application |
| Prompt count | 4 operational prompts across 4 stages |
| Batch size | 5–15 records per API call during development |
| Processing scope | Full dataset, or first 10 / 25 / 50 / 100 records |
| Recorded per run | `run_id`, `prompt_version`, `model_version`, `input_dataset_version`, `parameter_profile`, `qa_summary` |

**Batch size controls how records are transferred to the model. It does not change the analytical schema applied to them.**

The specification is defined and documented independently of a specific model version, supporting methodological consistency when the underlying model is updated.

The prompt and output structure remain fixed within a processing run. Where testing identifies unclear instructions, missing restrictions, or inconsistent outputs, the specification is revised as a **new version** before further execution.
