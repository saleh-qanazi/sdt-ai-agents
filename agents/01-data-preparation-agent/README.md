# Agent 1 — Data Preparation Agent

**Rule-based, non-interpretive, instruction-governed.**

## Role

The Data Preparation Agent operates according to fixed researcher-defined rules — a keyword taxonomy and explicit filtering criteria — to identify and structure relevant records. It transforms data from the source and contextual layer into the prepared-records layer.

It does **not** interpret social meaning, assign sentiment or other social attributes, or make social judgements. Those tasks belong to Agent 2. This functional separation is what allows different reliability levels to be assigned to different fields downstream.

Preparation is necessary because social media exports differ considerably across platforms in structure, field definitions, formats, and relevance. The agent addresses filtering, cleaning, normalisation, structuring, and cross-platform interoperability before any interpretation begins.

## Architecture

```
  PDFs (project documents) + manual keyword list
                    ↓
  Keyword extraction and consolidation → KeywordTaxonomy.pdf
                    ↓
  Platform CSVs (X.csv, Instagram.csv, TikTok.csv)
                    ↓
  Relevance filtering (per platform)
                    ↓
  Cleaning and normalisation (per platform)
                    ↓
  Platform-specific prepared outputs
  (PreparedSocialRecord_[X, Instagram, TikTok].csv)
                    ↓
  Cross-platform harmonisation → UnifiedCrossPlatform.csv
```

The agent is implemented as a single instruction-driven entity, but its operation follows a defined multi-stage workflow. The stages are organised within the instruction schema and are **distinct from** the seven components of the specification schema.

## Inputs and outputs

**Inputs**
- Platform data files: CSV, or NDJSON exported directly from the capture tool, for X/Twitter, Instagram, and TikTok
- Documentary sources in PDF (planning documents, project reports, institutional communication, spatial materials)
- A manual keyword list supplied by the researcher

**Outputs**
- A cleaned CSV per platform
- A merged cross-platform CSV with a common field set
- A structured keyword taxonomy PDF with per-layer frequency counts
- A machine-readable quality-assurance report
- A processing audit log
- Retained intermediate datasets: keyword tables, relevance-flagged files, cleaned files before merging

Intermediate files are retained deliberately. They preserve each processing step and are what make the pipeline auditable rather than merely repeatable.

## The six processing stages

### Stage 1 — Document-informed keyword identification
The agent analyses project-related PDFs to identify recurring keywords: place names, actors, programme names, intervention components, thematic terms. This creates a keyword base reflecting the thematic and spatial context of the case, ensuring filtering is informed by institutional and territorial documentation rather than by platform signals alone.

### Stage 2 — Keyword consolidation and normalisation
Document-derived keywords are combined with researcher-defined terms into a consolidated search vocabulary. The agent assists in identifying duplicates, overlaps, synonyms, and variants. **Final consolidation and validation are carried out by the researcher** to maintain contextual accuracy and prevent inappropriate semantic merging.

Normalisation operates at three levels:

| Level | Handles | Example |
|---|---|---|
| Lexical | Spelling variants, singular/plural, accents | *bruit* / *bruits* |
| Semantic | Synonyms and closely related expressions for the same issue | *bruit*, *nuisance sonore*, *noise* → one concept |
| Thematic | Grouping under broader categories | mobility, housing, public space, environment, safety |

Terms with opposite meanings — *insécurité* and *sécurité* — are kept separate. Merging them would erase the evaluative distinction the analysis depends on.

### Stage 3 — Relevance-based filtering
The consolidated keyword set is applied to platform datasets using a two-tier classification:

| Tier | Condition | Confidence |
|---|---|---|
| **Tier 1** | A direct project-identity term (Layer 1) appears in the textual content | High |
| **Tier 2** | A spatial reference (Layer 2) appears together with one or two contextual terms from the remaining layers | Medium |

**A spatial reference alone is not sufficient.** This anchor-and-context logic is the core precision control.

Two additional filters apply at this stage:

**Geographic disambiguation.** Where a project keyword is also a well-known toponym elsewhere, a co-occurrence rule applies: a record is excluded when the ambiguous term appears with references specific to the *other* location and no local anchor is present. The term cannot simply be removed from the keyword set, because it is also a primary project identifier. In the reference implementation, the term *Concorde* also denotes a major Paris landmark; records combining it with Paris-specific references (*Champs-Élysées*, *Trocadéro*, *Arc de Triomphe*) and no Lille-specific anchor (*Lille*, *Faubourg de Béthune*, *Boulevard de Metz*, local actors, project references) are excluded.

**Commercial-content noise filter.** Removes posts created mainly for advertising, promotion, or business visibility with no meaningful connection to resident experience, institutional communication, or project discussion. Identified through account type, promotional vocabulary, repeated advertising patterns, commercial hashtags, and absence of project connection.

