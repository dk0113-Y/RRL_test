# 10 Output Contract

## Purpose

This document defines machine-readable output contracts for GPT decisions, placeholders, controller actions, and Codex instruction artifacts.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`
- `docs/03_current_mainline.md`
- `docs/04_automation_scope.md`
- `docs/05_round_protocol.md`
- `docs/06_formal_artifact_map.md`
- `docs/07_tuning_policy.md`
- `docs/08_evaluation_charter.md`
- `docs/09_stopping_policy.md`

It converts protocol, evaluation, and stopping decisions into round-scoped files that `DRL_automatic` can safely consume.

It does not define metric interpretation or action semantics. Those are defined in `docs/08_evaluation_charter.md` and `docs/09_stopping_policy.md`.

## Output Location Rules

Formal output files must be round-scoped and stored only under the active round directory:
- `rounds/<round_id>/gpt_decision.json`
- `rounds/<round_id>/gpt_decision_placeholder.json`
- `rounds/<round_id>/controller_action.json` when needed
- `rounds/<round_id>/codex_instruction.md` when needed

Location constraints:
- decision artifacts are round-scoped
- `CURRENT_ROUND.json` is pointer authority
- active-round structured JSON is fact authority for exchange-facing control
- output files must not silently overwrite prior round evidence

Prohibited sources and targets:
- global `outbox/`
- stale `next_gpt_decision.json`
- decision files in backup directories
- decisions not matching `CURRENT_ROUND.json.current_round_id`
- controller consumption of historical decisions unless explicitly marked reference-only and not executable

Consumption path rule:
- executable decision input for controller must come from active round files only.

## Decision Generation Rules

Decision-generation behavior must follow pointer state and decision requirement status.

If `decision_required_from_gpt=false`:
- do not generate formal `gpt_decision.json` unless user explicitly requests one
- use `gpt_decision_placeholder.json` when needed
- baseline registration rounds may remain in placeholder state

If `decision_required_from_gpt=true`:
- GPT may generate `gpt_decision.json`
- decision must reference active round ID
- decision must include evidence and comparability status
- decision must include selected action from allowed action vocabulary

If evidence is missing, contradictory, or incomplete:
- decision must downgrade to `pause_for_manual_review`, `analyze_only`, `reject_round`, or equivalent inconclusive-safe handling
- missing values must not be invented

Decision-generation boundaries:
- no global outbox dependency
- no checkpoint binary payloads
- no synthetic rehearsal evidence as formal training evidence
- no automatic formal decision generation when not requested

## `gpt_decision.json` Required Fields

Required top-level fields:
- `schema_version`
- `round_id`
- `round_path`
- `generated_at`
- `generated_by`
- `decision_status`
- `round_type`
- `operating_mode`
- `baseline_reference`
- `evaluation_verdicts`
- `selected_action`
- `action_confidence`
- `comparability_status`
- `artifact_sufficiency`
- `protocol_assessment`
- `config_assessment`
- `stability_assessment`
- `efficiency_assessment`
- `claim_boundary`
- `manual_review_required`
- `codex_instruction_required`
- `controller_action_required`
- `reasons`
- `blocked_reasons`
- `required_next_steps`
- `evidence_references`
- `consumption_rules`

Required-field rules:
- implementation may extend fields, but required fields must remain present
- unknown or missing required fields must block controller consumption
- required fields must remain JSON-parseable and schema-stable across rounds

## Recommended Field Semantics

### Core identity fields

`schema_version`:
- type: string
- recommended initial value: `output_contract_v1`

`round_id`:
- type: string
- must match active round ID

`round_path`:
- type: string
- must match `CURRENT_ROUND.json.current_round_path`

`generated_at`:
- type: string
- ISO-like timestamp is preferred

`generated_by`:
- type: string
- examples: `web_gpt`, `codex_generated_placeholder`, `human_review`

### Decision state fields

`decision_status` enum:
- `not_requested`
- `draft`
- `recorded`
- `invalid`
- `superseded`
- `manual_review_required`

`round_type` enum:
- `baseline_registration`
- `protocol_review`
- `dry_run_no_train`
- `formal_train_result`
- `analysis_only`
- `failed_or_rejected`

`operating_mode` enum:
- `protocol_review`
- `dry_run_no_train`
- `formal_train`
- `analysis_only`
- `synthetic_rehearsal`

`selected_action` enum:
- `accept_baseline`
- `run_next_round`
- `continue_local_search`
- `branch_new_direction`
- `stop_direction`
- `reject_round`
- `pause_for_manual_review`
- `analyze_only`

`action_confidence` enum:
- `high`
- `medium`
- `low`

### Evidence and comparability fields

`comparability_status` enum:
- `same_group`
- `same_subgroup`
- `reference_only`
- `not_comparable`
- `unknown`
- `failed`

`artifact_sufficiency` enum:
- `sufficient`
- `partial`
- `missing_core_evidence`
- `dry_run_expected_missingness`
- `not_applicable`
- `unknown`

`evaluation_verdicts`:
- array of verdict labels from evaluation charter vocabulary

`claim_boundary`:
- array that should classify claims such as:
- `engineering_speed_baseline`
- `method_performance_claim`
- `diagnostic_only`
- `reference_only`
- `analysis_only`
- `protocol_review_only`

### Control-gating fields

`manual_review_required`:
- type: boolean

`codex_instruction_required`:
- type: boolean

`controller_action_required`:
- type: boolean

`reasons`:
- type: array of strings
- must explain selected action

`blocked_reasons`:
- type: array of strings
- empty array allowed

`required_next_steps`:
- type: array of strings

`evidence_references`:
- type: array of paths within active round or protocol docs

`consumption_rules`:
- object
- must specify whether controller may consume decision
- must include fail-closed conditions and rationale

## Assessment Objects

The following objects are required and should remain structured.

### `baseline_reference`

Recommended fields:
- `baseline_round_id`
- `baseline_run_name`
- `baseline_role`
- `comparability_group`

Consistency rules:
- baseline identity must align with active comparability context
- baseline role must preserve claim boundary language

### `protocol_assessment`

Recommended fields:
- `formal_protocol`
- `protocol_valid`
- `frozen_field_violation`
- `manual_review_fields_touched`
- `notes`

Consistency rules:
- `formal_protocol` should match active protocol context
- frozen-field violations must never be hidden

### `config_assessment`

Recommended fields:
- `config_diff_available`
- `changed_fields_summary`
- `unknown_fields_present`
- `preflight_status`
- `allowed_tuning_fields`
- `manual_review_fields`
- `frozen_field_violations`

Consistency rules:
- should align with `config_diff.json` and preflight outcomes
- unknown fields should trigger fail-closed behavior

### `stability_assessment`

Recommended fields:
- `best_vs_last_gap_status`
- `late_stage_degradation_risk`
- `repeat_visit_or_stall_risk`
- `notes`

Consistency rules:
- should align with available diagnostics and round summaries
- cannot assert stability where evidence is missing without explicit unknown state

### `efficiency_assessment`

Recommended fields:
- `runtime_claim_type`
- `runtime_comparability`
- `runtime_toggle_change`
- `notes`

Consistency rules:
- runtime claims must separate efficiency from method-performance claims
- runtime-toggle changes must align with tuning and comparability policy

## `gpt_decision_placeholder.json` Contract

Placeholder purpose:
- used when no formal GPT decision is required
- common for baseline registration when `decision_required_from_gpt=false`
- not a formal accept or reject decision

Required fields:
- `schema_version`
- `round_id`
- `decision_status`
- `reason`
- `next_decision_stage`
- `created_at` when available

Recommended baseline-registration values:
- `decision_status`: `not_requested`
- `reason`: explain that round registers baseline before automated tuning decision stage
- `next_decision_stage`: for example, after controller protocol review and dry-run validation

Placeholder constraints:
- placeholder must not be consumed as approval to launch training
- placeholder must not be treated as baseline promotion unless explicit promotion is recorded elsewhere

## `controller_action.json` Contract

Controller-action file is optional.

Usage rule:
- create only when decision requires machine-consumable controller instruction
- must be derived from valid active-round `gpt_decision.json`
- must be round-scoped

Recommended fields:
- `schema_version`
- `round_id`
- `source_decision_file`
- `controller_action`
- `allowed_next_mode`
- `target_next_round_id`
- `baseline_reference`
- `allowed_config_changes`
- `forbidden_config_changes`
- `manual_review_required`
- `must_not_launch_training`
- `dry_run_required_before_training`
- `notes`

Controller-action guard rules:
- if action is `pause_for_manual_review`, `reject_round`, or `analyze_only`, controller must not launch formal training
- if action is `run_next_round`, controller must still run preflight validation
- `controller_action.json` must not bypass tuning policy, frozen-field guards, comparability guards, or preflight

## `codex_instruction.md` Contract

Codex-instruction file is optional.

Usage rule:
- create when bounded code or document modification is required
- keep round-scoped when instruction is tied to round-level corrective action

Minimum instruction content should include:
- target repository
- allowed files
- forbidden files
- task objective
- required constraints
- validation commands
- commit/push instructions
- final report requirements

Codex-instruction constraints:
- must not authorize broad training-code modifications unless explicitly approved
- must not authorize checkpoint upload
- must not authorize training launch unless explicitly requested
- must preserve task boundary and fail closed on out-of-scope requests

## Consumption Rules For `DRL_automatic`

`DRL_automatic` may consume decision only if all checks pass:
- file is under active round directory
- `round_id` matches `CURRENT_ROUND.json.current_round_id`
- `schema_version` is supported
- required fields exist
- `selected_action` is valid enum
- `decision_status` is `recorded`
- manual-review requirements are compatible with action path
- no blocking reasons prevent execution
- preflight still runs before any `formal_train`

`DRL_automatic` must fail closed if any check fails, including:
- decision file missing when required
- decision file stale or superseded
- decision file from backup directory
- decision refers to different round
- selected action unknown
- required fields missing
- frozen-field violation present
- global outbox detected as source
- controller action attempts to bypass preflight

Execution safety rule:
- decision consumption permission is necessary but never sufficient for training launch
- preflight and scope guards remain mandatory

## Claim Boundary Fields

Decision artifacts must separate claim classes explicitly:
- engineering speed baseline claim
- method-performance claim
- diagnostic-only claim
- reference-only claim
- analysis-only claim
- protocol-review-only claim

Claim boundary constraints:
- `round_0001` can only be engineering speed baseline candidate unless future explicit evidence and protocol process justify stronger claim
- runtime speedup alone must not be encoded as method-performance superiority
- synthetic rehearsal must not be encoded as formal evidence

Consistency rule:
- claim boundary fields should align with evaluation verdicts, comparability status, and artifact sufficiency.

## Consistency Rules Across Files

Decision artifacts must remain consistent with active-round structured evidence.

Required consistency checks:
- `round_id` and `round_path` must match `CURRENT_ROUND.json`
- `round_type` should align with active round summary or registration file
- comparability statements should align with `comparability_report.json`
- configuration-change statements should align with `config_diff.json`
- artifact sufficiency and missingness statements should align with `artifact_digest.json`
- baseline identity fields should align with baseline registration context

If inconsistency is detected:
- mark decision invalid or manual-review-required
- block controller consumption until corrected

## JSON Quality Rules

All JSON contract files must satisfy:
- valid UTF-8 encoding
- valid standard JSON parse
- no comments
- arrays for multi-reason and multi-step fields
- explicit `null` only when truly unknown or not applicable
- required fields must not be omitted
- missing metrics must not be invented
- checkpoint binary content must not be embedded
- file paths should be round-relative or repo-relative when possible
- local absolute paths may appear only as execution-context metadata, not as public identity anchors

Quality enforcement rule:
- parser-valid but semantically incomplete files are still invalid for controller consumption.

## Fail-Closed Output Rules

Controller consumption must fail closed for any of the following:
- unknown `selected_action`
- unknown `decision_status`
- unknown `round_type`
- unknown `operating_mode`
- missing required fields
- decision references wrong round
- manual review required but action attempts automatic continuation
- `not_comparable` evidence attempts same-group promotion
- checkpoint binary path treated as copied artifact
- global outbox used as source
- synthetic rehearsal used as `formal_train` evidence

Allowed fail-closed outcomes:
- reject decision as invalid
- pause for manual review
- retain analysis-only path

## Minimal JSON Skeletons

### Minimal `gpt_decision.json` skeleton

```json
{
  "schema_version": "output_contract_v1",
  "round_id": "round_xxxx",
  "round_path": "rounds/round_xxxx",
  "generated_at": "YYYY-MM-DDTHH:MM:SSZ",
  "generated_by": "web_gpt",
  "decision_status": "recorded",
  "round_type": "formal_train_result",
  "operating_mode": "formal_train",
  "baseline_reference": {
    "baseline_round_id": "round_0001",
    "baseline_run_name": "value_bcchild_gated_statequery_effopt_formal_500k_decay240k_end005_20260422_210814",
    "baseline_role": "engineering_speed_baseline_candidate",
    "comparability_group": "effopt_formal_500k_decay240k_end005"
  },
  "evaluation_verdicts": ["neutral_or_mixed"],
  "selected_action": "pause_for_manual_review",
  "action_confidence": "medium",
  "comparability_status": "unknown",
  "artifact_sufficiency": "partial",
  "protocol_assessment": {},
  "config_assessment": {},
  "stability_assessment": {},
  "efficiency_assessment": {},
  "claim_boundary": ["diagnostic_only"],
  "manual_review_required": true,
  "codex_instruction_required": false,
  "controller_action_required": false,
  "reasons": [],
  "blocked_reasons": [],
  "required_next_steps": [],
  "evidence_references": [],
  "consumption_rules": {
    "controller_may_consume": false,
    "reason_if_not_consumable": "manual review required"
  }
}
```

### Minimal `gpt_decision_placeholder.json` skeleton

```json
{
  "schema_version": "output_contract_v1",
  "round_id": "round_xxxx",
  "decision_status": "not_requested",
  "reason": "No formal GPT decision was requested for this round.",
  "next_decision_stage": "after controller review",
  "created_at": "YYYY-MM-DDTHH:MM:SSZ"
}
```

Skeleton notes:
- skeleton values are placeholders
- do not treat skeleton as a real decision instance
- do not generate a real decision for `round_0001` unless explicitly requested

## Relationship To Other Protocol Docs

Cross-document boundary map:
- `00_gpt_entry_guide.md`: pointer authority, evidence authority, reading order
- `05_round_protocol.md`: round lifecycle and state meaning
- `07_tuning_policy.md`: allowed, frozen, and manual-review fields
- `08_evaluation_charter.md`: evaluation verdicts and metric interpretation
- `09_stopping_policy.md`: action semantics
- `10_output_contract.md`: machine-readable output contract

Operational reading rule:
- use lifecycle/tuning/evaluation/stopping documents to produce decision semantics
- use this document to serialize those semantics into safe, machine-readable round-scoped artifacts
