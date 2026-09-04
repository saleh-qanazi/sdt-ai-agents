# Enriched Social Record — Schema Documentation

The enriched social record contains **70 fields** across four analytical dimensions plus a provenance block.

## Read this first

[`metadata-conventions.md`](metadata-conventions.md) defines the columns used in every field table here — status, type, cardinality, nullability, evidence requirement, derivation rule, and category provenance. The dimension files assume it.

## Why field status governs everything

The four status values carry sharply different reliability, and every downstream claim must respect the difference.

| Status | How produced | Measured reproducibility |
|---|---|---|
| Observed | Copied unchanged from the source record | 100% |
| Derived | Computed mechanically by a documented rule | 100% |
| Inferred-structural | Rule-based inference within a controlled vocabulary | 100% |
| Inferred-semantic | Contextual interpretation of meaning | 44.3%–86.3% |

A finding resting on an inferred-semantic field is not the same kind of claim as one resting on a derived field. Reporting them at equal confidence misrepresents the method.

## Null semantics

Empty is meaningful, and is routinely confused with three things it is not.

| Value | Means |
|---|---|
| `""` on an optional text field | No content applies |
| `unclear` on an inferred enum | Evidence was insufficient. **Valid, expected, and frequent** |
| `none` on an enum | The category genuinely does not apply |
| `not_applicable_institutional` | Meaningless for an organisational actor |
| Empty on an engagement integer | The platform does not export this measure. **Not zero** |

Conflating empty with zero on engagement measures, and treating `unclear` as a failure to be minimised, are the two most common ways to misuse this schema.

## Files

### Conventions and vocabularies

| File | Contents |
|---|---|
| [`metadata-conventions.md`](metadata-conventions.md) | Definition of every column used in the field tables |
| [`controlled-vocabularies.md`](controlled-vocabularies.md) | Every closed list, annotated for transferability |
| [`controlled-vocabularies.json`](controlled-vocabularies.json) | The same lists, machine-readable, with thresholds and portability flags |

### Field definitions by dimension

| File | Fields | Question |
|---|---|---|
| [`dimension-1-profile.md`](dimension-1-profile.md) | 15 | who |
| [`dimension-2-activities-and-issues.md`](dimension-2-activities-and-issues.md) | 19 | what, and why where supported |
| [`dimension-3-emotions-and-sentiment.md`](dimension-3-emotions-and-sentiment.md) | 11 | how |
| [`dimension-4-context.md`](dimension-4-context.md) | 16 | where and when |
| [`provenance-fields.md`](provenance-fields.md) | 9 | which run produced this |

### Relationships and validation

| File | Contents |
|---|---|
| [`spatial-join-tables.md`](spatial-join-tables.md) | Record-to-place linkage, one row per record–place pair |
| [`enriched-record.schema.json`](enriched-record.schema.json) | JSON Schema: 67 properties, 11 encoded coherence constraints |

## Validating an output file

The JSON Schema can be applied directly:

```python
import json, jsonschema
schema = json.load(open("enriched-record.schema.json"))
for record in records:
    jsonschema.validate(record, schema)
```

The schema enforces field presence, types, and permitted values. It does **not** enforce the eleven cross-field coherence constraints, which are recorded under `x-coherence-constraints` and tested by [Agent 3, Prompt 3](../../03-validation-agent/prompts/prompt-3-cross-dimension-coherence.md).
