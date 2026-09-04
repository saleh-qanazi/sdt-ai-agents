# Agent 1 — Input Schemas

## 1. Platform exports

Raw exports from browser-based capture, in NDJSON or converted CSV. Field counts and names are platform-specific and **not** standardised at input; harmonisation occurs at Stages 5–6.

Reference implementation field counts:

| Platform | Fields | Principal analytical content |
|---|---|---|
| Instagram | 48 | Captions, hashtags, language, media links, engagement metrics, available location references, author attributes |
| X/Twitter | 47 | Full text, hashtags, mentions, URLs, language, record and conversation identifiers, engagement measures, detailed author information |
| TikTok | 57 | Video description, hashtags, language, video and audio attributes, engagement metrics, author information |

### Required field mapping

Before running Prompt 2, supply a mapping declaring which source column carries each of these roles.

| Role | Required | Notes |
|---|---|---|
| `record_identifier` | Yes | **Must be read as string.** Float storage causes precision loss and collisions |
| `body_text` | Yes | Caption, full text, or video description |
| `publication_timestamp` | Yes | **Not** the collection timestamp |
| `collection_timestamp` | Yes | Inserted by the capture tool |
| `author` | Yes | Posting account |
| `hashtags` | No | |
| `place_metadata` | No | Availability varies sharply by platform |
| `like_count` | No | Absent ≠ zero |
| `comment_count` | No | Absent ≠ zero |
| `share_count` | No | Unavailable on some platforms entirely |
| `quote_count` | No | Platform-specific |
| `media_type` | No | |
| `url` | No | |
| `account_biography` | No | Used for actor classification downstream |

### Field categories present across platforms

| Category | Contents |
|---|---|
| Collection metadata | Collection timestamp, source URLs, source platform URL |
| Record identification | Platform-native identifier |
| Temporal | Native publication timestamp; **format varies by platform** |
| Textual | Caption, full text, or description; hashtags; language |
| Media | Media type, media links |
| Engagement | Likes, comments, shares, views, saves, reposts, quotes — **availability varies** |
| Location | Explicit place metadata where provided |
| Author | Account name, declared location, follower counts, activity, creation date |

### Two recurring input defects

**Numeric identifier precision.** Platform-native numeric identifiers stored as floating-point values in CSV exports lose precision and produce collisions between genuinely distinct records. Read them as strings; use content-based deduplication keys.

**Timestamp heterogeneity.** Two patterns recur:

| Pattern | Symptom | Handling |
|---|---|---|
| Near-standard datetime with a trailing textual suffix | Direct parsing fails on the suffix | Strip the suffix, then parse |
| Platform-native format | Neither human-readable nor parseable by standard datetime libraries | Convert by explicit format specification |

Publication timestamps must be distinguished from collection timestamps throughout. The collection timestamp is not subject to these corrections and is never substituted for a failed publication timestamp.

---

## 2. Documentary sources

PDF documents supplying the contextual grounding for keyword derivation. Each must be declared with a category.

| Category | Analytical contribution |
|---|---|
| `planning_regulatory` | Strategic orientation, governance structure, signatory institutions, formal position of the study area within the programme |
| `official_project_report` | Environmental and health ambitions, implementation stages, technical constraints, regulatory procedures |
| `project_maps_spatial` | Locations of planned interventions, protection measures, infrastructures, specific places — the basis for linking discourse to spatial entities |
| `institutional_comm` | Public framing, official terminology, highlighted assets and problems, participation framing |
| `technical_environmental` | Renewal needs, environmental priorities, sustainability issues |
| `survey_qualitative` | Resident perceptions, local vocabulary, issue framing — the principal source for L4 |

**Scanned image PDFs with no text layer cannot be processed.** They are recorded in `extraction_failures` and their absence reported, not worked around.

---

## 3. Study area parameters

Required configuration for Prompts 1 and 2.

| Parameter | Type | Purpose |
|---|---|---|
| `study_area_name` | string | Primary L1 anchor |
| `parent_city` | string | Local anchor for disambiguation |
| `parent_district` | string | L1 and local anchor |
| `programme_name` | string | L1 anchor |
| `surrounding_areas[]` | string[] | Zone-level classification of adjacent references |
| `known_ambiguous_terms[]` | string[] | Terms always flagged, whatever the agent's own assessment |
| `local_anchors[]` | string[] | Establish the record concerns **this** area |
| `competing_referents[]` | string[] | Indicate a homonymous place elsewhere |
| `promotional_markers[]` | string[] | Commercial noise detection |
| `commercial_hashtags[]` | string[] | Commercial noise detection |

### The disambiguation problem this solves

Where a project keyword is also a well-known toponym elsewhere, it cannot simply be removed from the taxonomy — it is often a **primary project identifier**. Nor can every record containing it be excluded. The test is per-record co-occurrence:

| Competing referent | Local anchor | Decision |
|---|---|---|
| Present | Absent | Exclude — `geographic_ambiguity` |
| Present | Present | Retain, record both in `disambiguation_note` |
| Absent | Present | Retain |
| Absent | Absent | Retain, downgrade confidence, set `weak_anchor` |

---

## 4. Manual keyword list

Researcher-supplied terms merged at Stage 2. Free text or tabular. Terms may carry an intended `layer`, `theme`, or `polarity`.

**Where a researcher-supplied classification conflicts with the agent's, the researcher's wins**, and the agent's proposal is recorded in `layer_conflict_note` rather than discarded.

---

## 5. Survey and qualitative material

Supplied as documentary input for keyword derivation, principally populating L4 (citizen vocabulary). Not filtered or processed as records by this agent.

Reference implementation: a perception survey with 124 respondents, providing locally grounded vocabulary and issue framing, and later serving as a triangulation reference against social-media-derived patterns.
