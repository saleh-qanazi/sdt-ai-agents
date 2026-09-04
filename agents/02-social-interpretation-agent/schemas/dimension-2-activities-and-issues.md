# Dimension 2 — Activities and Issues

**19 fields. Questions addressed: what, and why where explicitly supported.**

Categories developed from activities, services, and concerns identified in project documents, perception survey material, and the multi-layer keyword taxonomy. Several fields are **multi-label**: one record can legitimately belong to several categories rather than being forced into one exclusive class.

---

## 2.1 `urban_activities`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum[] (multi-label) |
| Cardinality | 0..n — empty is valid |
| Companions | `activities_justification` |
| Provenance | Consolidated from project and survey material plus activity vocabulary in the taxonomy |

**Permitted values (closed list)**

| Value | Scope |
|---|---|
| `Housing` | Dwellings, housing conditions, rehousing, construction or demolition of homes, tenancy |
| `Health services` | Clinics, health centres, medical practices, health programmes |
| `Early childhood` | Nurseries, crèches, early years provision |
| `Education` | Schools, colleges, training, educational activity |
| `Other public services` | Town hall functions, social services, administration, post, policing services |
| `Public transport` | Metro, bus, tram, stops, routes, service quality |
| `Retail & shopping` | Shops, markets, commercial premises, local trade |
| `Community spaces` | Community centres, meeting places, association premises, shared gardens |
| `Sport services` | Sports facilities, pitches, gyms, sporting activity |
| `Entertainment` | Cultural events, festivals, leisure, performances |
| `Other services` | Not covered above; must be specified in the justification |

---

## 2.2 `activities_justification`

Text. Format: `Activity: evidence` pairs, pipe-separated. Every assigned activity must appear with its supporting words, phrases, hashtags, or contextual evidence.

---

## 2.3 `physical_urban_problems`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum[] (multi-label) |
| Cardinality | 0..n — **empty is common and valid** |
| Companions | `physical_problems_confidence`, `physical_problems_justification`, `physical_problems_motivation` |
| Provenance | Physical and environmental layers of the keyword taxonomy plus case-study sources |

**Permitted values**

| Value | Scope |
|---|---|
| `Infrastructure/Buildings` | Building condition, disrepair, structural problems, lifts, communal areas |
| `Transportation/Congestion` | Traffic, parking, congestion, road safety, access difficulty |
| `Air quality` | Pollution, fumes, dust, smells |
| `Green spaces` | Absence, loss, poor maintenance, or damage to vegetation and open space |
| `Noise` | Noise nuisance from any source |
| `Construction disruption` | Works, site nuisance, road closures, dust and noise attributed to construction |
| `Waste/Cleanliness` | Rubbish, dumping, litter, cleaning frequency |
| `Energy/Thermal` | Heating, insulation, cold, overheating, energy cost tied to building performance |

**Rule:** a record naming a facility is not thereby reporting a problem with it. Assign only where the record indicates something is wrong.

---

## 2.4 `physical_problems_confidence`

| Value | Assigned when |
|---|---|
| `high` | Explicit direct reference to the problem |
| `medium` | Clear indirect reference, or a described condition naming the domain without stating it as a problem |
| `low` | Inference from an indirect sign, hint, or single ambiguous word |

Indirect or ambiguous references are **retained at low confidence** rather than either discarded or treated as certain.

---

## 2.5 `physical_problems_justification`

Text. Keywords, phrases, or textual evidence for each assignment. Where a taxonomy term contributed, records the `keyword_id`.

---

## 2.6 `physical_problems_motivation`

| Attribute | Value |
|---|---|
| Status | **Interpretation — contextual** |
| Type | text |
| Nullable | Yes — empty where no relevant contextual finding exists |

Contextual explanation drawn from the perception survey or project documentation, relevant to the detected problem.

> **The most easily misread field in the schema.** It provides triangulation context **about the issue**. It does **not** state the author's motivation. Correct: "survey respondents reported noise as a leading concern on this axis." Incorrect: "the author is frustrated because of documented noise levels." Validation check V4 in Prompt 2 tests for this framing.

---

