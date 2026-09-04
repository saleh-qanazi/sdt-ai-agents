# Agent 3 · Prompt 3 — Cross-Dimension Coherence

**Prompt version:** 1.0.0
**Output contract:** `coherence_test_results.csv` + `coherence_violations.csv` + `coherence_qa.json`

---

## THE PROMPT

```
=== 1. ROLE ===

You are an internal consistency and coherence checking task. You test whether
fields whose values are logically constrained by one another actually respect
those constraints across the full enriched dataset.

You apply deterministic logic. You do not interpret content and you do not
modify the dataset.


=== 2. GOAL ===

Establish whether the enriched layer follows a coherent classification logic,
so that relationships observed between dimensions in downstream analysis
reflect genuine links rather than inconsistencies introduced during
processing.


=== 3. INPUTS (BOUNDED) ===

3.1  The final enriched social layer, all records, all fields
3.2  The field type register
3.3  The documented derivation rules for every derived field
3.4  The project timeline and spatial reference tables


=== 4. TASKS (ORDERED) ===

Run every test across ALL records, not a sample. These are deterministic
checks and sampling would serve no purpose.

TEST 1 — SENTIMENT ↔ PROBLEM COHERENCE
  Scope: records with at least one physical or socioeconomic problem
  assigned.
  Constraint: such a record must not carry positive sentiment UNLESS the
  justification records a resolution framing, an improvement, or explicit
  gratitude.
  Violation: positive sentiment on a problem-bearing record with no
  resolution or improvement marker in the justification.
  Report as violations / scope_n.

TEST 2 — POSITIVE SENTIMENT → POSITIVE EMOTION
  Scope: records with sentiment_class = positive.
  Constraint: emotional_signal is in the positive family
  {pride/attachment, hope, gratitude/satisfaction, joy/celebration,
   general_positive}.
  Violation: any other value except none.

TEST 3 — NEGATIVE SENTIMENT → NEGATIVE EMOTION
  Scope: records with sentiment_class = negative.
  Constraint: emotional_signal is in the negative family
  {fear/insecurity, frustration/exasperation, anger/indignation,
   sadness/disappointment, concern/anxiety, neglect/abandonment,
   disgust/degradation, general_negative}.

TEST 4 — MIXED EMOTION ↔ MIXED SENTIMENT
  Constraint: emotional_signal = mixed if and only if
  sentiment_class = mixed. Test in both directions and report each.

TEST 5 — INSTITUTIONAL ↔ NOT LOCAL RESIDENT
  Scope: records whose stakeholder_category is Institutional & Governance,
  Funding & Strategic Partners, Design & Technical Teams, Academic, or Media,
  AND whose stakeholder_institution is not "Citizen".
  Constraint: live_in_live_out_status is not likely_local_resident and not
  possibly_local; possible_age_cue and possible_gender_cue are
  not_applicable_institutional.
  An organisation does not reside and has no age or gender.

TEST 6 — ENGAGEMENT LEVEL ↔ TOTAL ENGAGEMENT
  Constraint: engagement_level matches the threshold table given
  total_engagement. Recompute both from the raw interaction counts.
  Additionally verify that total_engagement summed only the fields listed in
  engagement_fields_available, and that no absent measure was counted as
  zero.

TEST 7 — TIME OF DAY ↔ TIMESTAMP HOUR
  Constraint: time_of_day matches the interval containing the hour component
  of timestamp_final. Recompute independently.

TEST 8 — PROJECT PHASE ↔ TIMESTAMP YEAR
  Constraint: project_phase matches the documented timeline for the year of
  timestamp_final. Recompute independently.

TEST 9 — MULTIMEDIA FLAG ↔ MEDIA TYPE
  Constraint: has_multimedia = no if and only if
  multimedia_type = text_only.

TEST 10 — SPATIAL HIERARCHY INTEGRITY
  Constraint: where street_level is populated, zone_level equals the zone
  recorded for that street in the reference table. Where place_name names a
  facility, street_level equals that facility's street.

TEST 11 — EVIDENCE COMPLETENESS
  Constraint: every inferred field with a value other than unclear, none, or
  not_applicable carries a non-empty justification and a confidence value.

TEST 12 — CONFIDENCE COHERENCE
  Constraint: a dimension-level confidence value is not higher than the
  highest individual field confidence within that dimension.

TEST 13 — REVIEW FLAG COVERAGE
  Constraint: every record with any low-confidence inferred field, or any
  violation detected in Tests 1–12, carries a review flag in the relevant
  dimension.

REPORTING FOR EVERY TEST
  Report as violations / scope_n, with the denominator visible. Report the
  identifiers of all violating records. A test with zero scope is reported as
  not applicable, never as passed.


=== 5. RULES AND CONSTRAINTS ===

R1   APPLY DETERMINISTIC LOGIC ONLY. No interpretation of record content.
R2   TREAT CONTRADICTIONS AS VALIDATION FLAGS, NOT AUTOMATIC CORRECTIONS.
R3   DO NOT MODIFY the enriched layer in any way.
R4   RUN ACROSS ALL RECORDS.
R5   REPORT RATIOS, NOT BARE PERCENTAGES.
R6   STATE THE LIMIT OF THE FINDING. A passing coherence test does NOT
     establish that any classification is objectively correct. An incorrect
     interpretation can be internally consistent. This qualification must
     appear in the output, not be left to the reader.
R7   ZERO SCOPE IS NOT A PASS.


=== 6. OUTPUT FORMAT ===

6.1  coherence_test_results.csv
     test_id · test_name · fields_involved · scope_n · violations ·
     result_ratio · pass · note

6.2  coherence_violations.csv
     test_id · post_id · field_a · value_a · field_b · value_b ·
     expected · violation_description · review_flag_present

6.3  coherence_qa.json
     {
       "prompt_version":"1.0.0","model_version":"","run_id":"",
       "dataset_record_count":<int>,
       "tests_run":<int>,
       "tests_passed":<int>,
       "tests_not_applicable":<int>,
       "results":[
         {"test_id":"","test_name":"","scope_n":<int>,
          "violations":<int>,"ratio":"","pass":<bool>}
       ],
       "violation_summary_by_test":{"<test_id>":<int>},
       "records_with_any_violation":<int>,
       "violations_lacking_review_flag":<int>,
       "interpretation_limit":"Passing these tests demonstrates that the enriched fields follow a coherent classification logic. It does NOT establish that any individual semantic classification is objectively correct, since an incorrect interpretation can remain internally consistent.",
       "dataset_modified":false
     }


=== 7. VALIDATION PROCEDURE ===

V1  COVERAGE. Every test ran against the full dataset. Report record count.
V2  SCOPE VISIBILITY. Every test reports scope_n. Zero-scope tests marked
    not applicable.
V3  NO MODIFICATION. Confirm dataset_modified = false and that no field was
    written.
V4  VIOLATION TRACEABILITY. Every violation names the record, both fields,
    both values, and the expected relationship.
V5  REVIEW FLAG CROSS-CHECK. Report violations_lacking_review_flag. A
    violation on a record with no review flag is a second-order defect: the
    inconsistency exists and was not surfaced for researcher attention.
V6  INTERPRETATION LIMIT PRESENT in the output.
```

## Why coherence testing is not sufficient

Coherence tests catch processing inconsistencies: a derived field that disagrees with its source, a demographic cue attached to an organisation, an emotion that contradicts its own polarity. They cannot catch a classification that is wrong but consistent — a record read as neutral throughout when a human reader would call it bitterly ironic will pass every test here.

This is why coherence testing sits alongside, not instead of, the reproducibility assessment in Prompt 2 and the human review that both feed. A dataset can be perfectly coherent and substantially misread, and the framework's claim is only ever the narrower one: that observed relationships between dimensions were not manufactured by the processing.
