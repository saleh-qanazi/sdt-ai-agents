# Agent 2 — Validation, Auditability, and Quality Control

Quality control is integrated into the processing procedure. Its purpose at this stage is to identify **structurally invalid, unsupported, or internally inconsistent outputs before they are used in analysis.**

The empirical evaluation of reliability — repeated runs, agreement measurement, comparison against independent reclassification — is a separate procedure carried out by Agent 3.

## Three check types

### Structural checks
Verify that required fields are returned in the expected format and that controlled fields use permitted values only. A field containing a value outside its enumerated set is a defect, regardless of how plausible the value appears.

### Evidence checks
Verify that inferred attributes contain the required justification, and that no information has been introduced that is unsupported by the source record or approved contextual material.

An inferred field without a justification note is not a low-quality output. It is an invalid one, because the interpretation cannot be reviewed.

### Cross-field checks
Verify that analytical fields which depend on one another are coherent. Examples of contradictions this catches:

| Contradiction | Why it matters |
|---|---|
| A demographic cue assigned to an institutional account | Individual inference applied to an organisation |
| A spatial interpretation without supporting place evidence | Precision asserted beyond the record |
| An issue type inconsistent with the detailed problem fields | Consolidation error |
| An emotional signal not supported by the expression recorded in its justification | Interpretation detached from evidence |

## Review routing

Records containing insufficient evidence, ambiguity, or sensitive inference are marked for researcher review. The researcher then **confirms, corrects, or rejects** the corresponding interpretation before it enters later analysis.

Review flags are not advisory. A record flagged and unreviewed should not contribute to a reported finding.

## What this procedure does not establish

Passing all three check types establishes that the output is **structurally valid and internally consistent**. It does not establish that any individual semantic classification is correct.

An incorrect interpretation can be perfectly well-formed, fully justified, and internally coherent. Detecting that requires comparison against independent methods and repeated runs — which is Agent 3's function.

## Reference implementation consistency check

At this stage, checking focused on whether the expected field structure was produced correctly and whether the analytical status of each field remained identifiable:

- observed information remained linked to the original records;
- derived variables followed predefined rules;
- inferred fields retained the confidence and justification information required for later review.

Result: 753 records × 70 fields, structurally valid.

The subsequent technical validation — repeated runs, independent recomputation of derived fields, assessment of semantic classifications, cross-dimension coherence testing — showed high reproducibility for observed, derived, and structurally generated fields, with greater variation concentrated in semantic interpretation, particularly sentiment.

See [`../03-validation-agent/`](../03-validation-agent/) and [`../../docs/03-reproducibility.md`](../../docs/03-reproducibility.md).