### Stage 4 — Cleaning and normalisation of relevant records
Noise removal, whitespace and text-structure normalisation, language detection, duplicate elimination.

*Noise* here means elements that do not contribute to interpreting a post or that create false relevance: duplicated records, broken metadata, unrelated commercial content, platform-generated artefacts, isolated URLs, residual out-of-area references.

**Informal language, emotional expressions, and resident vocabulary are not noise.** They carry the social information the analysis exists to capture. The cleaning process does not alter the original meaning of content, and original identifiers, timestamps, and raw text are retained alongside the cleaned version.

### Stage 5 — Platform-specific dataset preparation
Cleaned records are organised into standardised platform-specific datasets. Field structure is harmonised through consistent naming and a common schema where possible, while platform provenance and analytically relevant platform-specific attributes are preserved.

### Stage 6 — Cross-platform dataset integration
Platform datasets are combined into a unified table. Common attributes are aligned; platform provenance and original identifiers are retained. **Cross-platform deduplication is applied only where exact duplication is identified**, since the same content legitimately appearing on two platforms is a finding, not an error.

## Multi-layer keyword taxonomy

The filtering vocabulary is organised into six layers. The layers are never used independently.

| Layer | Captures | Examples |
|---|---|---|
| **L1 — Institutional and project identity** | Formal references to the renewal project; the direct project anchor | *Projet Concorde*, *Renouvellement urbain Concorde*, *ANRU Concorde*, *NPNRU Faubourg de Béthune* |
| **L2 — Spatial and place-based** | Streets, facilities, landmarks, public spaces, housing blocks | *Rue Léon Blum*, *Square Frédéric Chopin*, *Ferme urbaine Concorde*, *Maison des Solidarités* |
| **L3 — Project components and themes** | Subjects and interventions: housing, public space, mobility, facilities, environment, services | *espaces verts*, *logements sociaux*, *réhabilitation*, *relogement*, *mobilité douce* |
| **L4 — Citizen vocabulary and lived experience** | How residents describe everyday experience, concerns, evaluations | *insécurité*, *bruit*, *isolement*, *fierté du quartier*, *pas consultés* |
| **L5 — Actors and institutions** | Institutions, associations, community actors | *Ville de Lille*, *MEL*, *bailleur social*, *collectif habitants* |
| **L6 — Temporal and event-based** | Events and project phases generating visibility | *réunion publique*, *chantier*, *travaux*, *inauguration*, *avant/après* |

**Combination logic.** At least one L1 keyword anchors the query to the project, with one or two terms from L2–L6 added where relevant. Frequency weights prioritise recurrent project and thematic terms while retaining lower-frequency keywords for targeted searches.

**Ambiguous place names.** L2 terms that are not unique to the study area are not treated as sufficient geographic evidence alone. They are combined with contextual anchors, and the geographic context of retrieved material is checked during filtering.

**Traceability of origin.** Project identifiers derive from official project documents; spatial references from project maps and geospatial data; thematic terms from planning and technical reports; citizen vocabulary from survey and qualitative material; actor references from institutional and governance documents; temporal terms from project timelines and event material.

**Iteration.** Keyword design is treated as iterative. Initial searches are reviewed to identify irrelevant results, overly generic terms, missing local references, and inadequately captured expressions. The taxonomy is then refined by removing or combining ambiguous terms and adding project-specific and resident-derived vocabulary during collection.

Reference implementation result: **243 keywords across six layers**, derived from nine documents totalling approximately 3.5 million characters. The frequency distribution was uneven — a small group of highly recurrent terms and a larger number of specific spatial, thematic, social, and event references. Nine keywords reached the 500+ occurrence tier, concentrated on the renewal programme, housing transformation, actors, and mobility.

## Integrated dataset structure

The unified cross-platform table retains fields identifiable across all three platforms:

| Field | Content |
|---|---|
| `platform` | Source platform |
| `post_id` | Platform record identifier |
| `timestamp_final` | Standardised publication time |
| `body` | Main textual content (tweet, caption, or video description) |
| `author` | Posting account |
| `hashtags` | Topical markers from the original content |
| `place` | Spatial references from the post or metadata |
| `like_count` / `comment_count` / `share_count` | Common engagement representation |
| `url` | Direct link to the original post, preserving the connection to source and multimedia |
| `language` | Detected language (ISO code) |
| `matched_keyword` | Keyword that triggered retention |
| `relevance_confidence` | Tier-based confidence value |

## Implementation

Developed from a researcher-defined specification translated into structured prompts, tested on sample data, refined, then implemented as a self-contained browser-based application distributed as a single HTML file requiring no installation.

