# Spatial Join Tables

Social records are linked to the physical and environmental backbone through join tables rather than by embedding geometry in the record. This preserves the **one-to-many** relationship: a single record may reference several places.

---

## Structure

**One row per record–place pair.** A record referencing two facilities on different streets produces two facility rows and two street rows.

### `SDT_GIS_Zone`

| Field | Type | Description |
|---|---|---|
| `join_id` | string | Unique row identifier |
| `unified_record_id` | string | Foreign key to the enriched record |
| `zone_id` | string | Foreign key to the zone feature class |
| `zone_name` | string | Human-readable |
| `geometry_ref` | string | Reference to the zone geometry |
| `place_confidence` | enum | Carried from the source identification |
| `place_evidence_type` | enum | Carried from the source identification |

### `SDT_GIS_Street`

| Field | Type | Description |
|---|---|---|
| `join_id` | string | Unique row identifier |
| `unified_record_id` | string | Foreign key to the enriched record |
| `street_id` | string | Foreign key to the road network |
| `street_name` | string | Human-readable |
| `geometry_ref` | string | Reference to the street geometry |
| `place_confidence` | enum | Carried from the source identification |
| `place_evidence_type` | enum | Carried from the source identification |

### `SDT_GIS_Facility`

| Field | Type | Description |
|---|---|---|
| `join_id` | string | Unique row identifier |
| `unified_record_id` | string | Foreign key to the enriched record |
| `facility_id` | string | Foreign key to the facility layer |
| `facility_name` | string | Human-readable |
| `service_type` | string | From the facility layer |
| `geometry_ref` | string | Reference to the facility geometry |
| `place_confidence` | enum | Carried from the source identification |
| `place_evidence_type` | enum | Carried from the source identification |

Reference implementation scale: **9 zones, 18 streets, 25 facilities.**

---

## Why confidence is carried onto every join row

Without it, a map of record density by street treats a GPS-tagged record and a record inferred from an indirect phrase as identical evidence. Carrying `place_confidence` and `place_evidence_type` onto the join row lets any spatial query filter by evidence strength, and lets any spatial figure state what proportion of its underlying records were explicitly located.

**A spatial analysis that does not filter or report on these fields is asserting a precision the data does not have.**

---

## Cardinality rules

| Situation | Join rows produced |
|---|---|
| No place evidence | **None** |
| One facility identified | 1 facility + 1 street + 1 zone |
| One street identified, no facility | 1 street + 1 zone |
| One zone identified only | 1 zone |
| Two facilities on different streets | 2 facility + 2 street + 1–2 zone |

**Upward derivation only.** A facility determines its street and zone. A zone never generates a street row, and a street never generates a facility row.

---

## Known interface constraint

In ArcGIS Experience Builder, the related-records pop-up previews only a limited number of related rows, which makes it unsuitable for displaying the full set of records joined to a place. The working solution is a **Table widget bound to the relationship class with a map-click trigger**, which displays the complete related set.

This is an interface limitation of the presentation layer, not a property of the join structure.
