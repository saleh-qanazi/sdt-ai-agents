# 4. Ethics and Methodological Safeguards

These safeguards are written into the agent specifications themselves, not applied afterwards as a review step. They are reproduced here so that anyone adapting the method can see which constraints are load-bearing.

## 4.1 The governing prohibition: no individual-level human twins

The framework produces **aggregate social representations of a place**, not digital models of individuals.

No agent in this pipeline may be used to construct an individual-level "human twin". Actor and demographic information serves aggregate analytical purposes. Where an individual name is preserved in the working dataset for traceability, it is not carried into reporting, and aggregation and anonymisation are applied where required.

This constraint is what separates social sensing for urban analysis from profiling. It is not negotiable and should not be relaxed when adapting the method.

## 4.2 Data collection

**Public content only.** Only publicly visible content is collected. No private accounts, no authenticated-access content, no direct messages.

**Sensitive identifiers.** Removed or anonymised where necessary during preparation.

**Platform terms.** Collection through browser-based capture is subject to each platform's terms of service. Compliance is the responsibility of whoever executes the collection. Publishing this method does not authorise any particular collection practice.

**Legal basis.** Where the General Data Protection Regulation or an equivalent regime applies, publicly visible social media content remains personal data. A lawful basis, a retention policy, and institutional ethical approval are required before collection begins.

## 4.3 Agent 1 — safeguards for a non-interpretive component

Agent 1's role is deliberately narrow, and the narrowness is itself a safeguard.

- Its role is limited to preparing data. It does **not** perform social interpretation or demographic inference.
- Prompt rules prevent fabrication and unsupported inference.
- Original identifiers and source information are preserved throughout processing.
- Every removal is logged with a reason, so exclusion decisions remain auditable rather than invisible.

A filtering decision that cannot be explained by an explicit rule is a specification defect, not an acceptable outcome.

## 4.4 Agent 2 — safeguards for an interpretive component

Because Agent 2 produces inferred social information, it operates under a stricter regime.

**Analytical status remains visible.** Observed, mechanically derived, and inferred attributes remain distinguishable in the output. A reader can always tell which is which.

**Every inference carries its evidence.** Each inferred field is accompanied by a justification note recording the words, phrases, metadata, or contextual signals used, and a confidence value expressing the strength of that evidence. The basis of every interpretation remains visible and can be reviewed, challenged, and overturned.

**Absence of evidence is represented as absence.** Where evidence does not support a classification, the field remains unclear or is flagged for review. It is not filled by assumption. This is the single most important rule in the specification.

**Demographic cues are retained only where evidence exists.** Age and gender cues are inferred only from explicit textual or self-description signals, are always marked as uncertain, and are never treated as verified personal characteristics. Institutional accounts are explicitly excluded from individual demographic classification.

**Spatial precision is not inflated.** Residence and location classifications are not assigned at a level of precision greater than the record supports. An inferred street-level assignment from indirect textual evidence is not equivalent to an explicit geotag, and the confidence field records the difference.

**Sentiment is not a psychological claim.** Sentiment and emotional signals describe what is *expressed* in the available content. They are not measurements of an author's internal psychological state, and must not be reported as such.

**Confidence is not reliability.** As documented in [`03-reproducibility.md`](03-reproducibility.md), the relationship between the agent's confidence score and cross-method reproducibility is inverse. Confidence expresses the strength of contextual interpretation. It must not be used as a filter for reliability.

## 4.5 Agent 3 — safeguards for validation

The validation agent's restrictions are structural:

- Do not treat AI outputs as self-validating.
- Preserve traceability to original records and intermediate outputs.
- Distinguish deterministic checks from interpretive validation.
- Keep uncertain or unstable cases **visible**; do not hide them.
- Do not replace researcher interpretation or final correction decisions.
- Report contradictions, instability, and limits clearly.

Semantic variation is flagged for review rather than treated automatically as error, because classifications such as sentiment, emotion, and stakeholder type may differ because of genuine interpretive ambiguity rather than fault.

## 4.6 Reporting obligations

Anyone publishing findings produced with this pipeline should report, at minimum:

1. The prompt version and model version used.
2. The record-count lineage, with every change attributable to a named step.
3. The measured agreement or variance for any semantic field on which a claim rests.
4. The proportion of records whose spatial assignment rests on inferred rather than explicit evidence, where a spatial claim is made.
5. Which records were flagged for review and how they were resolved.

A claim resting on a semantic field, reported without its measured variance, overstates what the method supports.

## 4.7 What this method cannot establish

Social media traces are partial, unrepresentative, and shaped by platform populations, search interfaces, recommendation mechanisms, account visibility, and the researcher's own browsing strategy. Broader collection coverage does not remove these sampling limitations.

The resulting social layer describes **visible digital discourse about a place**. It does not describe the population of that place. Institutional, documentary, and survey-based sources are integrated precisely to contextualise and triangulate these signals, and findings should be reported against that triangulation rather than on the social media corpus alone.
