# Dimension 1 — Profile

**15 fields. Question addressed: who.**

Describes broad characteristics of the actors visible in the discourse. Categories derive from the project stakeholder framework, extended where additional actor types appear in the corpus.

> **Governing constraint.** These fields support aggregate analysis of participation patterns. They must never be assembled into an individual-level model of a real person. See [`../../../docs/04-ethics-and-safeguards.md`](../../../docs/04-ethics-and-safeguards.md).

---

## 1.1 `stakeholder_category`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum |
| Cardinality | 1 |
| Nullable | No — `Unclear` is the fallback |
| Companions | `profile_confidence`, `profile_review_flag` |
| Provenance | Project stakeholder framework, extended with `Media` because media actors appeared in the corpus |
| Reproducibility | **86.3% raw agreement, Cohen's κ = 0.227** against three independent name-based comparators |

**Permitted values**

| Value | Definition |
|---|---|
| `Institutional & Governance` | Municipal bodies, metropolitan authorities, state agencies, programme authorities, elected representatives acting officially |
| `Design & Technical Teams` | Architects, urban designers, engineering consultancies, project managers |
| `Community Stakeholders` | Residents, resident collectives, local associations, neighbourhood groups |
| `Funding & Strategic Partners` | Social landlords, investors, funding agencies, strategic partners |
| `Academic` | Universities, research laboratories, researchers posting as such |
| `Media` | News organisations, journalists, local outlets, bloggers acting as media |
| `Unclear` | Evidence insufficient |

**Evidence permitted:** author display name, account handle, account biography where exported, and body content where it explicitly states the author's role.

**Known instability:** the same 18 records disagreed across every agent–comparator pair in validation, concentrated on institutional, media, and organisational actors whose role is clearer from bio or content than from the account name. This is a systematic difference between context-based and name-based classification, and does not establish which is correct. Records classified on bio or content rather than name carry `profile_review_flag = yes`.

---

## 1.2 `stakeholder_institution`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | string (controlled by the stakeholder framework) |
| Cardinality | 1 |
| Nullable | No — `Unidentified` is the fallback |
| Provenance | Stakeholder framework institution list |

Canonical organisation name. All accounts belonging to one organisation carry the identical string, enabling institution-level aggregation.

**Critical rule:** where the actor is an individual citizen, this field records `Citizen` — **never the personal name**. The `author` field preserves the name for traceability; duplicating it into an analytical dimension would propagate identifiable data through every aggregation.

---

## 1.3 `platform`

| Attribute | Value |
|---|---|
| Status | **Observed** |
| Type | enum |
| Cardinality | 1 |
| Nullable | No |
| Reproducibility | 100% |

Carried unchanged from the unified dataset. Requires no interpretation.

---

## 1.4 `posting_pattern`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | enum |
| Cardinality | 1 |
| Nullable | No |
| Reproducibility | 100% — must be exactly recomputable |

**Derivation:** posts by this author (institution-level for organisational actors, account-level for individuals) ÷ span in weeks between their first and last post in the dataset.

| Value | Threshold |
|---|---|
| `frequent` | ≥ 3.0 posts/week |
| `regular` | 1.0 – 2.99 |
| `occasional` | 0.25 – 0.99 |
| `rare` | < 0.25, more than one post |
| `single_post` | Exactly one post |

**Prerequisite:** requires author-level aggregation across the whole dataset, computed before batching. It cannot be derived within a batch.

---

## 1.5 `possible_age_cue`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic, **uncertain** |
| Type | enum |
| Cardinality | 1 |
| Nullable | No — `unclear` is the expected majority value |
| Companions | `age_confidence`, `age_justification` |
| Provenance | Age brackets from the perception survey, used as contextual reference |

**Permitted values:** `<15` · `16-24` · `25-34` · `35-44` · `45-60` · `60-70` · `70+` · `unclear` · `not_applicable_institutional`

**Admissible evidence, descending strength**

| Rank | Evidence |
|---|---|
| a | Explicit self-statement of age or age group |
| b | Explicit life-stage self-description tied to an age range |
| c | Generational self-identification |
| d | Strongly age-associated language register — **corroboration only, never alone** |

