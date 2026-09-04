# Provenance Block

**9 fields carried on every enriched record.**

Provenance is what makes any individual output traceable to the record, the run, the specification, and the model that produced it. Without it, a result is not attributable to a method and cannot be reproduced or challenged.

| Field | Status | Type | Description |
|---|---|---|---|
| `post_id` | Observed | string | Pipeline-unique record identifier, assigned at cross-platform integration |
| `source_record_id` | Observed | string | Platform-native identifier, **stored as string**. Float storage causes precision loss and identifier collisions |
| `prepared_record_id` | Observed | string | Identifier assigned during preparation, linking to the pre-interpretation record |
| `run_id` | Provenance | uuid | Unique identifier of the processing run that produced these values |
| `agent_id` | Provenance | string | Which agent executed: `agent_1_preparation`, `agent_2_interpretation`, `agent_3_validation` |
| `prompt_version` | Provenance | semver | Specification version. Records produced under different prompt versions are not directly comparable |
| `model_version` | Provenance | string | Model identifier and version used as execution engine |
| `parameter_profile` | Provenance | string | Batch size, processing scope, and other run parameters |
| `processed_timestamp` | Provenance | datetime | When this record was processed, distinct from publication and collection time |

## Three timestamps, three meanings

A recurring source of error. These must never be conflated.

| Timestamp | Meaning |
|---|---|
| `timestamp_final` | When the post was **published** by its author |
| `collection_timestamp` | When the capture tool **retrieved** the post |
| `processed_timestamp` | When the agent **interpreted** the record |

Substituting one for another — for instance, using the collection timestamp when publication time failed to parse — invalidates all temporal analysis and all project-phase assignment.

## Run-level records

Beyond the per-record block, three run-level structures are maintained.

### ProcessingRun

| Field | Description |
|---|---|
| `run_id` | uuid |
| `agent_id` | Which agent |
| `prompt_version` | Specification version |
| `model_version` | Execution engine |
| `parameter_profile` | Run parameters |
| `input_dataset_version` | Which data version was processed |
| `qa_summary` | Reference to the quality-assurance report |
| `records_in` / `records_out` | For lineage reconciliation |

### ProvenanceRecord

| Field | Description |
|---|---|
| `provenance_id` | uuid |
| `source_entity` | Originating record or dataset |
| `target_entity` | Resulting record or dataset |
| `run_id` | Run producing the transformation |
| `version_reference` | Dataset or keyword-set version |

### ValidationRecord

| Field | Description |
|---|---|
| `validation_id` | uuid |
| `validation_type` | Filtering consistency, reproducibility, coherence |
| `validation_status` | Pass, fail, flagged for review |
| `validator` | Agent or named human reviewer |
| `uncertainty_level` | Strength of the validation conclusion |
| `target_entity` | Record or field assessed |
| `run_id` | Run under assessment |

## Minimum reporting obligation

Any published finding produced with this pipeline must state, at minimum, the `prompt_version` and `model_version` that produced the underlying data. A result reported without them is not attributable to a specification and cannot be reproduced.
