# Agent 1 — Validation, Auditability, and Quality Control

Validation is integrated throughout execution rather than applied as a terminal review step.

## Stage-level validation reports

At each stage the agent produces a brief validation report summarising:

- number of extracted keywords and their distribution across the six layers;
- distribution of relevant and non-relevant records, per platform;
- number of duplicates removed;
- final record count per platform.

These provide an initial overview of what each processing step produced. They are a completeness check, not a correctness check.

## Researcher manual validation

The researcher carries out manual validation using random samples at different points in the workflow, checking:

1. **Relevance decisions** — were retained records genuinely relevant, and were excluded records genuinely irrelevant?
2. **Meaning preservation** — did cleaning alter the sense of the original content?
3. **Keyword mapping** — does `matched_keyword` correctly reflect what triggered retention?

Where notable inconsistencies are identified, the prompts are revised as a new version and the relevant parts of the workflow re-executed.

## Auditability requirements

Key traceability information is preserved throughout:

| Retained | Purpose |
|---|---|
| Original text | Recovery of source content when processed text is insufficient |
| Post identifier | Linkage to the platform record |
| Raw export source | Linkage to the collection file |
| Capture-session information | Linkage to the collection context |
| Prompt version | Attribution of output to a specific specification |
| Agent configuration details | Attribution of output to a specific run |
| Exclusion reasons | Every removal is explicable |

A filtering decision that cannot be explained by an explicit rule is a specification defect, not an acceptable outcome.

## Iterative precision testing

The relevance filtering process is tested across successive iterations to examine whether the retained dataset converges toward a stable and relevant set.

**Convergence criterion:** the number of corrections required should decrease across iterations. A stable or increasing correction count indicates the filtering rules are not converging and the specification requires revision rather than further iteration.

In the reference implementation, four iterative precision tests removed 41 false positives — neighbouring-area references and geographic ambiguities — with progressively smaller corrections, indicating convergence. A final researcher review removed 16 further records.

**Anchoring check:** report the proportion of retained records linked to core project keywords. A high proportion limits the influence of weak contextual matches. The reference implementation reported 94.9%.

## Handover to Agent 3

The systematic reliability assessment — repeated-run testing of the filtering stage, false-positive detection, keyword-taxonomy stability — is carried out by the Validation Agent. See [`../03-validation-agent/`](../03-validation-agent/).

The stage-level validation described here establishes that the pipeline ran correctly. It does not establish that its outputs are reliable. That is a separate question, answered by a separate procedure.
