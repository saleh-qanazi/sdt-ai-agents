# 3. Reproducibility

**Read this before running anything in this repository.**

## 3.1 Reproducibility is not determinism

The underlying language model is probabilistic. Identical inputs will not always produce byte-identical outputs. Any claim to the contrary would be false, and any method built on the assumption would be fragile.

What is reproducible here is the **procedure**. The specification is itself the method, and it is documented in full, making it possible to examine exactly how each task was defined and to repeat it.

Four published properties support this:

| Property | What it guarantees |
|---|---|
| **Documented specification** | The exact definition of every task can be inspected and criticised |
| **Fixed output schema** | Results from different runs are structurally comparable |
| **Raw-to-output traceability** | Every processed record can be traced to its original source |
| **Measured variance** | Output variability is tested empirically, not assumed |

The fourth point is what distinguishes this from an unfalsifiable claim. Variance is not waved away; it is measured, reported, and used to determine how much weight each field can bear.

## 3.2 What reproduces, and what does not

Fields in the enriched output fall into four categories with sharply different reproducibility profiles.

| Field category | How produced | Reproducibility |
|---|---|---|
| **Observed** | Transferred directly from the source record | 100% |
| **Derived** | Computed mechanically by fixed rule from existing fields | 100% |
| **Inferred — structural** | Rule-based inference within a controlled vocabulary | 100% |
| **Inferred — semantic** | Contextual interpretation of meaning | 44.3%–86.3% (see below) |

This is the central finding of the validation campaign and it should govern how any downstream result is read. **The enriched layer is highly reproducible for factual and structural attributes; semantic outputs require cautious interpretation.**

## 3.3 Measured variance in the reference implementation

Two testing campaigns were conducted: five iterations assessing Agent 1's relevance filtering across the raw corpus, and four repeated runs evaluating Agent 2 across all records. Thirty-four technical checks were performed in total.

### Agent 1 — filtering stability

| What was measured | Result |
|---|---|
| Deduplication reliability (stable identification using content keys) | 487 / 487 stable |
| Precision convergence (false positives removed until stable) | 41 removed; stable at 769 records |
| Keyword anchoring (records linked to core project keyword variants) | 94.9% |

The progressively smaller number of corrections across iterations indicates convergence toward a stable relevant dataset. The high proportion of records anchored to core project keywords limits the influence of weak contextual matches.

### Agent 2 — classification reproducibility

Nine derived fields — temporal variables, project phase, engagement measures, multimedia status, repetition indicators — were independently recomputed across all records. All matched the original outputs with no deviations.

Semantic fields were compared against three independently constructed rule-based reclassification methods (Conservative, Moderate, Broad), using raw agreement, Cohen's Kappa, and confusion matrices.

**Stakeholder classification** (n = 131 stratified sample):

| Pair | Raw agreement | κ |
|---|---|---|
| Agent ↔ Conservative | 86.3% | 0.227 |
| Agent ↔ Moderate | 86.3% | 0.227 |
| Agent ↔ Broad | 86.3% | 0.227 |
| Conservative ↔ Moderate | 100.0% | 1.000 |
| Conservative ↔ Broad | 100.0% | 1.000 |
| Moderate ↔ Broad | 100.0% | 1.000 |

The same 18 records caused disagreement in every agent–comparator comparison. Most involved institutional, media, or organisational actors whose roles were clearer from profile descriptions or post content than from account names. This is a systematic difference between the agent's context-based approach and the comparators' name-based rules — it does not establish that either is correct. The low Kappa indicates limited agreement beyond chance, and those 18 records require human review.

**Sentiment classification** (n = 131):

| Pair | Raw agreement | κ |
|---|---|---|
| Agent ↔ Conservative | 44.3% | 0.004 |
| Agent ↔ Moderate | 48.1% | 0.130 |
| Agent ↔ Broad | 58.8% | 0.397 |
| Conservative ↔ Moderate | 87.0% | 0.093 |
| Conservative ↔ Broad | 55.7% | 0.020 |
| Moderate ↔ Broad | 64.9% | 0.272 |

