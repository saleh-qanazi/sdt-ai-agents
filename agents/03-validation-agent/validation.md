# Agent 3 — Validating the Validator

A validation agent that cannot itself be checked would simply relocate the problem it was built to solve. This document states the conditions under which its outputs are trustworthy.

## Reporting requirements

**Denominators visible.** Every result is reported as a ratio, not a bare percentage. `0 / 216 violations` is informative; `100% pass` is not.

**Sample composition stated.** Where a stratified sample is used, its construction is declared — including deliberate over-representation of difficult categories. In the reference implementation, the sentiment sample deliberately over-represented negative and mixed posts, because those categories are harder to interpret consistently. A reader who did not know this would misread the agreement figures.

**Negative findings reported equally.** The framework's most consequential validation result was negative: the agent's confidence score is inversely related to cross-method stability, and cannot be used as a reliability filter. Suppressing or softening such a finding would defeat the purpose of the exercise.

**What each result does not establish, stated explicitly.** A coherence test that passes does not confirm any classification is correct. A high raw agreement does not demonstrate consistency where one category dominates. These qualifications belong in the report, not in a footnote.

## Independence requirements

**Comparators must be constructed independently** of the agent whose outputs they assess. A comparator derived from the same prompt logic would measure nothing.

**Comparators are not ground truth.** Where the agent and comparators diverge systematically — as when context-based classification disagrees with name-based rules on institutional and media actors — the divergence identifies records requiring human review. It does not establish which method is right.

**Derived fields are recomputed, not compared.** Mechanically derived variables are independently recalculated from source values rather than checked against the agent's own output, since agreement with oneself demonstrates nothing.

## Convergence requirements

For iterative filtering validation, the correction count must **decrease** across iterations. A stable or increasing count means the rules are not converging, and the correct response is specification revision, not further iteration.

## Reconciliation requirement

The record-count lineage must reconcile end to end: every transition from one count to the next attributable to a named, documented step. Where a count varies between runs, the variation is reported as a reproducibility finding rather than silently resolved to a single number.

## What remains outside the validator

Final correction decisions, methodological judgements, and the resolution of flagged records are the researcher's responsibility. The validator identifies where judgement is required; it does not exercise it.

This is not a limitation to be engineered away in a future version. It is the design.