The application manages file handling, prompt sequencing, API communication, transfer of outputs between steps, and generation of final files. The LLM executes the bounded tasks specified in the prompts; the processing rules, permitted inputs, workflow sequence, and required outputs remain defined by the specification.

The user supplies an API key, used for model communication during the active browser session and not persistently stored.

**On platform selection.** Early versions were prototyped using workflow-automation and visual agent-builder environments. These were useful for exploring sequencing, testing prompt behaviour, and refining task organisation on samples of 10–20 records. They produced acceptable results but showed greater variability across repeated executions. Since the framework requires the procedure to be repeatable and its remaining variability to be measurable, stability became a selection criterion. The final configuration also reduces dependence on external workflow platforms and gives greater control over prompts, sequence, input structures, and outputs — all of which could be integrated into the browser application and documented as part of the research instrument.

## Reference implementation outcomes

| Platform | Raw | Unique | Duplicates removed | Retained | Excluded | Relevance rate |
|---|---|---|---|---|---|---|
| Instagram | 843 | 843 | 0 | 404 | 439 | 47.9% |
| X/Twitter | 2,420 | 2,062 | 358 | 373 | 1,689 | 18.1% |
| TikTok | 1,914 | 1,914 | 0 | 19 | 1,895 | 1.0% |
| **Total** | **5,177** | **4,819** | **358** | **796** | **4,023** | **16.5%** |

*Note: these are the figures from the initial preparation pass. The subsequent validation campaign, using content-based deduplication keys, established 4,690 unique records (487 duplicates removed) and reduced the relevant set to 769 through iterative precision testing and then to 753 through human revision. See [`../../docs/03-reproducibility.md`](../../docs/03-reproducibility.md) §3.4 for the full lineage. Use content-based deduplication.*

**Why the rates differ so sharply.** Hashtag-based and location-related browsing on Instagram allowed L1 and L2 terms to guide collection toward the study area. On X/Twitter, much content is textual so the taxonomy applies directly, but the same terms appear in unrelated contexts, generating ambiguity handled at Stage 3.

The very low TikTok rate reflects a **limitation of the filtering method, not an absence of relevant content**. The taxonomy does not assign relevance on hashtags or isolated terms alone; it requires sufficient textual evidence and keyword combinations. On a video-first platform, relevant information may be communicated through speech, imagery, on-screen text, or the video itself and may never appear in the captions and hashtags available to the capture tool. Text-based filtering therefore has little evidence on which to decide.

## Limitations

**Model variation.** Fixed prompts and defined rules do not eliminate variation between executions. Mitigated through stable prompt wording, reduced-scale testing, and repeated checks before full execution.

**Filtering errors.** The process produces both false positives and false negatives. Rules deliberately avoid unsupported assumptions, so implicit references to the study area may be missed. Manual validation and the reliability assessment address these cases.

**Platform asymmetry.** The strategy depends on textual evidence and keyword combinations. It works adequately where sufficient textual or location information exists, and poorly on video-oriented platforms. A complete treatment of such platforms would require methods for analysing multimedia content directly.

**Context loss during cleaning.** Removing unnecessary elements can also remove context. Original records are preserved alongside processed data.

**Scale and cost.** Execution cost becomes significant with larger datasets on a paid API. Managed through small-sample testing, batching, and budget controls.

## Contents of this directory

| File | Contents |
|---|---|
| [`specification.md`](specification.md) | Agent-level seven-component specification |
| [`prompts/prompt-1-keyword-extraction.md`](prompts/prompt-1-keyword-extraction.md) | Full runnable prompt: 7 extraction steps, 6-layer classification with tie-breaks, 3-level normalisation, frequency weighting, 8 validation checks, edge-case table |
| [`prompts/prompt-2-relevance-filtering.md`](prompts/prompt-2-relevance-filtering.md) | Full runnable prompt: two-tier logic, non-qualifying patterns, geographic disambiguation matrix, commercial filter, 13 output fields, 7 validation checks |
| [`prompts/prompt-3-cleaning-normalisation.md`](prompts/prompt-3-cleaning-normalisation.md) | Full runnable prompt: noise definition with explicit do-not-remove list, timestamp failure patterns, content-based deduplication, 30 output fields |
| [`prompts/prompt-4-cross-platform-harmonisation.md`](prompts/prompt-4-cross-platform-harmonisation.md) | Full runnable prompt: schema alignment, empty-vs-zero rule, cross-post handling, 23 output fields |
| [`schemas/input-schema.md`](schemas/input-schema.md) | Required field mapping, document categories, study-area parameters, disambiguation matrix, known input defects |
| [`schemas/output-schema.md`](schemas/output-schema.md) | All four output files with per-field status, type, cardinality, nullability, and integrity constraints |
| [`validation.md`](validation.md) | Quality control and auditability procedure |