Agreement rises as the comparator incorporates broader vocabulary and more contextual rules. The Conservative ↔ Moderate pair illustrates why raw agreement and Kappa must be read together: 87.0% agreement with κ = 0.093, because both methods classified most posts as neutral. This class imbalance produces high apparent agreement without consistency across categories — the Kappa paradox.

Disagreement concentrates in negative and mixed classifications, which depend on tone, contradictory signals, implied meaning, and wider context. Positive sentiment reproduces more consistently.

**Neither the agent nor the rule-based methods constitute ground truth.** The comparison establishes where classifications are robust to method choice and where they are not.

### Stability across subsets

| Platform (N) | Sentiment 4/4 | Sentiment ≥3/4 | Stakeholder 4/4 | Stakeholder ≥3/4 |
|---|---|---|---|---|
| Instagram (66) | 51.5% | 71.2% | 93.9% | 100% |
| X/Twitter (62) | 22.6% | 59.7% | 77.4% | 100% |
| TikTok (3) | 66.7% | 66.7% | 100% | 100% |

The explanation is contextual rather than technical. Longer captions generally supply enough context for consistent interpretation; shorter, more fragmented posts depend on information not contained within the post. Stakeholder classification reaches full agreement at the ≥3/4 threshold on every platform, meaning disagreement is confined to a single dissenting run rather than being genuinely contested. The three TikTok records are noted but not generalisable.

**By text length:**

| Stability group | N | Mean length | Dominant agent label |
|---|---|---|---|
| All 4 runs agree | 50 | 181 characters | 100% neutral |
| 2 unique values | 54 | 254 characters | mixed distribution |
| 3 unique values | 26 | 607 characters | predominantly mixed |
| 4 unique values | 1 | 288 characters | edge case |

Longer texts more often combine positive and negative views, which different methods interpret differently. The apparent stability of short posts should be treated cautiously: it may reflect neutral being used as a default where sentiment cues are limited, rather than genuine agreement.

### Confidence does not predict reproducibility

| Confidence | N | Stable (4/4) | Variable (≥3 distinct values) |
|---|---|---|---|
| Low | 48 | 91.7% | 0.0% |
| Medium | 77 | 7.8% | 29.9% |
| High | 6 | 0.0% | 66.7% |

The relationship is **inverse**. Low-confidence records were most stable because they were typically neutral with limited sentiment evidence, and neutral was also the rule-based methods' default. High-confidence records reflected the agent identifying contextual signals — irony, implicit criticism, evaluative meaning spread across sentences — which keyword-based methods cannot reproduce.

**Confidence indicates the strength of the agent's contextual interpretation, not the reproducibility of its classification.** It must not be used as a reliability filter.

### Cross-dimension coherence

Nine tests, each pairing two fields whose values are logically constrained by one another, run across the full dataset:

| Test | Result |
|---|---|
| Sentiment ↔ problem coherence | 0 / 216 violations |
| Positive sentiment → positive emotion | 147 / 147 |
| Negative sentiment → negative emotion | 155 / 155 |
| Mixed emotion ↔ mixed sentiment | 64 / 64 |
| Institutional ↔ not local resident | 0 violations |
| Engagement level ↔ total engagement | 753 / 753 |
| Time-of-day ↔ timestamp hour | 753 / 753 |
| Project phase ↔ timestamp year | 753 / 753 |
| Multimedia flag ↔ media type | 0 contradictions |

All nine passed. This does not confirm that any individual semantic classification is objectively correct — an incorrect interpretation can still be internally consistent. It demonstrates that the enriched fields follow a coherent classification logic, so relationships observed between dimensions reflect consistent links rather than inconsistencies introduced during processing.

## 3.4 Record-count lineage

Reproducibility requires that every change in record count be attributable. The reference implementation's lineage, as established by the validation campaign:

