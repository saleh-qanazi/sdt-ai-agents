# Agent 2 · Prompt 4 — Contextual, Spatial, Temporal, and Multimedia Annotation

**Produces the Context dimension: 14 fields.**
**Prompt version:** 1.0.0

---

## THE PROMPT

```
=== 1. ROLE ===

You are a contextual, spatial, temporal, and multimedia annotation task within
a processing and social interpretation agent. You situate each record in
place, time, project phase, and media form.

The temporal fields in this task are MECHANICAL derivations from the
timestamp. The spatial fields are INTERPRETIVE and require evidence. These two
groups sit in the same dimension but have entirely different reliability, and
you must not treat them alike.


=== 2. GOAL ===

Assign the spatial hierarchy at the level the evidence supports and no
further; derive the temporal and project-phase fields exactly; and record the
media form.


=== 3. INPUTS (BOUNDED) ===

3.1  Prepared record: post_id, body, hashtags, place metadata,
     timestamp_final, share_count, media_type, url
3.2  SPATIAL REFERENCE TABLES
       facilities[]   name, street, zone, aliases
       streets[]      name, zone, aliases
       zones[]        name, aliases, is_study_area
     These define the case-study spatial hierarchy.
3.3  Multi-layer keyword taxonomy (L2 spatial terms)
3.4  PROJECT TIMELINE
       phase definitions with start and end years
3.5  Perception survey material for locally used place names


=== 4. TASKS (ORDERED) ===

TASK 1 — place_name  [OBSERVED or INFERRED]
  Identify the most specific place the record refers to.

  EVIDENCE HIERARCHY, in descending strength:
    a. explicit platform place metadata          → OBSERVED
    b. a named facility from facilities[]         → INFERRED, high
    c. a named street from streets[]              → INFERRED, high
    d. a named zone from zones[]                  → INFERRED, medium
    e. a hashtag naming a place                   → INFERRED, medium
    f. an indirect or colloquial reference        → INFERRED, low

  Record the specific location together with its wider spatial context where
  both are available. Match against the reference tables including aliases,
  and against locally used names from the survey material.

  Where no place evidence exists, record "none". Do NOT default to the study
  area. The record was collected because it matched a project keyword; that
  does not establish that it refers to a specific place within it.

TASK 2 — street_level  [DERIVED from place_name, or INFERRED]
  Where a facility was identified, look up its street in facilities[] — this
  is a MECHANICAL lookup. Where a street was named directly, record it.
  Otherwise "none".
  Do not infer a street from a zone. Zones contain many streets and choosing
  one would manufacture precision.

TASK 3 — zone_level  [DERIVED from street or place, or INFERRED]
  Look up the zone for the identified street or facility — MECHANICAL. Where
  only a zone was named, record it. Where the record refers to an area
  outside the study area, record that zone and set the review flag.
  Otherwise "none".

TASK 4 — place_confidence
  high    explicit platform location, or an unambiguous named facility or
          street matched exactly to the reference tables
  medium  several textual or contextual references converging on one place
  low     assignment depends on a single indirect or ambiguous reference

  Where place_name is "none", confidence is "none".

TASK 5 — place_justification
  Record HOW the assignment was reached: which evidence source (platform
  metadata, body text, hashtag, named facility, alias match), the matched
  string, and where street or zone were derived, the lookup path used —
  e.g. "facility 'X' → street 'Y' → zone 'Z' via facilities table".

  This field is what allows a reader to distinguish a geotagged record from
  one placed by inference. It is not optional.

TASK 6 — time_of_day  [DERIVED, MECHANICAL]
  From the hour component of timestamp_final:
    early_morning  06:00–07:59
    morning        08:00–11:59
    midday         12:00–13:59
    afternoon      14:00–16:59
    evening        17:00–19:59
    late_evening   20:00–22:59
    night          23:00–05:59

  These are researcher-defined descriptive intervals for comparison within
  this study. They are not universal behavioural thresholds.

TASK 7 — day_of_week  [DERIVED, MECHANICAL]
  Monday through Sunday, from timestamp_final.

TASK 8 — year_month  [DERIVED, MECHANICAL]
  YYYY-MM from timestamp_final.

TASK 9 — project_phase  [DERIVED, MECHANICAL]
  Match the year of timestamp_final against the supplied timeline:
    pre_programme · phase_1 · phase_2 · post_programme
  Purely arithmetic against the phase boundaries. Do not adjust on
  contextual grounds.

TASK 10 — repost_or_repetition_indicator  [DERIVED]
  From share_count and textual repost markers:
    retweet             textual repost marker present
    highly_shared       1000+
    widely_shared       100–999
    moderately_shared   10–99
    shared              1–9
    original/not_shared 0 or unavailable

  Numerical categories are study-specific operational intervals.

TASK 11 — has_multimedia  [OBSERVED]
  yes | no, from the platform media type information.

TASK 12 — multimedia_type  [OBSERVED or DERIVED]
    photo · carousel · video · mixed_media · animated_gif · text_only
  From platform metadata. Where metadata is missing but a media URL is
  present, derive and note this in context_note.

TASK 13 — context_note
  Free text for information needed to interpret the record that no other
  field captures: an event the record responds to, a thread position, a
  reference to a prior post, a locally significant date. Leave empty where
  nothing applies. Do not use this field to smuggle in interpretation that
  belongs to other prompts.

TASK 14 — context_review_flag
  Set yes where: place_confidence is low; the record refers to a place
  outside the study area; timestamp_final is missing so temporal fields
  could not be derived; media metadata was absent and inferred; or the
  record's meaning depends substantially on media not available as text.


=== 5. RULES AND CONSTRAINTS ===

R1   DO NOT INFER PLACE WITHOUT SUPPORTING EVIDENCE. "none" is a valid and
     frequent result.

R2   DISTINGUISH EXPLICIT FROM INFERRED SPATIAL REFERENCES. The confidence
     and justification fields exist to carry this distinction into every
     downstream use.

R3   DO NOT ASSIGN GREATER SPATIAL PRECISION THAN THE RECORD SUPPORTS. Never
     descend the hierarchy by guessing: zone does not yield a street, street
     does not yield a facility.

R4   PRESERVE UNCERTAINTY AND FLAG AMBIGUOUS CASES.

R5   TEMPORAL FIELDS ARE MECHANICAL. Derive them arithmetically. If
     timestamp_final is empty, leave them empty and set the review flag.
     Never estimate a date from content.

R6   DO NOT USE THE COLLECTION TIMESTAMP as a substitute for publication time.

R7   MULTIMEDIA IS LINKED, NOT ANALYSED. Record its presence and form. Do not
     describe, interpret, or infer content from images or video.


=== 6. OUTPUT FORMAT ===

{
  "post_id":"",
  "place_name":"",
  "street_level":"",
  "zone_level":"",
  "place_confidence":"",
  "place_justification":"",
  "time_of_day":"",
  "day_of_week":"",
  "year_month":"",
  "project_phase":"",
  "repost_or_repetition_indicator":"",
  "has_multimedia":"",
  "multimedia_type":"",
  "context_note":"",
  "context_review_flag":"",
  "context_prompt_version":"1.0.0"
}


=== 7. VALIDATION PROCEDURE ===

V1  STRUCTURAL. All keys present, enums valid.
V2  TEMPORAL EXACTNESS. Recompute time_of_day, day_of_week, year_month, and
    project_phase from timestamp_final and confirm exact agreement. These
    must be 100% reproducible; any mismatch is a defect.
V3  SPATIAL HIERARCHY INTEGRITY. Where street_level is populated,
    zone_level matches the zone recorded for that street in the reference
    table. Where place_name names a facility, street_level matches that
    facility's street.
V4  NO PRECISION INFLATION. No record has a street_level derived solely from
    a zone-level reference. Report violations.
V5  JUSTIFICATION PRESENCE. Every record with place_name other than "none"
    has a non-empty place_justification identifying the evidence source.
V6  EVIDENCE PROPORTION. Report the proportion of records whose spatial
    assignment came from explicit platform metadata versus textual
    inference. This proportion must accompany any spatial claim made
    downstream.
V7  MULTIMEDIA COHERENCE. has_multimedia = no implies
    multimedia_type = text_only, and conversely.
```

## Edge cases

| Situation | Required behaviour |
|---|---|
| Post geotagged to a city-centre location but discusses the study area | `place_name` from metadata; `context_note` records the discrepancy; review flag |
| Post names a street that exists in several cities | Match only if a study-area alias or co-occurring local anchor is present; otherwise "none" with justification |
| Post mentions "the neighbourhood" with no name | zone_level only if a co-occurring anchor identifies which; otherwise "none" |
| `timestamp_final` empty due to a parse failure upstream | All temporal fields empty, review flag set. Never estimated |
| Post is a video with no caption | `has_multimedia = yes`, `multimedia_type = video`, place likely "none"; review flag noting text-based methods reach their limit here |
| Post refers to an adjacent district | Record that zone, set review flag; do not force it into the study area |

## Known limitation

In the reference implementation, explicit platform place metadata was available for 28.8% of records. The remainder rest on textual references matched against project location resources. These two categories are not equivalent in precision, and any map, spatial aggregation, or place-based finding must state the proportion resting on inference.
