# Schema Metadata Conventions

Every field table in this directory uses the same columns. This document defines them.

---

## `analytical_status`

The single most important attribute. It determines how a field may be used and what reproducibility it carries.

| Status | Definition | Produced by | Reproducibility |
|---|---|---|---|
| **Observed** | Transferred directly from the source record without transformation | Platform export → Agent 1 → carried through | 100% |
| **Derived** | Computed mechanically from existing fields by a fixed, documented rule | Agent 1 or Agent 2, by rule | 100%, independently recomputable |
| **Inferred — structural** | Rule-based inference within a controlled vocabulary, where the rule is deterministic given the inputs | Agent 2 | 100% |
| **Inferred — semantic** | Contextual interpretation of meaning | Agent 2 | Variable; measured, not assumed |
| **Interpretation — contextual** | Aggregate context imported from external sources to aid reading | Agent 2 | Not a property of the record |
| **Control** | Pipeline metadata: flags, versions, identifiers | All agents | 100% |

A derived field that fails independent recomputation is a **defect**. A semantic field that varies across runs is **measured variance**. Confusing the two produces either false alarm or false confidence.

---

## `data_type`

`string` · `integer` · `float` · `boolean` · `enum` · `datetime` · `array<enum>` · `array<string>`

`datetime` values are `YYYY-MM-DD HH:MM` in UTC unless stated otherwise.

---

## `cardinality`

| Value | Meaning |
|---|---|
| `single` | Exactly one value, or empty |
| `multi` | Zero or more values from a closed list; empty is valid and common |
| `multi-free` | Zero or more free-text values |

**Multi-label fields must not be silently collapsed** to a single dominant value downstream. Where an analysis requires one value per record, the collapse rule must be stated.

---

## `permitted_values`

For enums, the complete closed list. A value outside it is a structural defect, not an edge case.

Where a list is long, the table cites [`controlled-vocabularies.md`](controlled-vocabularies.md).

---

## `nullable`

| Value | Meaning |
|---|---|
| `yes` | Empty is valid and expected |
| `yes — expected` | Empty is the *most common* correct value; a low empty rate is suspicious |
| `no` | Must always be populated; empty is a defect |

`unclear` is distinct from empty. `unclear` means the field was evaluated and evidence was insufficient. Empty means the field does not apply.

---

## `evidence_requirement`

What must be present in the record for the field to be populated.

| Level | Meaning |
|---|---|
| `none` | Mechanical; no evidence judgement |
| `direct` | Explicit statement in the record |
| `converging` | Several consistent indirect cues |
| `weak_permitted` | A single indirect cue is acceptable, at low confidence |
| `prohibited_without_X` | The field may not be populated unless condition X holds |

---

## `companion_fields`

Inferred fields carry a confidence and a justification. The table names them, because an inferred value without its companions is unreviewable and therefore invalid.

---

## `derivation_rule`

For derived fields, the exact rule. Stated precisely enough that an independent implementation reproduces the value — which is what the validation agent does.

---

## `category_provenance`

Where a controlled vocabulary came from: the programme framework, the perception survey, the keyword taxonomy, established literature, or researcher definition.

This matters for transferability. Categories drawn from one programme's strategic pillars do not transfer to another programme; categories drawn from established affective research travel further.

---

## Confidence semantics

Confidence appears on most inferred fields. It has one meaning throughout:

> **Confidence expresses the strength of the available evidence, not the probability that the classification is correct, and not the likelihood that it will reproduce.**

| Level | Condition |
|---|---|
| `high` | Direct or unambiguous evidence |
| `medium` | Several consistent but partly indirect cues |
| `low` | Limited or ambiguous evidence |

**Validation established an inverse relationship between confidence and cross-method stability.** High-confidence records were *least* stable, because high confidence reflects detection of contextual signal — irony, implicit criticism, evaluation spread across sentences — that rule-based comparators cannot reproduce. Filtering to high-confidence records selects for the records where methods disagree most.

Confidence must not be used as a reliability filter downstream.

---

## Justification fields

Every inferred field has one. Its function is to make the inference **reviewable and challengeable**.

A justification must name specific evidence: the words, phrases, hashtags, metadata or contextual signals used. Where the value is `unclear`, the justification records the absence of evidence.

`"Appears to be a local resident"` is not a justification. `"Possessive framing 'notre quartier' plus a named street from the L2 taxonomy"` is.

An inferred field without a justification is not low quality. It is **invalid**, because no reviewer can assess it.

---

## Review flags

| Field | Trigger |
|---|---|
| `profile_review_flag` | Content-based classification, unclear category, low-confidence demographics, sensitive disclosure |
| `emotion_review_flag` | Possible irony, mixed classification, very short text, single-emoji basis |
| `context_review_flag` | Borderline spatial inference, ambiguous place reference |

**Review flags are not advisory.** A flagged record that has not been reviewed should not contribute to a reported finding.