```
  Raw capture                              5,177 records, 3 platforms, 60 queries
        │
        ▼  deduplication (487 removed)
  Unique records                           4,690
        │
        ▼  commercial-content filter, geographic disambiguation
        ▼  keyword-based relevance filtering
  Baseline relevant set                    796–810  (varies by run)
        │
        ▼  four iterative precision tests (41 false positives removed)
  Post-precision-testing set               769
        │
        ▼  human revision by researcher (16 removed)
  Final validated corpus                   753
        │
        ▼  social interpretation
  Enriched social layer                    753 records × 70 fields
```

The variation in the baseline relevant set (796–810 across runs) is itself a reported reproducibility finding, not an inconsistency to be hidden. The earlier totals represent successive pre-revision stages; **753 is the authoritative figure** carried into analysis.

Note that a separate, earlier processing pass reported deduplication figures of 358 removed / 4,819 unique, using a different deduplication key. The 487 / 4,690 figures above come from the validated pass using content-based keys and supersede them. Anyone reproducing this pipeline should use content-based deduplication (see §3.5).

## 3.5 Known technical constraints

**Numerical identifier precision.** Platform-native numeric identifiers (`tweet_id`, `video_id`) stored as floating-point values in CSV exports lose precision and produce collisions. Deduplication must therefore use content-based keys: reliable string shortcodes where available, and normalised text otherwise. This is effective for corpora of public posts but is less suitable for datasets containing legitimate identical or near-identical posts.

**Timestamp heterogeneity.** Platform exports use incompatible timestamp formats. Two patterns recur: a near-standard format with a trailing textual suffix that breaks direct parsing, and a platform-native format that is neither human-readable nor interoperable with standard datetime libraries. Publication timestamps must also be distinguished from collection timestamps. Normalise to a single documented format, retain the raw field alongside the derived one, and report parsing errors.

**Model dependence.** Even with identical specification and prompts, interpretive outputs may vary between executions and between model versions. Prompt versions, model information, confidence scores, justification notes, and review flags are retained as part of the traceability record precisely so this variation remains attributable.

**Execution cost and scale.** API-based processing introduces time, cost, and scalability constraints that grow with corpus size and contextual material. Manage through small-sample testing, batching, and budget controls.

## 3.6 How to reproduce a run

1. Fix the input dataset and record its version.
2. Select the prompt version from this repository and record it.
3. Record the model identifier and version.
4. Record batch size and processing scope.
5. Execute on a reduced sample first (10–25 records). Inspect the output against the validation procedure in the agent's `validation.md`.
6. Execute at full scope only once the sample output is structurally correct.
7. Run the validation agent procedures in [`agents/03-validation-agent/`](../agents/03-validation-agent/) — including at least three repeated runs — and report measured agreement.
8. Publish the resulting variance figures alongside any substantive finding.

Step 8 is not optional. A result from this pipeline reported without its measured variance is not a reproducible result.

## 3.7 Consolidated limitations

**Filtering.** Relevance filtering produces both false positives and false negatives. Implicit references to the study area may be missed. The rules deliberately avoid unsupported assumptions, which trades recall for precision.

**Platform asymmetry.** The filtering strategy depends on textual evidence. It performs adequately where sufficient textual or location information exists and poorly on video-oriented platforms, where meaning is carried by speech, imagery, or on-screen text absent from the available metadata. A very low relevance rate on such a platform reflects a limitation of text-based filtering, **not** evidence that relevant content is absent.

**Cleaning.** Removing extraneous elements can also remove context. Original records are preserved alongside processed data so source content can be revisited.

**Interpretive ceiling.** Short, ambiguous, ironic, or strongly context-dependent posts resist confident classification. Structured interpretation reduces but cannot eliminate this.

**Spatial precision.** Explicit location metadata is not available for all records; some assignments rest on textual or contextual evidence. These must not be treated as equivalent in precision to explicitly geotagged records.

**Profile inference.** Demographic cues and residence status are limited to the evidence in the record and are not verified personal characteristics.

**Source-data ceilings.** Location metadata is incomplete, engagement measures are not directly comparable across platforms, and not all records can be linked to precise spatial objects.

The validation does not establish that this pipeline is error-free. It clarifies where cautious interpretation and researcher verification remain necessary.
