# Dimension 4 — Contextual, Spatial, and Temporal

**16 fields. Questions addressed: where, when, and under what circumstances.**

> **This dimension mixes the most and least reproducible fields in the schema.** Temporal fields are mechanical derivations reproducing at 100%. Spatial fields are interpretive and depend on evidence that is frequently absent. They must not be treated alike because they share a dimension.

---

## 4.1 `place_name`

| Attribute | Value |
|---|---|
| Status | **Observed or Inferred-semantic** — varies per record |
| Type | string |
| Cardinality | 1 |
| Nullable | No — `none` is a valid and frequent value |
| Companions | `place_confidence`, `place_justification` |
| Provenance | Project location resources: facility list, service list, street terms in the taxonomy, locally used names from survey material |

**Evidence hierarchy, descending strength**

| Rank | Evidence | Status | Confidence |
|---|---|---|---|
| a | Explicit platform place metadata | Observed | `high` |
| b | Named facility matched to the reference table | Inferred | `high` |
| c | Named street matched to the reference table | Inferred | `high` |
| d | Named zone matched to the reference table | Inferred | `medium` |
| e | Hashtag naming a place | Inferred | `medium` |
| f | Indirect or colloquial reference | Inferred | `low` |

**Rule:** where no place evidence exists, record `none`. **Do not default to the study area.** A record was collected because it matched a project keyword; that does not establish it refers to a specific place within the area.

**Reference implementation:** explicit platform place metadata was available for **28.8%** of records. The remainder rest on textual matching.

---

## 4.2 `street_level`

| Attribute | Value |
|---|---|
| Status | **Derived** where looked up from a facility; **Inferred** where named directly |
| Type | string |
| Nullable | No — `none` valid |
| Derivation | Mechanical lookup of the identified facility's street in the reference table |

**Constraint:** do not infer a street from a zone. Zones contain many streets; choosing one manufactures precision. Enforced by coherence Test 10 and Prompt 4 check V4.

---

## 4.3 `zone_level`

| Attribute | Value |
|---|---|
| Status | **Derived** from street or facility; **Inferred** where named directly |
| Type | string |
| Nullable | No — `none` valid |
| Derivation | Mechanical lookup of the zone for the identified street or facility |

Includes the study area itself and surrounding districts. Where a record refers to an area outside the study area, that zone is recorded and the review flag set — the record is not forced into the study area.

---

## 4.4 `place_confidence`

| Value | Assigned when |
|---|---|
| `high` | Explicit platform location, or an unambiguous named facility or street matched exactly |
| `medium` | Several textual or contextual references converging on one place |
| `low` | Assignment depends on a single indirect or ambiguous reference |
| `none` | `place_name` is `none` |

---

## 4.5 `place_justification`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | text |
| Nullable | No wherever `place_name` ≠ `none` |

Records **how** the assignment was reached: the evidence source (platform metadata, body text, hashtag, named facility, alias match), the matched string, and the lookup path where street or zone were derived — e.g. `facility 'X' → street 'Y' → zone 'Z' via facilities table`.

**This field is what allows a reader to distinguish a geotagged record from one placed by inference.** It is not optional.

---

## 4.6 `time_of_day`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | enum |
| Derivation | Hour component of `timestamp_final` |
| Reproducibility | 100% |

| Value | Interval |
|---|---|
| `early_morning` | 06:00–07:59 |
| `morning` | 08:00–11:59 |
| `midday` | 12:00–13:59 |
| `afternoon` | 14:00–16:59 |
| `evening` | 17:00–19:59 |
| `late_evening` | 20:00–22:59 |
| `night` | 23:00–05:59 |

> Researcher-defined descriptive categories for finer temporal comparison within this study. **Not universal behavioural thresholds.**

---

## 4.7 `day_of_week`

**Derived.** `Monday`–`Sunday`, calculated from `timestamp_final`. 100% reproducible. Preserves weekly variation without interpretive classification.

---