**Inadmissible:** platform choice, topic of interest, writing quality, emoji use, inference from the person's name.

**Expected outcome:** `unclear` for the large majority of records. A low assignment rate is the correct result, not under-performance. Any downstream age distribution must state the proportion of records where a cue was assignable, since a distribution over a small assigned subset is not a distribution over the corpus.

---

## 1.6 `age_confidence`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum |
| Permitted values | `high` · `medium` · `low` |

`high` for explicit self-statement; `medium` for clear life-stage or generational cue; `low` for register-based corroboration. Where the value is `unclear` or `not_applicable_institutional`, confidence is `low`.

**Semantics:** strength of available evidence. **Not** a statistical probability of correctness.

---

## 1.7 `age_justification`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | text |
| Nullable | No — must be non-empty whenever a cue is assigned |

Must quote or closely describe the **specific textual evidence**. A justification reading "language style suggests a younger user" without identifying the language is **invalid** and fails validation check V2.

---

## 1.8 `possible_gender_cue`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic, **uncertain** |
| Type | enum |
| Companions | `gender_confidence`, `gender_justification` |

**Permitted values:** `possible_male` · `possible_female` · `unclear` · `not_applicable_institutional`

The `possible_` prefix is mandatory and reflects genuine uncertainty. Name-based inference is culturally dependent and error-prone; it is retained only because aggregate participation patterns are analytically relevant.

**Admissible evidence:** explicit self-identification; grammatically gendered self-reference in a gendered language; a display name that is a strongly gender-associated given name in the relevant cultural context.

**Inadmissible:** profile imagery, topic, tone, writing style.

Institutional accounts always take `not_applicable_institutional`. This is enforced by coherence Test 5.

---

## 1.9 `gender_confidence`

`high` · `medium` · `low`. **Name-based inference is never higher than `medium`.**

---

## 1.10 `gender_justification`

Text. Records the name pattern or grammatical evidence used. Non-empty whenever a cue is assigned.

---

## 1.11 `live_in_live_out_status`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic, **uncertain** |
| Type | enum |
| Cardinality | 1 |
| Companions | `live_status_confidence`, `live_status_justification` |

**Permitted values and decision rules**

| Value | Assigned when |
|---|---|
| `likely_local_resident` | Explicit residence statement, or possessive belonging language plus a local place reference |
| `possibly_local` | Local place references and sustained local posting, without an explicit residence claim |
| `works_in_area` | Explicit statement of working locally without residing |
| `visitor` | Explicit statement of visiting |
| `external_observer` | Commenting with no local ties evidenced |
| `external_partner` | Institutional or professional actor engaged from outside |
| `unclear` | Default |

**Evidence combines:** the place field, spatial references in the body, possessive or belonging language, `posting_pattern`, and `stakeholder_category`.

**Hard constraint:** institutional accounts are never `likely_local_resident` or `possibly_local`. An organisation does not reside. Enforced by coherence Test 5.

---

## 1.12 `live_status_confidence`

`high` · `medium` · `low`, from the strength of spatial, textual, and actor-related evidence.

---

## 1.13 `live_status_justification`

Text identifying which evidence types were present — possessive language, geotags, place references, posting pattern, stakeholder information.

---

## 1.14 `profile_confidence`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum: `high` · `medium` · `low` |
| Derivation | Modal confidence across assigned inferred fields; where fields disagree, the lower |

**Constraint:** never higher than the highest individual field confidence. Enforced by coherence Test 12.

---

## 1.15 `profile_review_flag`

| Attribute | Value |
|---|---|
| Status | Inferred-structural / control |
| Type | boolean-as-enum: `yes` · `no` |

**Set to `yes` when any of:** `stakeholder_category` rests on bio or content rather than name; category is `Unclear`; any demographic cue assigned at low confidence; residence status conflicts with stakeholder category; the record contains information that could identify a private individual beyond the public author name.

**A flagged and unreviewed record must not contribute to a reported finding.**
