# 2. The Specification Schema

Every operational prompt in this repository follows the same seven-component structure. The schema is the unit of methodological control: it is what makes an agent inspectable, repeatable, and challengeable.

## 2.1 The seven components

```
  ┌──────────────────────────────────┐
  │  1 · Agent Role                  │   what kind of operator this is
  │  2 · Agent Goal                  │   what it must achieve
  │  3 · Inputs (bounded)            │   what it may look at, exhaustively
  │  4 · Procedural Steps (ordered)  │   the sequence it must follow
  │  5 · Rules and Constraints       │   what it must not do
  │  6 · Output Format               │   the fixed schema it must return
  │  7 · Validation Procedure        │   how the output is checked
  └──────────────────────────────────┘
        + task parameters and operational limits
```

### 1 · Agent Role
A single sentence naming the function. Narrow by design: "keyword extraction agent", not "research assistant". The role establishes what kind of judgement is in scope and, by omission, what is not.

### 2 · Agent Goal
The specific outcome the prompt must achieve. One goal per prompt. Where a task has several goals, it is split into several prompts rather than compounded into one.

### 3 · Inputs (bounded)
An exhaustive list of what the agent may use. **Bounded** is the operative word: anything not listed is out of scope, including the model's own background knowledge. This is what prevents the agent from importing unstated assumptions about the study area.

### 4 · Procedural Steps (ordered)
The numbered sequence the agent must follow. Ordering matters where later steps depend on earlier ones. The steps describe *what* is done, not *how well*; quality is governed by the rules.

### 5 · Rules and Constraints
Prohibitions and conditions. This component carries most of the methodological weight. Typical constraints across this framework:

- do not invent content not present in the inputs;
- do not infer relevance, meaning, or attributes without textual evidence;
- where evidence is insufficient, return an explicit "unclear" value rather than a guess;
- preserve original identifiers, raw text, and provenance;
- flag ambiguous cases for review rather than resolving them silently.

### 6 · Output Format
The exact schema to be returned: field names, permitted values, file structure. Fixed within a processing run. This is what makes outputs from different runs structurally comparable, and therefore what makes variance measurable at all.

### 7 · Validation Procedure
How the output is checked before it is used. Structural checks confirm required fields are present and controlled fields use permitted values. Evidence checks confirm inferred attributes carry justification. Cross-field checks confirm dependent fields do not contradict one another.

### Task parameters and operational limits
Recorded alongside the seven components for every run:

| Parameter | Purpose |
|---|---|
| `prompt_version` | Which specification produced this output |
| `model_version` | Which model executed it |
| `batch_size` | How many records per API call |
| `processing_scope` | Full dataset, or a defined test sample |
| `input_dataset_version` | Which data version was used |
| `run_id` | Unique identifier linking outputs to their run |

Batch size controls how records are transferred to the model. It does **not** change the analytical schema applied to them.

## 2.2 How to write a compliant specification

If you are adapting this framework to another context, a specification is compliant when all seven components are present, explicit, and written **before** execution.

Two failure modes to watch for:

**Unbounded inputs.** If component 3 says "the dataset and any relevant context", the agent's behaviour is not specified. Inputs must be enumerable. Where contextual documents are supplied, they are listed individually.

**Rules stated as preferences.** "Try to avoid unsupported inference" is not a constraint. "Do not infer relevance without textual evidence; if uncertain, mark as no" is.

## 2.3 Versioning discipline

The prompt and output structure remain **fixed within a processing run**.

Where testing identifies unclear instructions, missing restrictions, or inconsistent outputs, the specification is revised as a **new version** and re-executed from the start. A prompt is never edited partway through a run, because the resulting dataset would then have been produced by two different methods with no way to tell which record came from which.

Every output record carries the `prompt_version` and `model_version` that produced it. This is the minimum condition for a result to be attributable to a method.

## 2.4 Why seven components rather than a free-form prompt

A free-form prompt can produce good output. It cannot be audited, because there is no way to distinguish the instruction from the phrasing, or to tell which part of the instruction produced which part of the result.

The schema forces four things to be separable and separately criticisable: what the agent was allowed to see (3), what it was told to do (4), what it was forbidden from doing (5), and how the result was checked (7). A reader who disagrees with a finding can locate the component responsible for it.
