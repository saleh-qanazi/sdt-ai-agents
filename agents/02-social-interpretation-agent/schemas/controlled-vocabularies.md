# Controlled Vocabularies

Every closed list in the enriched record, gathered for validation use. A value outside these lists is a **structural defect**, not an edge case.

Each list is marked for **transferability**: whether it can be reused in another context, or must be rebuilt from that context's own materials.

---

## Analytical status (schema-level)

`Observed` · `Derived` · `Inferred — structural` · `Inferred — semantic` · `Interpretation — contextual` · `Control`

**Transferable:** yes — this is the framework's own vocabulary.

---

## Confidence (all confidence fields)

`high` · `medium` · `low`

**Transferable:** yes.

---

## Dimension 1 — Profile

### `stakeholder_category`
`Institutional & Governance` · `Design & Technical Teams` · `Community Stakeholders` · `Funding & Strategic Partners` · `Academic` · `Media` · `Unclear`

**Provenance:** project stakeholder framework; `Media` added from the corpus.
**Transferable:** partially — the structure travels, the specific groups must be rebuilt from the target project's stakeholder analysis.

### `posting_pattern`
`frequent` · `regular` · `occasional` · `rare` · `single_post`

**Provenance:** researcher-defined thresholds.
**Transferable:** yes, with thresholds restated.

### `possible_age_cue`
`<15` · `16–24` · `25–34` · `35–44` · `45–60` · `60–70` · `70+` · `unclear` · `not_applicable_institutional`

**Provenance:** perception survey bracket definitions.
**Transferable:** no — use the target study's own survey brackets.

### `possible_gender_cue`
`possible_male` · `possible_female` · `unclear` · `not_applicable_institutional`

**Transferable:** yes, with the same cultural-dependence caveat.

### `live_in_live_out_status`
`likely_local_resident` · `possibly_local` · `works_in_area` · `visitor` · `external_observer` · `external_partner` · `unclear`

**Transferable:** yes.

---

## Dimension 2 — Activities and Issues

### `urban_activities`  *(multi-label)*
`Housing` · `Health services` · `Early childhood` · `Education` · `Other public services` · `Public transport` · `Retail & shopping` · `Community spaces` · `Sport services` · `Entertainment` · `Other services`

**Provenance:** project and survey material + activity vocabulary in the keyword taxonomy.
**Transferable:** largely — these are generic urban service domains, but check coverage against the target context.

### `physical_urban_problems`  *(multi-label)*
`Infrastructure/Buildings` · `Transportation/Congestion` · `Air quality` · `Green spaces` · `Noise` · `Construction disruption` · `Waste/Cleanliness` · `Energy/Thermal`

**Provenance:** physical and environmental layers of the keyword taxonomy + case sources.
**Transferable:** largely.

### `socioeconomic_problems`  *(multi-label)*
`Economic/Poverty` · `Security/Safety` · `Health problems` · `Social isolation` · `Social equity` · `Housing precarity` · `Education/Youth` · `Community disruption`

**Provenance:** socioeconomic vocabulary of the keyword taxonomy + survey material.
**Transferable:** largely.

### `focus_area`  *(multi-label)*
`Positive Health Neighbourhood` · `Accessibility & Opening to the North` · `Diverse & High-Quality Housing` · `Economic Activity & Quality Public Services` · `Improving Residents' Social Conditions`

**Provenance:** the renewal programme's five strategic pillars.
**Transferable: NO.** These are one programme's specific objectives. Substitute the target programme's own focus areas.

### `issue_type`  *(multi-label)*
`physical` · `environmental` · `social` · `safety` · `mobility` · `health` · `service-related` · `none`

**Transferable:** yes. Note this field is **deterministically derived** from the two problem fields; see the mapping in the dimension file.

### `keyword_taxonomy_layer`
`L1` · `L2` · `L3` · `L4` · `L5` · `L6`

**Transferable:** yes — the six-layer structure is part of the method.

### `keyword_frequency_weight`
`tier_1` (≥500) · `tier_2` (100–499) · `tier_3` (20–99) · `tier_4` (5–19) · `tier_5` (1–4)

**Transferable:** yes, though tier boundaries should be checked against the target corpus size.

---

## Dimension 3 — Emotions and Sentiment

### `sentiment_class`
`positive` · `neutral` · `negative` · `mixed`

