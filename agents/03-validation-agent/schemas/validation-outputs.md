# Agent 3 — Validation Output Schemas

Metadata conventions follow [`../../02-social-interpretation-agent/schemas/README.md`](../../02-social-interpretation-agent/schemas/README.md).

Every table below carries a common reporting requirement: **denominators are visible**. Results appear as ratios, never as bare percentages, because a proportion without its denominator cannot be assessed.

---

## 1. `iteration_comparison.csv`

Output of Prompt 1. One row per filtering iteration.

| Field | Type | Description |
|---|---|---|
| `iteration` | integer | Sequence number, 1-indexed |
| `input_count` | integer | Records entering this iteration |
| `retained_count` | integer | Records retained |
| `removed_count` | integer | Records removed |
| `removal_reasons` | string | `reason:count` pairs, pipe-separated |
| `newly_excluded` | integer | Retained previously, excluded now |
| `newly_retained` | integer | Excluded previously, retained now |
| `oscillating` | integer | Status changed in both directions across iterations |
| `change_rate` | float | (newly_excluded + newly_retained) ÷ input_count |
| `convergence_indicator` | enum | `converging` · `stable` · `diverging` |

**Convergence criterion.** `change_rate` must decrease across iterations. A stable or increasing rate means the rule set is not converging, and the correct response is specification revision rather than further iteration. **Oscillating records are reported separately** from stable changes, because oscillation indicates rule ambiguity rather than progressive refinement.

---

## 2. `removal_audit.csv`

| Field | Type | Description |
|---|---|---|
| `record_id` | string | The removed record |
| `iteration_removed` | integer | Which iteration |
| `removal_reason` | enum | Closed list from the filtering schema |
| `matched_keywords` | string | What had matched |
| `triggering_evidence` | string | The specific text producing the removal |
| `text_excerpt` | string | First 200 characters, for researcher review |
| `flagged_for_researcher` | boolean | Requires a human decision |

**Every removed record has a row.** An unexplained removal is a defect, not an acceptable outcome.

---

## 3. `agreement_tables.csv`

Output of Prompt 2. One row per field per method pair.

| Field | Type | Description |
|---|---|---|
| `field` | string | Which semantic field |
| `method_a` | string | First method |
| `method_b` | string | Second method |
| `n` | integer | **Denominator** — records compared |
| `raw_agreement` | float | Proportion receiving identical classification |
| `cohens_kappa` | float | Chance-corrected agreement, three decimal places |
| `kappa_paradox_present` | boolean | High raw agreement with low Kappa |
| `dominant_category` | string | The category inflating expected agreement, where applicable |
| `interpretation_note` | string | What drives the observed level |

**Cohen's Kappa:** κ = (p_o − p_e) / (1 − p_e), where p_o is observed agreement and p_e is expected agreement computed from the marginal distributions.

| κ | Meaning |
|---|---|
| 1 | Perfect agreement |
| 0 | Agreement equivalent to chance |
| Negative | Agreement below chance expectation |

**Kappa is never reported alone.** It is sensitive to unbalanced category distributions. Where one category dominates both methods, expected agreement is high and even substantial observed agreement yields a low Kappa. Flag `kappa_paradox_present = true` where raw agreement exceeds 0.80 and κ falls below 0.20, and name the dominant category.

---

## 4. `confusion_matrices.csv`

Long format, so matrices of any dimensionality are representable.

| Field | Type | Description |
|---|---|---|
| `field` | string | Which field |
| `method_a` | string | Row method |
| `method_b` | string | Column method |
| `category_a` | string | Row category |
| `category_b` | string | Column category |
| `count` | integer | **Raw count, not percentage** |

Diagonal cells (`category_a` = `category_b`) are matches. Off-diagonal cells identify **which categories are confused with which** — the information raw agreement and Kappa both discard. Each matrix must sum to `n`.

---

## 5. `stability_by_subset.csv`

