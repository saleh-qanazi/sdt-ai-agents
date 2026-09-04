# Agent 1 — Output Schemas

Metadata conventions follow [`../../02-social-interpretation-agent/schemas/README.md`](../../02-social-interpretation-agent/schemas/README.md).

---

## 1. `KeywordTaxonomy.csv`

Output of Prompt 1. One row per canonical term.

| # | Field | Status | Type | Card. | Nullable | Description |
|---|---|---|---|---|---|---|
| 1 | `keyword_id` | Derived | string | 1 | No | `K0001` zero-padded, stable across runs |
| 2 | `term` | Observed | string | 1 | No | Canonical form; lowercase except proper nouns |
| 3 | `variants` | Derived | string[] | 0..n | Yes | Pipe-separated observed spelling variants |
| 4 | `layer` | Inferred-structural | enum | 1 | No | `L1`–`L6` |
| 5 | `secondary_layers` | Inferred-structural | enum[] | 0..n | Yes | Where a term genuinely functions in several roles |
| 6 | `theme` | Inferred-structural | enum | 1 | No | Closed list below |
| 7 | `polarity` | Inferred-semantic | enum | 1 | No | `positive`·`negative`·`neutral`·`not_applicable` |
| 8 | `concept_id` | Inferred-semantic | string | 0..1 | Yes | `C0001`; shared by synonyms. **Never shared by antonyms** |
| 9 | `frequency` | **Derived** | integer | 1 | No | Corpus-level, variants aggregated. 100% reproducible |
| 10 | `frequency_by_document` | **Derived** | string | 1 | No | `doc_id:count` pairs, pipe-separated |
| 11 | `weight_tier` | **Derived** | enum | 1 | No | `tier_1`–`tier_5` |
| 12 | `source_document` | Observed | string[] | 1..n | No | Pipe-separated document identifiers |
| 13 | `source_category` | Observed | enum[] | 1..n | No | Document categories |
| 14 | `researcher_supplied` | Observed | boolean | 1 | No | From the manual list |
| 15 | `is_ambiguous` | Inferred-semantic | boolean | 1 | No | Could denote something outside the study area |
| 16 | `ambiguity_note` | Inferred-semantic | string | 0..1 | Yes | The competing referent |
| 17 | `layer_conflict_note` | Provenance | string | 0..1 | Yes | Agent's proposal where the researcher overrode it |

**`layer` values**

| Value | Definition | Role in filtering |
|---|---|---|
| `L1` | Institutional and project identity | **Anchor layer.** Tier 1 relevance requires an L1 match |
| `L2` | Spatial and place-based | Tier 2 requires L2 **plus** contextual terms |
| `L3` | Project components and themes | Contextual only; never establishes location |
| `L4` | Citizen vocabulary and lived experience | Contextual only |
| `L5` | Actors and institutions | Contextual only |
| `L6` | Temporal and event-based | Contextual only |

**`theme` values:** `housing` · `public_space` · `mobility` · `facilities` · `environment` · `safety` · `services` · `governance` · `social` · `economy` · `none`

**`weight_tier` thresholds:** `tier_1` ≥500 · `tier_2` 100–499 · `tier_3` 20–99 · `tier_4` 5–19 · `tier_5` 1–4

**Integrity constraints**

| ID | Constraint |
|---|---|
| K1 | `frequency` equals the sum of `frequency_by_document` |
| K2 | No `concept_id` groups terms whose `polarity` is both `positive` and `negative` |
| K3 | At least one row has `layer = L1`, else downstream filtering cannot anchor |
| K4 | `keyword_id` values unique and sequential |
| K5 | Every row has a `source_document` **or** `researcher_supplied = true` |

---

## 2. `PreparedSocialRecord_<platform>_filtered.csv`

Output of Prompt 2. All original columns preserved, plus thirteen.

| Field | Status | Type | Nullable | Description |
|---|---|---|---|---|
| `relevance_flag` | Inferred-structural | enum `yes`·`no` | No | Filtering decision |
| `relevance_confidence` | Inferred-structural | enum `high`·`medium`·`low`·`none` | No | Tier-derived |
| `relevance_tier` | Inferred-structural | enum `tier_1`·`tier_2`·`none` | No | Which rule was satisfied |
| `matched_keywords` | Observed | string | Yes | `keyword_id:term` pairs |
| `matched_layers` | Derived | enum[] | Yes | Distinct layers matched |
| `match_segments` | Derived | enum[] | Yes | `body`·`hashtag`·`place`·`author`·`bio` |
| `author_only_match` | Inferred-structural | boolean | No | Match occurred only in author or bio; insufficient for relevance |
| `weak_anchor` | Inferred-structural | boolean | No | Ambiguous term with neither competing nor local reference |
| `disambiguation_applied` | Inferred-structural | boolean | No | Geographic test was run |
| `disambiguation_note` | Inferred-semantic | string | Yes | Competing and local terms found |
| `exclusion_reason` | Inferred-structural | enum | No | Closed list below |
| `exclusion_evidence` | Observed | string | Yes | The specific text triggering exclusion |
| `filter_prompt_version` | Provenance | string | No | Specification version |

**`exclusion_reason` values**

