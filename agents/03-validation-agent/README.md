# Agent 3 — Validation Agent

**Quality control. Supports researcher judgement; does not replace it.**

## Role

The Validation Agent is the third component of the pipeline. It supports technical quality control by assessing the reliability, reproducibility, and internal consistency of outputs from the Data Preparation Agent and the Processing and Social Interpretation Agent.

It compares repeated runs, detects unstable classifications, checks internal coherence, and organises validation records.

**It does not produce interpretations and does not make final correction decisions.** Those remain under researcher control. Its function is to surface instability, not to resolve it.

## Why technical validation does not reduce to a single score

Technical validation here does not rely on a single accuracy score. It applies complementary checks to the stages where filtering, classification, and interpretation shape the final social layer.

The two agents perform different functions and are therefore evaluated against different criteria:

- **Agent 1** is assessed on the **stability and traceability** of its filtering process.
- **Agent 2** is assessed on **field reproducibility, agreement with independent comparators, and internal coherence**.

These results are not combined into a single figure, because they measure different properties — ranging from deterministic processing to interpretive classification. A composite score would obscure exactly the distinction the framework depends on.

## Operational restrictions

These constraints are written into the specification:

- Do not treat AI outputs as self-validating.
- Preserve traceability to original records and intermediate outputs.
- Distinguish deterministic checks from interpretive validation.
- Keep uncertain or unstable cases **visible**; do not hide them.
- Do not replace researcher interpretation or final correction decisions.
- Report contradictions, instability, and limits clearly.

**Semantic variation is flagged for review, not treated automatically as error.** Classifications such as sentiment, emotion, and stakeholder type may differ because of genuine interpretive ambiguity. Treating every disagreement as a fault would misrepresent the nature of the task.

## Workflow

```
  1. Validation inputs
     iterative testing outputs · enriched dataset · repeated runs · audit files
                    ↓
  2. Agent 1 checking
     iterative filtering review · keyword taxonomy stability · false-positive detection
                    ↓
  3. Agent 2 checking
     repeated classification runs · agreement analysis · stability assessment
                    ↓
  4. Cross-dimension coherence tests
     sentiment–emotion · problem–sentiment · time · engagement
                    ↓
  5. Flagging and correction support
     contradictions · unstable cases · corrections · review-needed records
                    ↓
  6. Validation outputs
     reproducibility tables · coherence checks · correction summary · technical report
```

## Testing campaigns

Two campaigns in the reference implementation:

| Campaign | Target | Design |
|---|---|---|
| 1 | Agent 1 relevance filtering | Five iterations across the raw corpus |
| 2 | Agent 2 social classification | Four repeated runs across all records |

Thirty-four technical checks in total: derived-field comparisons, statistical tests, confusion matrices, subgroup analyses, cross-dimension coherence checks.

## Validation criteria by field type

Different criteria apply according to how each field was produced. Semantic classifications cannot be assessed using the same exact-matching standard as mechanically derived variables.

| Field type | Validation method |
|---|---|
| Observed | Checked against the source records |
| Derived | Independently recomputed |
| Inferred — structural | Rule-based verification |
| Inferred — semantic | Compared with independent reclassification methods |

## Measuring agreement on semantic fields

Three measures are used together, never in isolation.

**Raw agreement** — the percentage of records receiving the same classification from two methods.

**Cohen's Kappa** — agreement after accounting for agreement that could occur by chance. A value of 1 indicates perfect agreement, 0 indicates chance-equivalent agreement, and negative values indicate agreement below chance expectation. Kappa is particularly useful where one category dominates the sample and could produce a misleadingly high raw agreement.

**Confusion matrices** — show *where* agreement and disagreement occur. Diagonal cells represent matching classifications; off-diagonal cells identify the categories between which classifications differ.

Because Kappa is affected by strongly unbalanced category distributions, it is interpreted alongside raw agreement and the confusion matrices rather than as an isolated measure.

> **The Kappa paradox.** In the reference implementation, two rule-based comparators agreed on 87.0% of records with a Kappa of only 0.093, because both classified most posts as neutral. High apparent agreement, no demonstrated consistency across categories. This is why the three measures must be read together.

## Independent comparators

Semantic outputs are compared against independently constructed rule-based reclassification methods of increasing vocabulary breadth — in the reference implementation, three: Conservative, Moderate, and Broad.

**These comparators are not ground truth**, and neither is the agent. The comparison establishes where classifications are robust to method choice and where they are not. Where the agent and comparators diverge systematically, the divergence itself is the finding, and the disputed records go to human review.

## Cross-dimension coherence testing

Field-level reproducibility alone does not guarantee internal consistency. Individual fields may each be stable while contradicting one another — a positive sentiment paired with a negative emotion, or a project phase that does not match the post date.

Tests pair two fields whose values are logically constrained by one another and run across the full dataset. In the reference implementation, nine tests, all passing.

**What a passing coherence test does and does not show.** It does not confirm that any semantic classification is objectively correct, since an incorrect interpretation can still be internally consistent. It demonstrates that the enriched fields follow a coherent classification logic, so relationships observed between dimensions reflect consistent links rather than inconsistencies introduced during processing.

## Robustness across subsets

Reproducibility figures aggregated over a whole dataset conceal where the remaining variation sits. Classification stability is therefore examined by **platform**, **post length**, and **the agent's own confidence score**, since each may systematically influence results.

Stability is measured as the proportion of records receiving the same label in all runs (4/4) or in at least three (≥3/4).

The subset analysis produced the framework's most consequential negative finding: **the agent's confidence score is inversely related to cross-method stability**, and therefore cannot be used as a reliability indicator. See [`../../docs/03-reproducibility.md`](../../docs/03-reproducibility.md).

## Contents of this directory

| File | Contents |
|---|---|
| [`specification.md`](specification.md) | Agent-level seven-component specification |
| [`prompts/prompt-1-preparation-agent-validation.md`](prompts/prompt-1-preparation-agent-validation.md) | Full runnable prompt: 8 tasks, convergence criterion, keyword stability classes, end-to-end lineage reconciliation |
| [`prompts/prompt-2-interpretation-agent-reproducibility.md`](prompts/prompt-2-interpretation-agent-reproducibility.md) | Full runnable prompt: field partitioning, Kappa formula and joint-interpretation rule, three subset analyses, systematic-disagreement detection |
| [`prompts/prompt-3-cross-dimension-coherence.md`](prompts/prompt-3-cross-dimension-coherence.md) | Full runnable prompt: **13 coherence tests** with explicit scope and constraint per test |
| [`schemas/validation-outputs.md`](schemas/validation-outputs.md) | All seven output tables with per-field metadata, plus mandatory reporting elements |
| [`validation.md`](validation.md) | How the validator itself is checked |
