# Agent 2 — Processing and Social Interpretation Agent

**Interpretive, evidence-bound, justification-required.**

## Role

The Processing and Social Interpretation Agent receives the prepared cross-platform records produced by Agent 1 and applies a researcher-defined analytical structure. Its purpose is to transform those records into structured social interpretations that can support quantitative analysis, qualitative interpretation, and spatial analysis.

Unlike Agent 1, which performs mainly rule-based operations, this agent produces **inferred** social information and therefore operates under stronger control over the evidence used for each interpretation.

The agent does not operate as an autonomous classifier developing its own categories. The researcher defines the categories, permitted inputs, rules, expected outputs, and restrictions before execution. AI assistance allows the same analytical procedure to be applied across a heterogeneous corpus without coding every record independently by hand. Its use is not linked to a minimum dataset size; the same procedure applies to hundreds or thousands of records, though execution time, cost, and review requirements grow with corpus size.

## The four analytical dimensions

The analytical structure derives from basic questions applied to each record: **who** is represented, **what** is happening or being discussed, **why** it is mentioned where explicitly expressed, **how** it is perceived, and **where and when** it occurs.

| Dimension | Question | Content |
|---|---|---|
| **Profile** | who | Broad actor categories, platform of origin, posting characteristics, limited demographic or residence cues where evidence supports them |
| **Activities and issues** | what, and why where supported | Everyday activities, mobility, urban services, facilities, community events, use of public space, project subjects, physical and socioeconomic concerns |
| **Emotion** | how | Sentiment polarity and specific emotional expressions |
| **Context** | where, when, circumstances | Temporal characteristics, spatial references, multimedia information, project context, contextual notes |

These four dimensions are specific to the analytical structure developed here. **They are not proposed as universal categories of social analysis.**

## Three types of information

The enriched record combines three kinds of field, and the distinction is load-bearing for every downstream claim.

| Type | How produced | Examples | Reproducibility |
|---|---|---|---|
| **Observed** | Preserved directly from the collected dataset | platform, timestamp, original text, author, hashtags, engagement metrics, URL, explicit place metadata | 100% |
| **Derived** | Generated mechanically from existing data without semantic interpretation | posting patterns, time-of-day classes, day-of-week, year-month, keyword taxonomy lookups | 100% |
| **Inferred** | Produced through AI-assisted processing and researcher-guided interpretation | actor type, demographic cues, residence status, activity category, issue type, sentiment, emotional signal, inferred location | Varies — see reproducibility doc |

**Confidence semantics.** For inferred fields, confidence represents the **strength of the available evidence**, not a statistical probability of correctness.

| Level | Condition |
|---|---|
| High | Direct or unambiguous evidence available |
| Medium | Several consistent but partly indirect cues |
| Low | Evidence limited or ambiguous |

Low-confidence and sensitive interpretations are routed to researcher review.

> **Warning.** Validation established an *inverse* relationship between the agent's confidence score and cross-method reproducibility. Confidence must not be used as a reliability filter. See [`../../docs/03-reproducibility.md`](../../docs/03-reproducibility.md).

## Architecture

```
  Prepared cross-platform social records
                    ↓
  Input verification and source-record linkage
                    ↓
  ┌─────────────────────────────────────────────────┐
  │  Profile        stakeholder category · institution │
  │                 posting pattern · age/gender cues  │
  │                 live-in/live-out status            │
  ├─────────────────────────────────────────────────┤
  │  Activities     urban activities · physical urban  │
  │  and Issues     problems · socioeconomic problems  │
  │                 focus area · issue type            │
  ├─────────────────────────────────────────────────┤
  │  Emotion        sentiment class · emotional signal │
  │                 engagement measures                │
  ├─────────────────────────────────────────────────┤
  │  Context        place name · street/zone level     │
  │                 temporal fields · project phase    │
  │                 multimedia information             │
  └─────────────────────────────────────────────────┘
                    ↓
  Interpretation control and traceability
  evidence · justification · confidence · review flag · source linkage
                    ↓
  Enriched social record / derived social representations
```

The workflow is kept separate from the field structure: the four dimensions define **what** information is produced, the workflow defines **how** processing is carried out. The source record is maintained throughout rather than replaced by the generated interpretation.

## Operational processing stages

### Stage 1 — Input verification and source-record linkage
Verifies structure and completeness of prepared records: record identifier, platform, normalised timestamp, body text, author, hashtags, place information, engagement fields, matched keywords, source reference. Missing information, incomplete records, duplicated references, and cases requiring review are flagged. The connection between each prepared record and its original source is confirmed. Contextual documents, taxonomy resources, stakeholder information, and spatial references are then supplied as bounded inputs.

### Stage 2 — AI-assisted social interpretation
The researcher-defined analytical schema is applied to verified records, generating the fields of the four dimensions. **Where available evidence does not support a classification, the field remains unclear or unavailable, or is flagged for review, rather than being completed through unsupported inference.**

### Stage 3 — Interpretation control and traceability
Interpretive outputs are checked before assembly. Inferred classifications carry justification and confidence. Ambiguous, weakly supported, or sensitive interpretations are flagged. Cross-field consistency is examined — for instance, an institutional account should not receive an unsupported individual demographic classification; an inferred place should be supported by spatial evidence; an emotional signal should correspond to evidence in the content.

### Stage 4 — Enriched record assembly and export
Preserved source data, mechanically derived attributes, and inferred interpretations are combined into the enriched social record.

## Spatial enrichment

Spatial enrichment connects social interpretations to a physical and environmental backbone: buildings, streets, facilities, land use, locations, addresses, and available environmental or mobility information. Where a streamed photorealistic 3D environment is used, it provides visual context only; the documented GIS datasets remain the analytical spatial reference.

