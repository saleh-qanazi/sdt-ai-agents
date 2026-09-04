# 6. Glossary

Terms are defined as they are used in this repository. Several have broader or contested meanings elsewhere; the definitions here are operational, not universal.

---

**AI agent** — A task-specific, prompt-specified process built on a large language model and governed by a researcher-defined specification. Not autonomous in the reinforcement-learning sense. The researcher defines the task, inputs, rules, and expected output; the model executes within them.

**Anchor and context logic** — The relevance rule requiring at least one project-identity keyword (L1) to anchor a query or classification, with one or two contextual terms from other layers added where relevant. A spatial reference alone is never sufficient.

**Bounded inputs** — An exhaustive list of what an agent may use. Anything not listed is out of scope, including the model's own background knowledge. The third component of the specification schema.

**Cohen's Kappa (κ)** — Agreement between two classification methods after accounting for agreement that could occur by chance. 1 = perfect; 0 = chance-equivalent; negative = below chance expectation. Sensitive to unbalanced category distributions and never reported alone.

**Confidence** — For inferred fields, the **strength of the available evidence**, not a statistical probability of correctness. High = direct or unambiguous evidence; medium = several consistent but partly indirect cues; low = limited or ambiguous evidence. Validated to be *inversely* related to cross-method reproducibility, and therefore unusable as a reliability filter.

**Cross-dimension coherence** — Internal consistency between analytical fields whose values are logically constrained by one another. Passing coherence tests demonstrates a coherent classification logic; it does not demonstrate correctness.

**Derived field** — A field generated mechanically from existing data without semantic interpretation. Fully reproducible.

**Enriched social record** — The output of the interpretation agent: source data, derived attributes, and inferred interpretations combined across four analytical dimensions, with confidence, justification, and review information attached to inferred fields.

**Inferred field** — A field produced through AI-assisted processing and researcher-guided interpretation. Subdivided into *structural* (rule-based, fully reproducible) and *semantic* (contextual interpretation, variable).

**Kappa paradox** — High raw agreement coexisting with near-zero Kappa, caused by one category dominating the sample. Illustrates why raw agreement and Kappa must be read together.

**Keyword taxonomy** — A six-layer vocabulary derived from documentary sources and researcher input, used for collection and relevance filtering. Layers: institutional and project identity (L1); spatial and place-based (L2); project components and themes (L3); citizen vocabulary and lived experience (L4); actors and institutions (L5); temporal and event-based (L6).

**Multi-label classification** — Assignment of several categories to a single record, where the record legitimately belongs to more than one. Applied to activities and issues fields.

**Noise** — Elements that do not contribute to interpreting a post or that create false relevance: duplicated records, broken metadata, unrelated commercial content, platform artefacts, isolated URLs, out-of-area references. **Informal language, emotional expressions, and resident vocabulary are not noise.**

**Observed field** — A field preserved directly from the source record without transformation. Fully reproducible.

**Prepared social record** — The output of the data preparation agent: a cleaned, normalised, relevance-filtered record with provenance retained.

**Provenance** — The retained chain linking any output back to the record, run, prompt version, and model version that produced it.

**Relevance confidence** — Tier-based classification of how strongly a record matches the study area. Tier 1 (high): a direct project-identity term appears. Tier 2 (medium): a spatial reference appears with one or two contextual terms.

**Reproducibility** — Here, the repeatability of a **documented procedure**, resting on four properties: published specification, fixed output schema, raw-to-output traceability, and empirically measured variance. **Explicitly not determinism.**

**Review flag** — A marker routing a record to researcher inspection because evidence is insufficient, ambiguous, or the inference is sensitive. Not advisory: a flagged and unreviewed record should not contribute to a reported finding.

**Sentiment** — Evaluative orientation expressed in a record: positive, neutral, negative, or mixed. A description of what is expressed in available content, **not** a measurement of an author's internal psychological state. The least reproducible field in the pipeline.

**Seven-component specification schema** — The fixed structure every prompt follows: agent role; agent goal; bounded inputs; ordered procedural steps; rules and constraints; output format; validation procedure.

**Social Digital Twin (SDT)** — An urban digital twin that treats social dimensions — citizen perceptions, behaviours, lived experience — as constitutive inputs rather than a supplementary layer.

**Spatial anchoring** — Linking a social record to a physical entity (zone, street, or facility) through join tables, applied only to the extent that evidence in the record supports it.

**Two-tier relevance classification** — See *relevance confidence*.
