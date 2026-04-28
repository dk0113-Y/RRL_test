# 07 Tuning Policy

## Purpose

This document defines allowed tuning fields, frozen fields, manual-review fields, and comparability grouping rules for the automated tuning system.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`
- `docs/03_current_mainline.md`
- `docs/04_automation_scope.md`
- `docs/05_round_protocol.md`
- `docs/06_formal_artifact_map.md`

It constrains `DRL_automatic` configuration proposal, preflight validation, and round publication behavior.

It does not define:
- complete metric scoring rules
- complete stopping decision tree
- exact GPT decision schema

Those are defined in `08`, `09`, and `10`.

## Tuning Philosophy

Policy posture:
- The system supports controlled hyperparameter tuning, not unrestricted AutoML.
- Tuning must preserve current method semantics and `formal_posthoc_trainselect_v1`.
- Every tuning candidate must be auditable through `config_diff.json` and comparability reporting.
- If a proposed change cannot be classified, it must fail closed and require manual review.
- `round_0001` is the current engineering speed baseline anchor, not a method-superiority claim.

Current-stage discipline:
- project stage is `automated_tuning_system_design`
- current priority is protocol hardening, exchange alignment, and later `DRL_automatic` review
- routine tuning must prioritize controllability, comparability, and reviewability over raw search breadth

Controller rule:
- configuration generation must be policy-constrained first, optimization-driven second.

## Baseline Anchor And Comparability Context

Current anchor:
- baseline round is `round_0001`
- baseline run is `value_bcchild_gated_statequery_effopt_formal_500k_decay240k_end005_20260422_210814`

Context rules:
- comparability context starts from this engineering speed baseline candidate
- future rounds should explicitly state baseline reference and comparability group
- different budget or environment settings may create different comparability subgroups
- a run can remain useful as reference-only even if not strictly comparable

Boundary reminder:
- baseline registration status does not mean all future rounds are automatically comparable
- comparability must be evaluated per round and per changed field category

## Routine Allowed Tuning Fields

Routine automated tuning is limited to outer-loop, protocol-compatible fields.

### `total_env_steps`

Classification:
- allowed training-budget tuning field

Rules:
- may create or change comparability subgroup
- must be recorded in `config_diff.json`
- must not be used for naive hard-superiority claims across materially different budgets

### `epsilon_decay_steps`

Classification:
- allowed exploration schedule tuning field

Rules:
- must be recorded in `config_diff.json`
- should be adjusted in controlled increments for attribution

### `epsilon_end`

Classification:
- allowed exploration schedule tuning field

Rules:
- must be recorded in `config_diff.json`
- should be interpreted with budget and warmup context

### warmup settings

Classification:
- allowed routine field if method semantics remain unchanged

Rules:
- warmup changes must be explicit and recorded
- warmup changes must not alter frozen mainline definitions

### posthoc candidate selection thresholds or rules

Classification:
- conditionally allowed within protocol boundaries

Rules:
- allowed only if still within `formal_posthoc_trainselect_v1`
- must not reintroduce training-time validation or recheck
- threshold-level tuning is lower risk than semantic rule replacement

### final probe candidate count

Classification:
- allowed with comparability annotation

Rules:
- allowed only if held-out final probe definition remains unchanged
- must be recorded because it affects evidence budget and selection confidence

### stopping or extension policy parameters

Classification:
- allowed controller-level policy tuning

Rules:
- must be recorded in config and policy context
- detailed stopping logic is defined in `docs/09_stopping_policy.md`

### logging and artifact summarization settings

Classification:
- allowed support-layer tuning

Rules:
- must not change training semantics or metric definitions
- must preserve auditability and machine-readable outputs

### controller dry-run checks

Classification:
- allowed and recommended

Rules:
- should be run before real `formal_train`
- should validate config generation and policy enforcement paths

General routine-tuning rules:
- these are allowed fields, not guaranteed-optimal fields
- every changed field must appear in `config_diff.json`
- simultaneous major changes should be minimized unless branch plan explicitly justifies them

## Frozen Fields

The following fields are frozen for routine automated tuning:
- state tensor schema
- formal input keys
- Dynamic Cumulative Belief Map semantics
- Shared Semantic Layer semantics
- advantage-side state meaning
- value-side block and entry representation meaning
- value-mask semantics
- dual-branch role separation
- Semantic Dueling Decision Head semantics
- reward semantics
- `formal_posthoc_trainselect_v1` protocol semantics
- held-out final probe definition
- checkpoint winner policy
- environment distribution
- evaluation seed policy
- metric definitions
- source code paths that define main method semantics

Freeze rules:
- routine config generation must not modify frozen fields
- if any frozen field changes, same-group comparability is invalid by default
- frozen-field violations must block `formal_train` or escalate to manual review
- frozen-field violations must never silently pass preflight

## Manual Review Fields

The following fields require manual review before formal execution:
- reward coefficients, reward terms, reward structure
- network architecture
- state tensor schema
- semantic-layer behavior
- value-branch semantics
- advantage-branch semantics
- dueling-head semantics
- formal protocol
- final probe protocol
- checkpoint winner policy
- environment generator or map distribution
- evaluation seed policy
- major runtime modes that may alter numerical behavior
- training-loop behavior that changes optimization semantics

Manual-review rules:
- manual review does not automatically preserve comparability
- manually approved semantic change may still require a new comparability group
- manual review status should be recorded in round summary or comparability report
- without explicit review record, controller must fail closed

## Runtime Toggle Policy

Current runtime baseline toggles:
- AMP: false
- inference AMP: false
- `torch.compile`: false
- `channels_last`: false
- TF32: true
- `cudnn_benchmark`: true

Policy rules:
- these toggles define current engineering speed baseline context
- routine automated tuning must not treat runtime toggles as a free search space
- runtime-toggle experiments are allowed only as explicitly scoped efficiency A/B tests
- if a runtime toggle may affect numerical behavior, it requires manual review and separate comparability grouping
- runtime speedup alone must not be presented as method innovation

Execution guard:
- runtime-toggle changes must be detected and categorized before run launch
- uncategorized runtime changes must fail preflight

## Comparability Group Rules

### Same-group comparable requirements

A candidate is same-group comparable only when all core conditions hold:
- same method mainline
- same state and input schema
- same reward semantics
- same formal protocol
- same held-out final probe definition
- same environment distribution
- same evaluation seed policy
- compatible training budget or explicitly same-budget subgroup
- compatible runtime numerical mode

### Different subgroup or reference-only triggers

A candidate should be separate subgroup or reference-only when one or more conditions apply:
- `total_env_steps` changes substantially
- environment distribution changes
- evaluation seed policy changes
- final probe candidate count changes in a way that affects decision confidence
- runtime numerical mode changes
- posthoc candidate-selection rule changes beyond threshold-level tuning
- any manual-review field changes

Interpretation notes:
- budget changes are allowed but require careful subgroup-aware comparison
- longer training can improve outcome but does not automatically imply method improvement over shorter-budget rounds
- reference-only comparisons remain useful for diagnosis and branch planning

Comparability recording rule:
- comparability group identity and subgroup rationale must be explicit in round structured files

## Config Diff Requirements

`config_diff.json` should record at minimum:
- baseline round ID or baseline run identity
- candidate round ID
- changed fields
- old value
- new value
- field category:
- `allowed_tuning`
- `frozen`
- `manual_review`
- `comparability_group_changing`
- `unknown`
- expected comparability impact
- reason for change
- manual-review requirement flag
- preflight result flag

Quality rules:
- unknown fields must fail closed or require explicit review
- `config_diff.json` must be machine-readable
- text-only explanations are insufficient for controller consumption
- grouped field-change blocks should be used when multi-change interaction is intentional

## Preflight Validation Requirements

Before `formal_train`, `DRL_automatic` must validate:
- mode is valid and compatible with requested action
- changed fields are allowed or manually reviewed
- no frozen-field violation
- comparability group is assigned
- baseline reference exists and is resolvable
- current mainline semantics are preserved
- formal protocol semantics are preserved
- runtime-toggle policy is respected
- artifact publication policy is respected
- no checkpoint-binary publication attempt
- no global outbox dependency

Preflight rules:
- preflight failure must block training
- preflight results should be recorded in comparability report or round summary
- preflight must run before real training, not after
- unknown-field or unknown-category changes must not default-pass

## Multi-Change Policy

Policy preference:
- prefer one major tuning axis per round during early automated tuning

Risk control rules:
- avoid changing budget, epsilon, posthoc selection, and final probe candidate count all at once unless explicitly planned
- multi-change rounds may be valid but are harder to attribute
- if multiple fields change, `config_diff.json` must group them and explain intended interaction
- exploratory multi-change rounds may be routed to analysis-only path or separate branch when attribution is weak

Attribution rule:
- when attribution confidence is low, do not promote claims; preserve diagnostic value and branch explicitly.

## Branching And Rollback Policy

High-level branching and rollback constraints:
- new branch is required when tuning direction changes objective or comparability group
- failed or rejected rounds must remain traceable
- rollback must not delete prior round evidence
- if candidate worsens stability or violates scope, reject or pause rather than overwrite baseline
- current baseline anchor remains valid unless later round is explicitly accepted as new baseline

Control rule:
- pointer updates and baseline updates must be explicit and auditable.

Detailed stop/continue logic is defined in `docs/09_stopping_policy.md`.

## Relationship To Other Protocol Docs

Cross-document scope map:
- `03_current_mainline.md`: frozen method semantics
- `04_automation_scope.md`: automation operating boundaries
- `05_round_protocol.md`: round lifecycle and preflight position
- `06_formal_artifact_map.md`: artifact evidence and publication context
- `07_tuning_policy.md`: allowed, frozen, and manual-review tuning fields
- `08_evaluation_charter.md`: tuning-outcome judgement criteria
- `09_stopping_policy.md`: stop, continue, and branching decisions
- `10_output_contract.md`: exact decision and controller-action schema

Use-order guidance:
- use this document to classify and validate proposed config changes
- use evaluation/stopping/output docs for downstream judgement and control actions
- unresolved conflicts must fail closed until manual review resolves them