## 2.7 `socioeconomic_problems`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum[] (multi-label) |
| Cardinality | 0..n |
| Provenance | Socioeconomic vocabulary in the taxonomy, survey material, case-study context |

**Permitted values**

| Value | Scope |
|---|---|
| `Economic/Poverty` | Income, cost of living, unemployment, financial hardship |
| `Security/Safety` | Crime, feeling unsafe, antisocial behaviour, trafficking, violence |
| `Health problems` | Illness, health outcomes, access to care as a health rather than service issue |
| `Social isolation` | Loneliness, lack of contact, withdrawal |
| `Social equity` | Unequal treatment, discrimination, unfair allocation of resources or attention |
| `Housing precarity` | Insecure tenure, rehousing anxiety, overcrowding, eviction, unaffordability |
| `Education/Youth` | Youth provision, school quality, opportunities for young people, disengagement |
| `Community disruption` | Breakdown of social ties, displacement effects, loss of community fabric |

---

## 2.8 `socioeconomic_problems_confidence`

Same scale and rules as 2.4.

---

## 2.9 `socioeconomic_problems_justification`

Text, as 2.5.

---

## 2.10 `socioeconomic_problems_motivation`

Text. **Same constraint as 2.6** — context about the issue, never attributed personal motivation.

---

## 2.11 `focus_area`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | enum[] (multi-label) |
| Cardinality | 0..n |
| Provenance | **Programme framework — configuration, not universal categories** |

**Permitted values in the reference configuration**

| Value |
|---|
| `1. Positive Health Neighbourhood` |
| `2. Accessibility & Opening to the North` |
| `3. Diverse & High-Quality Housing` |
| `4. Economic Activity & Quality Public Services` |
| `5. Improving Residents' Social Conditions` |
| `none` |

Anyone adapting this schema replaces these with their own programme's strategic priorities. They are not portable.

Assignment uses textual evidence, matched keywords, and the activities or problems already identified in the record.

---

## 2.12 `focus_area_justification`

Text. Words, themes, activities, problems, or project evidence supporting each assigned area.

---

## 2.13 `issue_type`

| Attribute | Value |
|---|---|
| Status | Inferred-structural |
| Type | enum[] (multi-label) |
| Permitted values | `physical` · `environmental` · `social` · `safety` · `mobility` · `health` · `service-related` · `none` |

Consolidates the detailed problem fields into broad analytical groups.

**Hard constraint:** must be consistent with the detailed problem fields. Empty problem fields imply `["none"]`. Inconsistency is a validation failure (Prompt 2 check V3).

---

## 2.14 `matched_keyword`

| Attribute | Value |
|---|---|
| Status | **Observed** |
| Type | string |
| Reproducibility | 100% |

Carried forward unchanged from the unified dataset. Maintains the link between collection and thematic interpretation.

---

## 2.15 `keyword_taxonomy_layer`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | enum: `L1`–`L6` |
| Derivation | Mechanical lookup of `matched_keyword` in the taxonomy |
| Reproducibility | 100% |

Replaces unexplained shorthand with an explicit link to the established taxonomy.

---

## 2.16 `keyword_frequency_weight`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | enum: `tier_1`–`tier_5` |
| Derivation | Mechanical lookup of the weight tier assigned during taxonomy development |
| Reproducibility | 100% |

Supports comparison and prioritisation without altering the original taxonomy classification.

---

## 2.17 `additional_keywords_found`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | string[] |
| Cardinality | 0..n |

Synonyms or closely related taxonomy terms present in the body beyond the original collection keyword. Expands thematic coverage where the same concept is expressed through different vocabulary.

---

## 2.18 `activities_review_flag`

`yes` · `no`. Set `yes` where: any problem was assigned at low confidence; the record discusses a place outside the study area; `issue_type` could not be made consistent with the detailed fields; or meaning depends substantially on media unavailable as text.

---

## 2.19 `activities_prompt_version`

Provenance. String. The prompt specification version that produced these fields.

---

## Multi-label handling in downstream analysis

Multi-label fields must not be collapsed to a single value for convenience without stating that this was done. A record carrying three problems contributes to three counts, and the sum of category counts will exceed the record count. Reporting category frequencies without stating the denominator convention makes the figures uninterpretable.