**Provenance:** established four-way polarity structures in sentiment-analysis tasks.
**Transferable:** yes.

### `emotional_signal`
`pride/attachment` · `hope` · `gratitude/satisfaction` · `joy/celebration` · `fear/insecurity` · `frustration/exasperation` · `anger/indignation` · `sadness/disappointment` · `concern/anxiety` · `neglect/abandonment` · `disgust/degradation` · `general_positive` · `general_negative` · `mixed` · `none`

**Provenance:** established emotion-analysis lexicons + environmental-psychology research on people–place affect.
**Transferable:** yes — the place-related signals in particular are grounded in general research rather than this case.

### `engagement_level`
`viral` (≥500) · `very_high` (100–499) · `high` (50–99) · `moderate` (20–49) · `low` (5–19) · `minimal` (1–4) · `no_engagement` (0)

**Provenance:** study-specific operational intervals.
**Transferable: thresholds NO, structure yes.** There is no established agreement on universal engagement thresholds. Set your own against your corpus and declare them.

---

## Dimension 4 — Context

### `place_evidence_type`
`metadata` · `facility_name` · `street_name` · `hashtag` · `zone_name` · `indirect` · `none`

**Transferable:** yes.

### `time_of_day`
`early_morning` (06:00–07:59) · `morning` (08:00–11:59) · `midday` (12:00–13:59) · `afternoon` (14:00–16:59) · `evening` (17:00–19:59) · `late_evening` (20:00–22:59) · `night` (23:00–05:59)

**Provenance:** researcher-defined descriptive intervals.
**Transferable:** structure yes; intervals are not universal behavioural thresholds.

### `day_of_week`
`Monday` · `Tuesday` · `Wednesday` · `Thursday` · `Friday` · `Saturday` · `Sunday`

### `project_phase`
`pre_NPNRU` · `Phase_1` · `Phase_2` · `post_NPNRU`

**Transferable: NO.** Substitute the target programme's timeline phases.

### `repost_or_repetition_indicator`
`retweet` · `highly_shared` (≥1000) · `widely_shared` (100–999) · `moderately_shared` (10–99) · `shared` (1–9) · `original/not_shared`

**Transferable:** structure yes; thresholds are study-specific.

### `has_multimedia`
`yes` · `no`

### `multimedia_type`
`photo` · `carousel` · `video` · `mixed_media` · `animated_gif` · `text_only`

**Transferable:** yes, subject to what the target platforms provide.

---

## Agent 1 vocabularies

### Keyword taxonomy `layer`
`L1` Institutional and project identity · `L2` Spatial and place-based · `L3` Project components and themes · `L4` Citizen vocabulary and lived experience · `L5` Actors and institutions · `L6` Temporal and event-based

### Keyword `theme`
`housing` · `public_space` · `mobility` · `facilities` · `environment` · `safety` · `services` · `governance` · `social` · `economy` · `none`

### Keyword `polarity`
`positive` · `negative` · `neutral` · `not_applicable`

### `relevance_tier`
`tier_1` (L1 term present) · `tier_2` (L2 term + 1–2 context terms) · `none`

### `exclusion_reason`
`commercial_content` · `geographic_ambiguity` · `insufficient_evidence` · `no_text_content` · `duplicate_in_batch`

### `ambiguity_outcome`
`excluded` · `retained_with_anchor` · `retained_no_competitor` · `discounted` · `not_applicable`

### `duplicate_status`
`unique` · `retained_representative` · `removed_duplicate`

### `dedup_method`
`string_id` · `content_hash`

> `numeric_id` is **not** a permitted value. Floating-point storage of platform numeric identifiers in CSV exports loses precision and produces collisions.

---

## Transferability summary

| Must be rebuilt for a new context | Transfers with threshold restatement | Transfers directly |
|---|---|---|
| `focus_area` | `engagement_level` | `sentiment_class` |
| `project_phase` | `repost_or_repetition_indicator` | `emotional_signal` |
| `possible_age_cue` brackets | `time_of_day` | `place_evidence_type` |
| `stakeholder_category` groups | `posting_pattern` | `live_in_live_out_status` |
| The keyword taxonomy itself | `keyword_frequency_weight` | `issue_type` |
| Spatial reference tables | | Analytical status and confidence |

The lists in the left column encode a specific programme and a specific survey. Reusing them elsewhere would import that case's assumptions under the appearance of a general schema.
