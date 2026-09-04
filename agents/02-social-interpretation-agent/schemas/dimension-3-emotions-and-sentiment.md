# Dimension 3 — Emotions and Sentiment

**11 fields. Question addressed: how.**

Distinguishes general sentiment polarity from specific emotional signals, consistent with affective text analysis where polarity and emotion categories are related but distinct outputs.

> **This dimension contains the least reproducible field in the pipeline.** `sentiment_class` showed **44.3%–58.8% raw agreement** (Cohen's κ 0.004–0.397) against independent comparators. Every claim resting on it must be reported with its measured variance.

---

## 3.1 `sentiment_class`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum |
| Cardinality | 1 |
| Nullable | No |
| Companions | `sentiment_confidence`, `sentiment_justification` |
| Provenance | Four-way polarity structure established in sentiment-analysis tasks, where the fourth category is commonly labelled *conflict* |
| Reproducibility | **44.3%–58.8%** depending on comparator |

**Permitted values**

| Value | Definition |
|---|---|
| `positive` | Approval, satisfaction, pleasure, appreciation, hope, pride |
| `neutral` | Information without evaluative orientation: announcements, factual statements, unframed questions |
| `negative` | Disapproval, dissatisfaction, complaint, anger, worry, disappointment |
| `mixed` | Positive **and** negative evaluations both meaningfully expressed |

**Evidence:** tone, word choice, emoji, expressive punctuation, hashtags, and the polarity values of matched taxonomy terms.

**Governing rules**

| Rule | Statement |
|---|---|
| Problems ≠ negative | Where problems were identified, their presence contributes negative evidence, but classification rests on the expression in the record. A neutral factual report of a problem is neutral |
| Irony | Classify the **intent**; record the divergence in the justification; confidence ≤ `medium`; set review flag |
| Quoted speech | Classify the author's stance toward the quoted material, not the material's own sentiment |
| Mixed threshold | Both orientations meaningfully present, not a single hedge softening a one-sided evaluation |

**Where disagreement concentrates:** negative and mixed classifications, which depend on tone, contradictory signals, implied meaning, and wider context. Positive sentiment reproduces relatively consistently. Variation is greater for longer posts and for platforms with shorter, more fragmented content.

---

## 3.2 `sentiment_confidence`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum: `high` · `medium` · `low` |

| Value | Assigned when |
|---|---|
| `high` | Unambiguous evaluative language, consistent across the record |
| `medium` | Orientation clear but indirect, or the record is short, or irony is involved |
| `low` | Orientation inferred from limited or conflicting cues |

> **Do not use this field as a reliability filter.** Validation established an **inverse** relationship with cross-method stability:
>
> | Confidence | n | Stable 4/4 | Variable (≥3 distinct values) |
> |---|---|---|---|
> | Low | 48 | 91.7% | 0.0% |
> | Medium | 77 | 7.8% | 29.9% |
> | High | 6 | 0.0% | 66.7% |
>
> Low-confidence records were most stable because they defaulted to neutral, which rule-based comparators also default to. High-confidence records reflect contextual signals — irony, implicit criticism, evaluative meaning spread across sentences — that keyword methods cannot reproduce. **Confidence indicates the strength of contextual interpretation, not the reproducibility of the classification.**

---

## 3.3 `sentiment_justification`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | text |
| Nullable | No |

Records the specific positive, negative, neutral, or conflicting markers identified and, where relevant, their relationship to detected problems. Must quote or closely describe the markers. A justification restating the class without naming markers is **invalid**.

---

## 3.4 `emotional_signal`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum |
| Cardinality | 1 |
| Provenance | Established affective categories combined with place-related expressions from environmental-psychology research on person–place affective bonds |

**Permitted values**

| Family | Values |
|---|---|
| Positive | `pride/attachment` · `hope` · `gratitude/satisfaction` · `joy/celebration` · `general_positive` |
| Negative | `fear/insecurity` · `frustration/exasperation` · `anger/indignation` · `sadness/disappointment` · `concern/anxiety` · `neglect/abandonment` · `disgust/degradation` · `general_negative` |
| Other | `mixed` · `none` |

| Value | Definition |
|---|---|
| `pride/attachment` | Expressed belonging, fondness for the place, identification with the neighbourhood |
| `hope` | Expectation of improvement, optimism about change |
| `gratitude/satisfaction` | Thanks, contentment with something delivered |
| `joy/celebration` | Pleasure, festivity, enthusiasm |
| `fear/insecurity` | Feeling unsafe, apprehension about danger |
| `frustration/exasperation` | Impatience, weariness at repeated problems |
| `anger/indignation` | Outrage, protest, moral condemnation |
| `sadness/disappointment` | Regret, letdown, loss |
| `concern/anxiety` | Worry about a future or ongoing situation |
| `neglect/abandonment` | Feeling forgotten, overlooked, deprioritised |
| `disgust/degradation` | Revulsion at conditions |
| `general_positive` / `general_negative` | Affect present but not specifically identifiable |
| `mixed` | Several conflicting signals meaningfully present |
| `none` | No affective signal expressed |

**Coherence constraint:** must be consistent with `sentiment_class` at the family level. Enforced by coherence Tests 2, 3, and 4.

---

## 3.5 `total_engagement`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | integer |
| Nullable | No |
| Derivation | `like_count + comment_count + share_count + quote_count`, summing only available fields |
| Reproducibility | 100% |

> **Absent measures are absent, not zero.** Where a platform does not export a measure, it is excluded from the sum and recorded in `engagement_fields_available`. Zero-filling fabricates data and biases every engagement statistic.

---

## 3.6 `engagement_fields_available`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | string[] |

Which interaction measures were included in the sum for this record. Required for the sum to be independently verifiable and for cross-platform comparison to be caveated correctly.

---

## 3.7 `engagement_level`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | enum |
| Reproducibility | 100% |

| Value | Threshold |
|---|---|
| `viral` | 500+ |
| `very_high` | 100–499 |
| `high` | 50–99 |
| `moderate` | 20–49 |
| `low` | 5–19 |
| `minimal` | 1–4 |
| `no_engagement` | 0 |

> **Study-specific operational intervals.** Likes, comments, and shares are widely used as engagement measures, but there is no established agreement on thresholds separating low, high, or viral engagement. Anyone adapting this schema should set thresholds appropriate to their corpus and state them.

---

## 3.8 `engagement_sentiment_proxy`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | string |
| Derivation | `engagement_level` + `_` + `sentiment_class`, e.g. `high_negative` |

A convenience label permitting examination of high-attention records alongside expressed polarity. **Not a new measure.** The underlying variables remain separate and must be analysed separately.

---

## 3.9 `sentiment_review_flag`

`yes` · `no`. Set `yes` where: irony was involved; the record is under 30 characters; sentiment rests on emoji alone; the record is multilingual; the classification is `mixed`; or confidence is `low`.

---

## 3.10 `emotion_review_flag`

`yes` · `no`. Set `yes` where the emotional signal is `mixed`, or where the signal was assigned from a single ambiguous marker.

---

## 3.11 `sentiment_prompt_version`

Provenance. String.

---

## Reporting requirements for this dimension

Any finding resting on `sentiment_class` must state:

1. The measured agreement range against independent comparators.
2. That disagreement concentrates in negative and mixed categories.
3. The proportion of records classified `neutral`, since a dominant category inflates raw agreement while suppressing Kappa.
4. That confidence is not a reliability indicator.

Reporting a sentiment distribution without these caveats overstates what the method supports.
