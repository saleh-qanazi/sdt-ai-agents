# Agent 1 — Specification

The Data Preparation Agent is implemented as a single instruction-driven entity whose operation is organised into six sequential processing stages, executed through four operational prompts. Each prompt carries its own full seven-component specification (see [`prompts/`](prompts/)). This document states the specification at agent level.

---

## 1 · Agent Role

A rule-based, non-interpretive, instruction-governed component of the data infrastructure. It transforms source and contextual data into prepared records.

It does not interpret social meaning, assign sentiment or other social attributes, or make social judgements.

## 2 · Agent Goal

To produce a cleaned, normalised, harmonised, cross-platform dataset of records relevant to a defined urban project, with full provenance retained, ready for social interpretation.

## 3 · Inputs (bounded)

| Input | Format | Role |
|---|---|---|
| Platform social media exports | CSV or NDJSON | Raw records to be filtered and prepared |
| Documentary sources | PDF | Basis for keyword derivation and contextual grounding |
| Manual keyword list | Text or tabular | Researcher-supplied vocabulary |

No other source is admissible, including the model's background knowledge of the study area.

## 4 · Procedural Steps (ordered)

| Stage | Operation | Prompt |
|---|---|---|
| 1 | Document-informed keyword identification | Prompt 1 |
| 2 | Keyword consolidation and normalisation | Prompt 1 |
| 3 | Relevance-based filtering of social media records | Prompt 2 |
| 4 | Cleaning and normalisation of relevant records | Prompt 3 |
| 5 | Platform-specific dataset preparation | Prompt 3 |
| 6 | Cross-platform dataset integration | Prompt 4 |

Stage order is strict. Later stages depend on the outputs of earlier ones.

## 5 · Rules and Constraints

**Evidence**
- Do not invent keywords, content, or values not present in the inputs.
- Do not infer relevance without textual evidence. Where uncertain, mark as not relevant.
- A spatial reference alone is never sufficient evidence of relevance.

**Preservation**
- Preserve original identifiers, raw text, timestamps, and provenance throughout.
- Do not change the semantic meaning of content during cleaning.
- Informal language, emotional expressions, and resident vocabulary are preserved, not cleaned away.

**Scope**
- Do not perform sentiment analysis, demographic inference, or social interpretation. These belong to Agent 2.
- Do not construct individual-level profiles.

**Auditability**
- Log the reason for every exclusion.
- Retain intermediate datasets at each stage.

**Deduplication**
- Use content-based keys, not floating-point numeric identifiers.
- Cross-platform deduplication only on exact match of content, timestamp, and source.

## 6 · Output Format

| Output | Description |
|---|---|
| `KeywordTaxonomy.pdf` | Six-layer taxonomy with `keyword_id`, `term`, `layer`, `frequency`, `weight_tier` |
| `PreparedSocialRecord_[platform].csv` | Cleaned, normalised per-platform datasets |
| `UnifiedCrossPlatform.csv` | Merged cross-platform dataset, common field set |
| Quality-assurance report | Machine-readable; field completeness and provenance integrity |
| Processing audit log | Exclusion reasons, deduplication log, stage record counts |
| Intermediate datasets | Keyword tables, relevance-flagged files, pre-merge cleaned files |

## 7 · Validation Procedure

At each stage the agent produces a brief validation report: number of extracted keywords, distribution of relevant and non-relevant records, number of duplicates removed, final record count per platform.

The researcher performs manual validation on random samples at multiple stages, checking relevance decisions, confirming meaning was preserved during cleaning, and verifying keyword mapping.

Where notable inconsistencies are found, the prompt is revised as a **new version** and the relevant part of the workflow re-executed.

Full procedure: [`validation.md`](validation.md).

---

## Task parameters and operational limits

| Parameter | Reference implementation |
|---|---|
| Execution engine | Anthropic Claude Opus models, user-supplied API key |
| Interface | Self-contained single-file browser application |
| Prompt count | 4 operational prompts across 6 stages |
| Processing scope control | Full dataset or defined test sample |
| Recorded per run | `run_id`, `prompt_version`, `model_version`, `input_dataset_version`, `parameter_profile`, `qa_summary` |

The specification is documented independently of the execution platform. It can be run through another interface provided inputs, rules, sequence, and output schema are preserved.