| Value | Meaning |
|---|---|
| `none` | Retained |
| `no_keyword_match` | No taxonomy term matched |
| `spatial_reference_only` | L2 present with no contextual term |
| `thematic_only_no_anchor` | L3–L6 terms with no L1 or L2 anchor |
| `geographic_ambiguity` | Ambiguous term with a competing referent and no local anchor |
| `commercial_content` | Promotional with no project connection |
| `author_only_match` | Match confined to author or bio segment |

---

## 3. `PreparedSocialRecord_<platform>.csv`

Output of Prompt 3.

| Field | Status | Type | Nullable | Notes |
|---|---|---|---|---|
| `prepared_record_id` | Derived | string | No | `P_<platform>_00001` |
| `platform` | Observed | enum | No | |
| `source_record_id` | Observed | **string** | No | Never float — precision loss causes collisions |
| `original_text` | Observed | text | No | **Immutable.** Written once, never altered |
| `cleaned_text` | Derived | text | No | Normalised working text |
| `original_timestamp` | Observed | string | No | Raw, as exported |
| `normalized_timestamp` | Derived | datetime | Yes | `YYYY-MM-DD HH:MM` UTC. Empty on parse failure |
| `collection_timestamp` | Observed | datetime | Yes | Capture time — **distinct from publication time** |
| `timestamp_parse_error` | Inferred-structural | boolean | No | |
| `language` | Inferred-structural | string | No | ISO 639-1 or `und` |
| `language_confidence` | Inferred-structural | enum | No | `high`·`medium`·`low` |
| `multilingual` | Inferred-structural | boolean | No | |
| `author` | Observed | string | No | Unmodified |
| `hashtags` | Observed | string[] | Yes | Unmodified |
| `place_metadata` | Observed | string | Yes | Platform-supplied location |
| `like_count` | Observed | integer | Yes | **Empty ≠ zero** |
| `comment_count` | Observed | integer | Yes | **Empty ≠ zero** |
| `share_count` | Observed | integer | Yes | **Empty ≠ zero** |
| `quote_count` | Observed | integer | Yes | **Empty ≠ zero** |
| `media_type` | Observed | string | Yes | |
| `url` | Observed | string | Yes | Link to original post |
| `content_key` | Derived | string | No | Deduplication key |
| `duplicate_status` | Inferred-structural | enum | No | `original`·`unique` |
| `duplicate_count` | Derived | integer | No | Copies suppressed |
| `cross_author_repetition` | Inferred-structural | boolean | No | Same content, different author — **not** a duplicate |
| `review_required` | Inferred-structural | boolean | No | |
| `review_reason` | Inferred-structural | string | Yes | |
| `clean_prompt_version` | Provenance | string | No | |

---

## 4. `UnifiedCrossPlatform.csv`

Output of Prompt 4. **The handover artefact to Agent 2.**

| Field | Status | Type | Nullable | Notes |
|---|---|---|---|---|
| `post_id` | Derived | string | No | Pipeline-unique |
| `platform` | Observed | enum | No | |
| `source_record_id` | Observed | string | No | |
| `prepared_record_id` | Observed | string | No | |
| `timestamp_final` | Derived | datetime | Yes | Standardised publication time |
| `body` | Derived | text | No | Cleaned text |
| `original_text` | Observed | text | No | Immutable |
| `author` | Observed | string | No | |
| `hashtags` | Observed | string[] | Yes | |
| `place` | Observed | string | Yes | Platform place metadata |
| `like_count` | Observed | integer | Yes | Empty where unsupported |
| `comment_count` | Observed | integer | Yes | Empty where unsupported |
| `share_count` | Observed | integer | Yes | Empty where unsupported |
| `quote_count` | Observed | integer | Yes | Empty where unsupported |
| `media_type` | Observed | string | Yes | |
| `url` | Observed | string | Yes | |
| `language` | Inferred-structural | string | No | |
| `matched_keyword` | Observed | string | Yes | |
| `relevance_confidence` | Inferred-structural | enum | No | |
| `cross_posted` | Inferred-structural | boolean | No | Same content, same author, two platforms |
| `cross_post_group_id` | Derived | string | Yes | Links cross-posted records |
| `review_required` | Inferred-structural | boolean | No | Carried forward |
| `integration_run_id` | Provenance | string | No | |

**Deliberate exclusions.** No derived engagement measure, no sentiment, no actor classification, no spatial hierarchy. Those belong to Agent 2, and computing them here would collapse the separation between rule-based preparation and interpretation on which the reliability distinctions rest.

---

## 5. Audit outputs

### `exclusion_audit.csv`
`source_record_id` · `platform` · `exclusion_reason` · `exclusion_evidence` · `matched_keywords` · `original_text_excerpt` (200 chars)

### `deduplication_log.csv`
`suppressed_record_id` · `retained_record_id` · `content_key` · `key_type` (`shortcode`·`normalised_text`) · `suppressed_timestamp` · `retained_timestamp` · `author_match`

### `cross_platform_dedup_log.csv`
`suppressed_post_id` · `retained_post_id` · `match_basis` · `platforms_involved` · `authors_involved`

These are not optional by-products. **A removal that cannot be explained by an explicit rule with recorded evidence is a defect**, and these files are what make that checkable.