## 4.8 `year_month`

**Derived.** `YYYY-MM` from `timestamp_final`. 100% reproducible.

---

## 4.9 `project_phase`

| Attribute | Value |
|---|---|
| Status | **Derived / Linked** |
| Type | enum: `pre_programme` · `phase_1` · `phase_2` · `post_programme` |
| Derivation | Arithmetic match of the year of `timestamp_final` against documented phase boundaries |
| Reproducibility | 100% |
| Provenance | Programme timeline — configuration, replaced per case study |

Assigned mechanically. **Not adjusted on contextual grounds.**

---

## 4.10 `repost_or_repetition_indicator`

| Attribute | Value |
|---|---|
| Status | **Derived** |
| Type | enum |
| Derivation | `share_count` bands, plus textual repost markers |

| Value | Threshold |
|---|---|
| `retweet` | Textual repost marker present |
| `highly_shared` | 1000+ |
| `widely_shared` | 100–999 |
| `moderately_shared` | 10–99 |
| `shared` | 1–9 |
| `original/not_shared` | 0 or unavailable |

> Share and repost counts are established diffusion indicators, but these numerical bands are **study-specific operational intervals**, not universal thresholds.

---

## 4.11 `has_multimedia`

| Attribute | Value |
|---|---|
| Status | **Observed** |
| Type | enum: `yes` · `no` |
| Reproducibility | 100% |

From platform media type information, restored from platform-specific files where needed.

---

## 4.12 `multimedia_type`

| Attribute | Value |
|---|---|
| Status | **Observed**, or **Derived** where metadata absent but a media URL is present |
| Type | enum |
| Permitted values | `photo` · `carousel` · `video` · `mixed_media` · `animated_gif` · `text_only` |

Retains differences in content form relevant for qualitative interpretation where information is communicated visually rather than textually.

**Coherence constraint:** `has_multimedia = no` if and only if `multimedia_type = text_only`. Enforced by coherence Test 9.

---

## 4.13 `context_note`

| Attribute | Value |
|---|---|
| Status | Inferred-semantic |
| Type | text |
| Nullable | Yes |

Information needed to interpret the record that no other field captures: an event the record responds to, a thread position, a reference to a prior post, a locally significant date.

**Not** a place to smuggle in interpretation belonging to other prompts.

---

## 4.14 `context_review_flag`

`yes` · `no`. Set `yes` where: `place_confidence` is `low`; the record refers to a place outside the study area; `timestamp_final` is missing so temporal fields could not be derived; media metadata was absent and inferred; or meaning depends substantially on media unavailable as text.

---

## 4.15 `spatial_evidence_source`

| Attribute | Value |
|---|---|
| Status | Inferred-structural |
| Type | enum: `platform_metadata` · `body_text` · `hashtag` · `facility_match` · `street_match` · `zone_match` · `alias_match` · `none` |

Machine-readable counterpart to `place_justification`, enabling the explicit-versus-inferred proportion to be computed without parsing free text.

---

## 4.16 `context_prompt_version`

Provenance. String.

---

## Spatial join tables

Where spatial evidence supports it, records link to the spatial backbone through join tables — **one row per record–place pair**, so a record referring to several places produces several rows.

| Table | Fields | Cardinality |
|---|---|---|
| Zone join | `post_id`, `zone`, `geometry_ref`, `place_confidence` | 0..n per record |
| Street join | `post_id`, `street`, `geometry_ref`, `place_confidence` | 0..n per record |
| Facility join | `post_id`, `facility`, `geometry_ref`, `place_confidence` | 0..n per record |

Carrying `place_confidence` into the join tables is essential: a map that renders high-confidence geotags and low-confidence textual inferences identically misrepresents its own evidence base.

---

## Reporting requirement

**Every spatial claim must state the proportion of contributing records whose assignment rested on explicit platform metadata versus textual inference.** These are not equivalent in precision, and an aggregation that mixes them without saying so overstates spatial resolution.
