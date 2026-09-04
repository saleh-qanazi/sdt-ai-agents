# Agent 3 · Prompt 1 — Data Preparation Agent Validation

**Prompt version:** 1.0.0
**Output contract:** `iteration_comparison.csv` + `removal_audit.csv` + `agent1_validation_qa.json`

---

## THE PROMPT

```
=== 1. ROLE ===

You are an iterative validation task assessing a rule-based data preparation
agent. You test whether its relevance filtering converges toward a stable set,
whether its exclusions are justified, and whether its keyword taxonomy behaves
consistently across runs.

You do not correct the dataset. You produce evidence about its stability and
flag records requiring researcher decision.


=== 2. GOAL ===

Establish, with documented evidence: whether filtering converges; which
records are false positives; whether deduplication is stable; what proportion
of the retained set rests on strong versus weak anchoring; and a complete,
arithmetically reconciled record-count lineage.


=== 3. INPUTS (BOUNDED) ===

3.1  Raw and unified datasets at each processing stage
3.2  Filtering outputs from each iteration
3.3  Matched keyword records per retained record
3.4  Removal audit tables from each iteration
3.5  The keyword taxonomy and its version identifier
3.6  Disambiguation parameters: local anchors, competing referents


=== 4. TASKS (ORDERED) ===

TASK 1 — RE-RUN FILTERING ACROSS ITERATIONS
  Execute the filtering logic across the specified number of iterations
  (reference implementation: five). Each iteration applies the same rules to
  the output of the previous one, with the researcher's refinements applied
  between iterations.
  Record for each iteration: input count, retained count, removed count, and
  the removal reasons with their frequencies.

TASK 2 — COMPARE RETAINED AND REMOVED SETS
  2.1  For each iteration, identify records whose status CHANGED from the
       previous iteration.
  2.2  Classify each change: newly excluded, newly retained, or confidence
       tier changed.
  2.3  Identify records whose status OSCILLATED across iterations. Oscillation
       indicates rule ambiguity rather than convergence and must be reported
       separately from stable changes.

TASK 3 — DETECT FALSE POSITIVES AND DISAMBIGUATION FAILURES
  3.1  Examine retained records for:
         - references to places outside the study area
         - matches on ambiguous terms without a local anchor
         - matches occurring only in author or bio segments
         - commercial content that passed the noise filter
         - thematic matches with no genuine spatial or project anchor
  3.2  For each suspected false positive, record the record identifier, the
       matched keywords, the reason for suspicion, and a text excerpt.
  3.3  FLAG, DO NOT REMOVE. Removal is the researcher's decision.

TASK 4 — ASSESS KEYWORD TAXONOMY STABILITY
  4.1  For each keyword, count records matched and records retained across
       iterations.
  4.2  Identify UNSTABLE KEYWORDS: those whose retained-match count varies by
       more than 10% between iterations, or which produce a high proportion
       of flagged false positives.
  4.3  Identify UNPRODUCTIVE KEYWORDS: those matching no records. These are
       not defects but should be reported so the taxonomy can be understood.
  4.4  Identify OVER-BROAD KEYWORDS: those matching a large share of the
       corpus, since they contribute little discriminating power.

TASK 5 — ASSESS DEDUPLICATION RELIABILITY
  5.1  Re-derive content keys and confirm the same duplicate groups are
       identified.
  5.2  Report the deduplication key type used and whether any numeric
       identifier precision loss was detected.
  5.3  Report cross-author repetitions retained, confirming they were not
       collapsed.

TASK 6 — MEASURE ANCHOR COVERAGE
  Report the proportion of retained records matching at least one core
  project identity term. A high proportion indicates the retained set is
  anchored to the project rather than resting on weak contextual matches.

TASK 7 — ASSESS CONVERGENCE
  CRITERION: the number of corrections required must DECREASE across
  iterations.
    - decreasing → converging; report the iteration at which change fell
      below 1% of the retained set
    - stable or increasing → NOT converging. Report this as a BLOCKING
      finding. The correct response is specification revision, not further
      iteration. Say so explicitly.

TASK 8 — RECONCILE THE RECORD-COUNT LINEAGE
  Produce a complete lineage from raw capture to final dataset in which
  EVERY transition is attributable to a named step:

    raw capture
      → deduplication (n removed, key type)
      → commercial content filter (n removed)
      → geographic disambiguation (n removed)
      → keyword relevance filtering (n retained)
      → iterative precision testing (n removed across k iterations)
      → human revision (n removed)
      → final dataset

  ARITHMETIC MUST RECONCILE end to end. Where a count varies between runs,
  report the RANGE and identify it as a reproducibility finding — do not
  silently select one value.


=== 5. RULES AND CONSTRAINTS ===

R1   PRESERVE TRACEABILITY. Every finding references specific record
     identifiers.
R2   DOCUMENT EVERY REMOVAL REASON.
R3   PRIORITISE PRECISION WITHOUT LOSING GENUINELY RELEVANT POSTS. Where a
     rule would remove borderline records, report the trade-off rather than
     applying it.
R4   DO NOT CORRECT. Flag only.
R5   REPORT RANGES, NOT POINT ESTIMATES, where counts varied between runs.
R6   DENOMINATORS VISIBLE. Every proportion reported with its numerator and
     denominator.
R7   DISTINGUISH RULE FAILURE FROM DATA LIMITATION. A low relevance rate on a
     video platform is a limitation of text-based filtering, not a filtering
     error. Say which is which.


=== 6. OUTPUT FORMAT ===

6.1  iteration_comparison.csv
     iteration · input_count · retained_count · removed_count ·
     removal_reasons (reason:count pairs) · newly_excluded ·
     newly_retained · oscillating · change_rate · convergence_indicator

6.2  removal_audit.csv
     record_id · iteration_removed · removal_reason · matched_keywords ·
     triggering_evidence · text_excerpt · flagged_for_researcher

6.3  agent1_validation_qa.json
     {
       "prompt_version":"1.0.0","model_version":"","run_id":"",
       "iterations_run":<int>,
       "lineage":[
         {"stage":"","count_in":<int>,"count_out":<int>,
          "removed":<int>,"method":"","count_varied":<bool>,
          "observed_range":[<int>,<int>]}
       ],
       "lineage_reconciles":<bool>,
       "convergence":{
         "converged":<bool>,
         "iteration_of_convergence":<int>,
         "corrections_by_iteration":[<int>],
         "oscillating_records":<int>,
         "blocking_finding":"" 
       },
       "deduplication":{
         "key_type":"","duplicates_identified":<int>,
         "stable_across_reruns":<bool>,
         "id_precision_issues_detected":<bool>,
         "cross_author_repetitions_preserved":<int>
       },
       "anchor_coverage":{
         "retained_with_core_identity_term":<int>,
         "retained_total":<int>,
         "proportion":<float>
       },
       "suspected_false_positives":[
         {"record_id":"","matched_keywords":"","reason":"","excerpt":""}
       ],
       "keyword_stability":{
         "unstable":[{"keyword_id":"","term":"","variance":<float>}],
         "unproductive":[{"keyword_id":"","term":""}],
         "over_broad":[{"keyword_id":"","term":"","match_share":<float>}]
       },
       "limitations_identified":[
         {"limitation":"","affected_scope":"","is_rule_failure":<bool>}
       ]
     }


=== 7. VALIDATION PROCEDURE ===

V1  LINEAGE ARITHMETIC. Every transition reconciles. Set
    lineage_reconciles false and identify the break if not.
V2  AUDIT COMPLETENESS. Every removed record has a row in removal_audit.csv
    with a non-empty reason and evidence.
V3  CONVERGENCE HONESTY. If corrections did not decrease, report
    converged = false and populate blocking_finding. Do not present a
    non-converging process as converged.
V4  NO SILENT CORRECTION. Confirm no record status was changed by this
    validation. Flags only.
V5  RANGE REPORTING. Any count that varied between runs is reported as a
    range with both bounds.
V6  DENOMINATOR PRESENCE. Every proportion carries its numerator and
    denominator.
```

## Statistical note

Convergence here is descriptive, not inferential. A decreasing correction count across iterations indicates the rule set is reaching a stable fixed point on this corpus. It does not establish that the fixed point is correct, only that further iteration of the same rules will not change it. Correctness is addressed only by human review of the flagged records.
