# 5. Reference Implementation

This document records the single case on which the specifications were developed and tested. It is provided so that the reported reliability figures can be read against the conditions that produced them.

**The reference implementation is not a validation of transferability.** The specifications are designed to be portable — keyword taxonomy, spatial reference tables, stakeholder framework, and contextual documents are all replaceable parameters — but portability has not been empirically tested on a second case.

## Case conditions

| Dimension | Value |
|---|---|
| Setting | A social housing district undergoing a multi-year urban renewal programme |
| Language of corpus | Primarily French |
| Platforms | Instagram, X/Twitter, TikTok |
| Collection method | Browser-extension capture during structured browsing |
| Collection sessions | Two sessions, approximately two hours each |
| Search queries | 60 |
| Temporal span of corpus | 2011–2025 |

## Contextual grounding

| Source type | Count | Contribution |
|---|---|---|
| Documentary corpus for keyword derivation | 9 documents, ~3.5M characters | Project identity, spatial references, thematic terms, actor references, temporal markers |
| Perception survey | 124 respondents | Citizen vocabulary, issue framing, triangulation reference |
| Citizen validation survey | 64 respondents | Validation of derived patterns |
| Facilities inside the study area | 34 | Spatial reference for place matching |
| Inside and surrounding services | 208 | Broader spatial reference list |

## Keyword taxonomy outcome

**243 keywords across six layers.**

The frequency distribution was uneven: a small group of highly recurrent terms alongside a larger number of specific spatial, thematic, social, and event-related references. Nine keywords reached the 500+ occurrence tier, concentrated on the renewal programme, housing transformation, actors, and mobility.

The distribution shows the documentary corpus is most strongly structured around the formal renewal process and its main interventions, while the remaining layers extend this vocabulary toward specific places, activities, actors, and project events. Some terms appear across more than one layer, reflecting connections between thematic, social, actor, and temporal dimensions.

**Interpretive caution.** A documentary corpus is institutional by nature. A taxonomy derived primarily from it will over-represent formal project terminology and under-represent resident vocabulary. This is why L4 was populated principally from survey and qualitative material rather than from planning documents, and why the imbalance is reported rather than smoothed.

## Record lineage

```
  5,177   raw capture (843 Instagram · 2,420 X/Twitter · 1,914 TikTok)
  4,690   unique after content-based deduplication (487 removed)
  796–810 baseline relevant set (varies by run)
    769   after four iterative precision tests (41 false positives removed)
    753   after human revision (16 removed) — AUTHORITATIVE
    753 × 70   enriched social layer
```

See [`03-reproducibility.md`](03-reproducibility.md) §3.4 for the full account, including a note on an earlier deduplication pass using a different key.

## Spatial structure

| Element | Count |
|---|---|
| Zones | 9 |
| Streets | 18 |
| Facilities | 25 |

Records are linked through three join tables, one row per record–place pair.

Explicit platform place metadata was available for **28.8%** of records. The remainder rest on textual place references and hashtags matched against project location resources. These are not equivalent in precision.

## Platform relevance rates

| Platform | Raw | Retained | Rate |
|---|---|---|---|
| Instagram | 843 | 404 | 47.9% |
| X/Twitter | 2,062 unique | 373 | 18.1% |
| TikTok | 1,914 | 19 | 1.0% |

The sharp asymmetry is a finding about the **method**, not about the platforms. Hashtag- and location-based browsing on Instagram allowed project and place terms to guide collection effectively. Textual content on X/Twitter allowed direct taxonomy application but generated ambiguity requiring disambiguation. The near-zero TikTok rate reflects the limits of text-based filtering on a video-first platform, where meaning is carried by speech, imagery, and on-screen text absent from available metadata.

**The TikTok result must not be read as evidence that relevant content is absent from that platform.**

## Execution environment

| Element | Value |
|---|---|
| Execution engine | Anthropic Claude Opus models |
| Development period | June 2025 – June 2026, with re-execution across successive releases |
| API key | User-supplied, session-only, not persistently stored |
| Interface | Self-contained single-file browser applications |
| Prototyping environments (superseded) | Workflow-automation and visual agent-builder platforms |

Results reported were produced using the model available at the time of final analysis. The prototyping environments were useful for exploring sequencing and prompt behaviour on samples of 10–20 records but showed greater output variability across repeated executions; stability became a selection criterion for the final configuration.

## Spatial and 3D context

The analytical spatial reference is a documented GIS dataset. Where a streamed photorealistic 3D environment is used, it provides **visual context only** and is not part of the analytical chain. Streamed 3D services are referenced, not stored, with metadata retained in the database.

## What this implementation establishes and does not

**Establishes:** that the specifications execute; that the pipeline produces a structurally valid enriched layer; that observed, derived, and structurally inferred fields reproduce fully; that semantic fields vary in a measurable and characterisable way.

**Does not establish:** that the reported reliability figures apply to other cases, languages, platforms, or urban contexts. Anyone adapting this method should re-run the validation procedures rather than inherit these numbers.
