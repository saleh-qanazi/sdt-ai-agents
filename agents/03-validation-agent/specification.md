# Agent 3 — Specification

---

## 1 · Agent Role

An AI-supported validation and quality-control component within the pipeline.

It assesses the reliability, reproducibility, and internal consistency of the outputs of Agent 1 and Agent 2. It **supports, but does not replace, researcher judgement** in technical validation.

## 2 · Agent Goal

To make the reliability of the pipeline's outputs measurable and visible: to determine which fields reproduce, which do not, where instability is concentrated, and which records require human review — without resolving any of it autonomously.

## 3 · Inputs (bounded)

| Input | Source |
|---|---|
| Iteration outputs from the Data Preparation Agent | Agent 1 |
| Enriched social layer and repeated runs | Agent 2 |
| Keyword taxonomy and filtering logic | Agent 1 |
| Classification fields and confidence/review indicators | Agent 2 |
| Removal audit tables | Agent 1 |
| Platform, time, and actor-type comparison logic | Researcher-defined |
| Independent rule-based reclassification methods | Researcher-constructed |

## 4 · Procedural Steps (ordered)

| Stage | Operation | Prompt |
|---|---|---|
| 1 | Validation input assembly | — |
| 2 | Agent 1 checking: iterative filtering review, keyword taxonomy stability, false-positive detection | Prompt 1 |
| 3 | Agent 2 checking: repeated classification runs, agreement analysis, stability assessment | Prompt 2 |
| 4 | Cross-dimension coherence tests | Prompt 3 |
| 5 | Flagging and correction support | — |
| 6 | Validation output assembly | — |

## 5 · Rules and Constraints

**Operational restrictions**
- Do not treat AI outputs as self-validating.
- Preserve traceability to original records and intermediate outputs.
- Distinguish deterministic checks from interpretive validation.
- Keep uncertain or unstable cases **visible**; do not hide them.
- Do not replace researcher interpretation or final correction decisions.
- Report contradictions, instability, and limits clearly.

**Measurement discipline**
- Use raw agreement, Cohen's Kappa, and confusion matrices together. Never report Kappa in isolation.
- Apply the same sample and field structure across compared runs.
- Do not establish either the agent or any comparator as ground truth.
- Do not combine results of different property types into a composite score.

**Handling of variation**
- Semantic variation is flagged for review, not treated automatically as error. Sentiment, emotion, and stakeholder type may differ because of genuine interpretive ambiguity.
- Do not correct records. Flag them.

## 6 · Output Format

| Output | Contents |
|---|---|
| Reproducibility tables | Agreement by field type, by platform, by text length, by confidence level |
| Coherence results | Test-by-test pass/fail with ratios and notes |
| Iteration comparison tables | Retained and removed records by iteration; removal audit |
| Correction summary | Records flagged, reason, and researcher resolution |
| Technical validation report | Consolidated findings and stated limitations |

## 7 · Validation Procedure

The validator itself must be checkable. See [`validation.md`](validation.md).

Minimum requirements:
- Every reported figure must be reproducible from the published input files and the specification.
- Sample composition must be stated, including any deliberate stratification.
- Denominators must be visible; ratios rather than bare percentages.
- Negative findings must be reported with the same prominence as positive ones.

---

## Task parameters and operational limits

| Parameter | Reference implementation |
|---|---|
| Campaign 1 | Five filtering iterations across the raw corpus |
| Campaign 2 | Four repeated classification runs across all records |
| Total technical checks | 34 |
| Comparator methods | Three independently constructed rule-based methods of increasing vocabulary breadth |
| Recorded per run | `run_id`, `prompt_version`, `model_version`, `validation_type`, `validation_status`, `validator`, `uncertainty_level` |