Enrichment is applied **according to the level of evidence available in each record**. Where sufficient place information exists, records are linked to the corresponding zone, street, or facility through join tables — one row per record–place pair. Where it does not, no link is forced.

In the reference implementation, explicit platform place metadata was available for 28.8% of records. The remainder rest on textual place references and hashtags matched against project location resources. These two categories must not be treated as equivalent in precision.

## Implementation

Implemented on the same browser-based foundation as Agent 1: a self-contained single HTML file running without installation. The browser application provides the execution environment; the analytical procedure remains defined by the researcher-controlled specification and operational prompts.

**Inputs to the application:**
1. The prepared cross-platform CSV from Agent 1.
2. The multi-layer keyword taxonomy, used particularly during activities and issues classification.
3. Contextual reference materials — surveys, project documents, census information, stakeholder material, and other local sources required to support interpretation. Multiple files can be uploaded, so the researcher defines exactly what evidence is available to the agent.

**Configurable parameters:**
- **Batch size** — records per API call. In development, 5–15 posts per call. Controls transfer, not the analytical schema.
- **Processing scope** — full dataset, or a test sample of the first 10, 25, 50, or 100 records.

The tool is designed to be reusable beyond its original case study. Project-specific contextual materials, the keyword taxonomy, spatial references, and other case parameters can be replaced with equivalent resources from another urban project while maintaining the same processing structure.

## Development approach

Early tests examined the behaviour of the four operational prompts on small record groups: category clarity, output consistency, missing fields, unsupported classifications, and the evidence required for inferred values. Particular attention went to profile-related cues, multi-label activities and issues, the distinction between sentiment and emotional signals, and spatial assignments resting on indirect evidence.

Prompts were revised where testing revealed unclear instructions or inconsistent outputs. Refinements clarified when values should remain uncertain, strengthened evidence requirements, allowed multiple activities or issues where needed, and required justification notes and confidence scores for inferred fields.

Testing continued until prompt structure and output schema were sufficiently stable for full execution.

## Reference implementation outcome

Final dataset: **753 records × 70 fields**.

Consistency checking at this stage confirmed the expected field structure was produced and the analytical status of each field remained identifiable: observed information linked to original records, derived variables following predefined rules, inferred fields retaining confidence and justification for later review.

## Limitations

**Interpretive ceiling.** Short, ambiguous, ironic, or strongly context-dependent posts resist confident classification. Structured interpretation reduces but cannot eliminate ambiguity in semantic fields.

**Evidence asymmetry.** Text-rich posts provide a stronger basis for interpretation than records whose meaning depends on images, video, speech, or other multimedia. This is particularly relevant for video-oriented platforms where textual metadata may represent only part of the original content.

**Spatial precision.** Explicit location metadata is not available for all records; some assignments depend on textual or contextual evidence. These must be interpreted according to the level of spatial evidence available and not treated as equivalent to explicitly geotagged records.

**Profile inference.** Demographic cues and residence status are limited to the evidence in the record and are not verified personal characteristics.

**Model dependence.** Even with identical specification and prompts, interpretive outputs may vary between executions or model versions. Prompt versions, model information, confidence scores, justification notes, and review flags are retained as part of the traceability procedure.

**Cost and scale.** API-based processing introduces execution time, cost, and scalability constraints that grow with the number of records and the volume of contextual material.

## Contents of this directory

| File | Contents |
|---|---|
| [`specification.md`](specification.md) | Agent-level seven-component specification |
| [`prompts/prompt-1-profile-classification.md`](prompts/prompt-1-profile-classification.md) | Full runnable prompt: 11 tasks, complete actor taxonomy, admissible/inadmissible evidence tables for demographic cues, posting-pattern thresholds, 7 validation checks |
| [`prompts/prompt-2-activities-and-issues.md`](prompts/prompt-2-activities-and-issues.md) | Full runnable prompt: 18 tasks, all five category sets enumerated with scope definitions, motivation-field framing constraint |
| [`prompts/prompt-3-emotion-and-sentiment.md`](prompts/prompt-3-emotion-and-sentiment.md) | Full runnable prompt: sentiment rules for irony, quoted speech and mixed; 15 emotional signals grouped by family; engagement derivation |
| [`prompts/prompt-4-contextual-annotation.md`](prompts/prompt-4-contextual-annotation.md) | Full runnable prompt: 6-rank spatial evidence hierarchy, no-precision-inflation rule, mechanical temporal derivations |
| [`schemas/README.md`](schemas/README.md) | Metadata conventions and null semantics |
| [`schemas/dimension-1-profile.md`](schemas/dimension-1-profile.md) | 15 fields, full metadata |
| [`schemas/dimension-2-activities-and-issues.md`](schemas/dimension-2-activities-and-issues.md) | 19 fields, full metadata |
| [`schemas/dimension-3-emotions-and-sentiment.md`](schemas/dimension-3-emotions-and-sentiment.md) | 11 fields, full metadata |
| [`schemas/dimension-4-context.md`](schemas/dimension-4-context.md) | 16 fields, full metadata |
| [`schemas/provenance-fields.md`](schemas/provenance-fields.md) | 9 fields plus run-level structures |
| [`schemas/enriched-record.schema.json`](schemas/enriched-record.schema.json) | **Machine-readable JSON Schema** — 67 properties, 11 coherence constraints |
| [`schemas/controlled-vocabularies.json`](schemas/controlled-vocabularies.json) | **All 22 closed lists**, flagged portable or case-specific |
| [`validation.md`](validation.md) | Quality control procedure |