| Field | Type | Description |
|---|---|---|
| `field` | string | Which field |
| `subset_type` | enum | `platform` · `text_length` · `confidence` |
| `subset_value` | string | The subset |
| `n` | integer | **Denominator** |
| `stable_4_of_4` | float | Same label in all runs |
| `stable_3_of_4` | float | Same label in at least three |
| `mean_text_length` | float | Characters |
| `modal_label` | string | Most frequent agent label in this subset |

**Why subset analysis is required.** Aggregate reproducibility conceals where variation sits. The reference implementation's most consequential finding emerged only from the confidence subset: the relationship between the agent's confidence score and cross-method stability is **inverse**, which means confidence cannot be used as a reliability filter. An aggregate figure would have hidden this entirely.

**Small subsets are reported with their count and marked non-generalisable** rather than dropped or presented alongside large ones without qualification.

---

## 6. `coherence_test_results.csv`

Output of Prompt 3. One row per test.

| Field | Type | Description |
|---|---|---|
| `test_id` | string | `T1`–`T13` |
| `test_name` | string | Human-readable |
| `fields_involved` | string | The constrained pair |
| `scope_n` | integer | **Records to which the test applies** |
| `violations` | integer | Failures |
| `result_ratio` | string | `violations / scope_n` |
| `pass` | boolean | Zero violations |
| `note` | string | Qualification |

**Zero scope is reported as not applicable, never as a pass.** A test that applied to no records demonstrates nothing.

---

## 7. `coherence_violations.csv`

| Field | Type | Description |
|---|---|---|
| `test_id` | string | Which test |
| `post_id` | string | The record |
| `field_a` / `value_a` | string | First field and its value |
| `field_b` / `value_b` | string | Second field and its value |
| `expected` | string | The constraint that was breached |
| `violation_description` | string | Plain statement of the inconsistency |
| `review_flag_present` | boolean | Whether the record already carried a review flag |

`review_flag_present = false` on a violating record is a **second-order defect**: the inconsistency exists *and* was not surfaced for researcher attention.

---

## 8. Validation record structures

### ValidationRecord

| Field | Type | Description |
|---|---|---|
| `validation_id` | uuid | |
| `validation_type` | enum | `filtering_consistency` · `reproducibility` · `coherence` |
| `validation_status` | enum | `pass` · `fail` · `flagged_for_review` |
| `validator` | string | Agent identifier or named human reviewer |
| `uncertainty_level` | enum | Strength of the validation conclusion |
| `target_entity` | string | Record or field assessed |
| `run_id` | uuid | Run under assessment |

### ValidationSource

| Field | Type | Description |
|---|---|---|
| `source_type` | enum | `stakeholder` · `citizen` |
| `instrument` | string | Survey, interview, workshop |
| `respondents` | integer | n |
| `collection_date` | date | |

---

## 9. Reproducibility summary

The headline table. Produced from the field partition and the per-field results.

| Field category | n fields | Reproducibility | Basis |
|---|---|---|---|
| Observed | — | Expected 100% | Verified against source records |
| Derived | — | Expected 100% | Independently **recomputed**, not compared to the agent's own output |
| Inferred — structural | — | Expected 100% | Rule-based verification |
| Inferred — semantic | — | Measured range | Compared against independent reclassification |

**No composite score is produced.** Agent 1 is assessed on filtering stability and traceability; Agent 2 on field reproducibility, agreement, and coherence. These are different properties, and a weighted average would let full reproducibility on mechanical fields conceal much lower agreement on sentiment — precisely the distinction the framework depends on.

---

## 10. Mandatory reporting elements

Every validation output must carry:

1. **Denominators** on every proportion.
2. **Sample composition**, including any deliberate over-representation of difficult categories. A reader unaware that a sentiment sample over-weights negative and mixed posts will misread every agreement figure in it.
3. **Negative findings**, with the same prominence as positive ones.
4. **The interpretation limit**, stated explicitly: passing coherence tests demonstrates that the enriched fields follow a coherent classification logic. It does **not** establish that any individual semantic classification is objectively correct, since an incorrect interpretation can remain internally consistent.
