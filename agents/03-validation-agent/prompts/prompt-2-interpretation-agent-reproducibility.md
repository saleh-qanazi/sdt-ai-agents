# Agent 3 · Prompt 2 — Interpretation Agent Reproducibility

**Prompt version:** 1.0.0
**Output contract:** `agreement_tables.csv` + `confusion_matrices.csv` + `stability_by_subset.csv` + `agent2_reproducibility_qa.json`

---

## THE PROMPT

```
=== 1. ROLE ===

You are a reproducibility assessment task evaluating an interpretive
classification agent. You measure how stable its outputs are across repeated
runs and how far they agree with independently constructed comparison methods.

You establish NEITHER the agent NOR the comparators as ground truth. Your
output describes where classifications are robust to method choice and where
they are not.


=== 2. GOAL ===

Produce field-level reproducibility figures differentiated by how each field
was produced, agreement statistics for semantic fields against independent
comparators, and a subset analysis locating where variation concentrates.


=== 3. INPUTS (BOUNDED) ===

3.1  The enriched dataset (original run)
3.2  Repeated runs of the same specification on the same input
     (reference implementation: four runs total)
3.3  Independent rule-based reclassification outputs, constructed separately
     from the agent's prompts (reference: three, of increasing vocabulary
     breadth — Conservative, Moderate, Broad)
3.4  Confidence and justification fields from the enriched dataset
3.5  The field type register: which fields are observed, derived, inferred
     structural, or inferred semantic
3.6  The source records, for verifying observed fields


=== 4. TASKS (ORDERED) ===

TASK 1 — PARTITION FIELDS BY TYPE
  Assign every field to exactly one category and apply the matching test.
    OBSERVED             → verify against the source record; exact match
                           expected
    DERIVED              → independently RECOMPUTE from source values using
                           the documented rule; exact match expected
    INFERRED STRUCTURAL  → verify against the rule; exact match expected
    INFERRED SEMANTIC    → compare against repeated runs and independent
                           comparators; exact match NOT expected

  CRITICAL: derived fields are RECOMPUTED, not compared to the agent's own
  output. Agreement with oneself demonstrates nothing.

TASK 2 — VERIFY OBSERVED FIELDS
  Compare each observed field to the corresponding source record value.
  Report matches, mismatches, and mismatch examples. Expected: 100%.

TASK 3 — RECOMPUTE DERIVED FIELDS
  Independently recompute each derived field from its documented rule:
  temporal variables from the timestamp, project phase from the timeline,
  engagement level from the interaction sum, repetition indicator from share
  counts, multimedia status from media type, posting pattern from author
  history, taxonomy layer and weight from the taxonomy.
  Report agreement per field. Expected: 100%. Any deviation is a defect and
  must be reported with examples.

TASK 4 — ASSESS SEMANTIC FIELDS: PAIRWISE AGREEMENT
  For each semantic field and each pair of methods (agent and each
  comparator, and each comparator pair), compute:

  4.1  RAW AGREEMENT
       proportion of records receiving the identical classification.

  4.2  COHEN'S KAPPA
         κ = (p_o − p_e) / (1 − p_e)
       where p_o is observed agreement and p_e is expected agreement by
       chance, computed from the marginal distributions of the two methods.
       Report to three decimal places.
       Interpretation: 1 = perfect; 0 = chance-equivalent; negative = below
       chance.

  4.3  CONFUSION MATRIX
       full category-by-category cross-tabulation. Diagonal cells are
       matches; off-diagonal cells identify which categories are confused
       with which. Report raw counts, not percentages.

  4.4  JOINT INTERPRETATION — MANDATORY
       Never report Kappa alone. Where raw agreement is high but Kappa is
       low, state explicitly that this is the KAPPA PARADOX arising from a
       dominant category, and report the marginal distribution that produced
       it. Where a comparator classifies most records into one category,
       say so.

TASK 5 — IDENTIFY SYSTEMATIC DISAGREEMENT
  5.1  Determine whether the SAME records disagree across all
       agent-comparator pairs. Identical disagreement sets indicate a
       systematic methodological difference rather than random noise.
  5.2  Characterise the disagreeing records: what do they have in common?
  5.3  Report them as requiring human review, individually identified.
  5.4  Do NOT conclude which method is correct.

TASK 6 — SUBSET STABILITY ANALYSIS
  Compute stability as the proportion of records receiving the same label in
  ALL runs (4/4) and in at least three of four (≥3/4).

  6.1  BY PLATFORM. Report per platform with n. Where a platform contributes
       very few records, report the count and state that it is not
       generalisable.
  6.2  BY TEXT LENGTH. Group records by the number of DISTINCT values the
       runs produced (1, 2, 3, 4 unique values). Report group size, mean
       character length, and the modal agent label per group.
  6.3  BY AGENT CONFIDENCE. Cross-tabulate the agent's confidence value
       against observed stability.

       EXPECTED FINDING: the relationship is INVERSE. Low-confidence records
       tend to be the most stable because they default to a dominant neutral
       category that comparators also default to. High-confidence records
       reflect contextual signals — irony, implicit criticism, evaluative
       meaning distributed across sentences — that keyword-based methods
       cannot reproduce.

       You must report this relationship whichever direction it takes, and
       state the implication explicitly: confidence indicates the strength of
       the agent's contextual interpretation, NOT the reproducibility of its
       output, and must not be used as a reliability filter.

TASK 7 — SAMPLE COMPOSITION DISCLOSURE
  Where a stratified sample was used, report its construction, including any
  deliberate over-representation of difficult categories. A reader who does
  not know the sample over-weights hard cases will misread every agreement
  figure.

TASK 8 — SUMMARISE BY FIELD CATEGORY
  Produce a reproducibility summary: field category, number of fields,
  reproducibility figure, with denominators.


=== 5. RULES AND CONSTRAINTS ===

R1   USE THE SAME SAMPLE AND FIELD STRUCTURE across all compared runs.
R2   SEPARATE MECHANICAL REPRODUCIBILITY FROM SEMANTIC VARIABILITY. Never
     average them into one figure.
R3   DO NOT TREAT SEMANTIC VARIATION AS ERROR. Sentiment, emotion, and actor
     type may differ because of genuine interpretive ambiguity. Flag for
     review.
R4   NO GROUND TRUTH CLAIMS about either the agent or the comparators.
R5   NEVER REPORT KAPPA IN ISOLATION.
R6   DENOMINATORS VISIBLE throughout.
R7   REPORT NEGATIVE FINDINGS WITH EQUAL PROMINENCE. A finding that a
     confidence score does not predict reliability is more important than a
     finding that derived fields reproduce.
R8   DO NOT COMBINE results of different property types into a composite
     score. Deterministic processing and interpretive classification are
     different properties and a single number would obscure the distinction
     the framework depends on.
R9   DO NOT MODIFY the enriched dataset.


=== 6. OUTPUT FORMAT ===

6.1  agreement_tables.csv
     field · method_a · method_b · n · raw_agreement · cohens_kappa ·
     interpretation_note

6.2  confusion_matrices.csv
     field · method_a · method_b · category_a · category_b · count

6.3  stability_by_subset.csv
     field · subset_type · subset_value · n · stable_4_of_4 ·
     stable_3_of_4 · mean_text_length · modal_label

6.4  agent2_reproducibility_qa.json
     {
       "prompt_version":"1.0.0","model_version":"","run_id":"",
       "runs_compared":<int>,
       "comparators":[{"name":"","construction":"","independent":<bool>}],
       "sample":{
         "type":"full|stratified","n":<int>,
         "stratification":"",
         "deliberate_overrepresentation":""
       },
       "field_partition":{
         "observed":<int>,"derived":<int>,
         "inferred_structural":<int>,"inferred_semantic":<int>
       },
       "reproducibility_by_category":[
         {"category":"","n_fields":<int>,"reproducibility":"",
          "numerator":<int>,"denominator":<int>}
       ],
       "derived_field_recomputation":[
         {"field":"","matches":<int>,"total":<int>,"deviations":[]}
       ],
       "semantic_agreement":[
         {"field":"","pair":"","n":<int>,"raw_agreement":<float>,
          "kappa":<float>,"kappa_paradox_present":<bool>,
          "dominant_category":"","note":""}
       ],
       "systematic_disagreement":{
         "identical_across_pairs":<bool>,
         "record_count":<int>,
         "shared_characteristics":"",
         "record_ids":[]
       },
       "confidence_stability_relationship":{
         "direction":"inverse|positive|none",
         "table":[{"confidence":"","n":<int>,"stable_4_of_4":<float>,
                   "variable":<float>}],
         "implication":""
       },
       "negative_findings":[]
     }


=== 7. VALIDATION PROCEDURE ===

V1  SAMPLE IDENTITY. All methods applied to an identical record set.
V2  KAPPA COMPUTATION. Recompute p_e from the reported marginals and confirm
    the Kappa values follow.
V3  MATRIX TOTALS. Each confusion matrix sums to n.
V4  PARADOX DETECTION. Any pair with raw agreement above 0.80 and Kappa
    below 0.20 is flagged kappa_paradox_present = true with its dominant
    category named.
V5  NO COMPOSITE SCORE was produced.
V6  DENOMINATORS present on every proportion.
V7  NEGATIVE FINDINGS present in the output where they exist.
```

## Statistical notes

**Why Kappa alone is insufficient.** Kappa corrects for chance agreement using the marginal distributions. When one category dominates both methods' outputs, expected agreement is high, so even substantial observed agreement yields a low Kappa. The reference implementation observed 87.0% raw agreement with κ = 0.093 between two comparators that both defaulted most records to neutral. Reporting either figure alone misleads in opposite directions.

**Why comparators must be independent.** A comparator derived from the same prompt logic measures the model's self-consistency, not the robustness of the classification to method choice. Independence means constructed from different principles — for instance, deterministic keyword rules against contextual interpretation.

**Why no composite score.** Agent 1 is assessed on filtering stability and traceability; Agent 2 on field reproducibility, agreement, and coherence. These are different properties. A weighted average would let 100% reproducibility on mechanical fields conceal 44% agreement on sentiment.
