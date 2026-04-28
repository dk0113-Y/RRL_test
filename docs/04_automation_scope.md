# 04 Automation Scope

## Purpose

This document defines what the automation system may and may not do in the current automated tuning stage.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`
- `docs/03_current_mainline.md`

It fixes operating boundaries for:
- `DRL_automatic` controller behavior
- Web GPT decision-review behavior
- Codex bounded modification behavior inside automation workflows

It does not define:
- detailed round lifecycle state transitions
- full tuning parameter policy
- full stopping decision policy
- full decision output schema

Those details are defined in `05`, `07`, `09`, and `10` protocol documents.

## Scope Summary

Automation posture:
- The automation system is a controlled semi-automated tuning loop.
- It is designed to improve training-process efficiency and propose future configurations without breaking comparability.
- It must preserve current method semantics and formal protocol constraints.
- It is currently in protocol hardening and controller-review stage.

Current context constraints:
- project stage is `automated_tuning_system_design`
- active round is `round_0001`
- active phase is `baseline_registration`
- baseline role is `engineering_speed_baseline_candidate`
- `decision_required_from_gpt` is currently false
- `round_0001` must not be interpreted as method-performance superiority evidence

Current formal anchors to preserve:
- formal protocol is `formal_posthoc_trainselect_v1`
- method mainline remains:
  - Dynamic Cumulative Belief Map
  - Shared Semantic Layer
  - Dual-State Input / Dual-Branch Encoding
  - Semantic Dueling Decision Head
- formal input keys remain:
  - `advantage_canvas`
  - `value_block_features`
  - `value_entry_features`
  - `value_block_mask`
  - `value_entry_mask`
- runtime baseline toggles remain:
  - AMP false
  - inference AMP false
  - `torch.compile` false
  - `channels_last` false
  - TF32 true
  - `cudnn_benchmark` true

This automation system is explicitly not:
- unrestricted AutoML
- architecture search
- reward redesign engine
- runtime-only benchmark loop
- synthetic rehearsal benchmark
- autonomous training launcher without protocol approval

## Supported Operating Modes

### `protocol_review`

Use cases:
- docs migration
- schema review
- exchange protocol cleanup
- controller interface review

Rules:
- does not imply training performance claims
- may generate Codex instructions for docs updates or controller-code review tasks
- must not launch training

### `dry_run_no_train`

Use cases:
- test `DRL_automatic` configuration generation
- test round directory generation
- test config diff generation
- test artifact placeholder and missing-artifact handling
- test `CURRENT_ROUND.json` update logic
- test GPT decision consumption path without real training

Rules:
- should be completed before real automated training
- may not generate formal performance claims
- missing training artifacts are expected only when explicitly marked as dry-run outputs

### `formal_train`

Use cases:
- controlled real training execution through `DRL_automatic` and `DRL-path-finding`
- formal evidence generation for future comparable rounds

Rules:
- should run only after protocol and dry-run validation
- must obey `formal_posthoc_trainselect_v1`
- must preserve frozen mainline fields
- must publish a structured round bundle to `RRL_test`
- must not run when config violates frozen fields or comparability guard

### `analysis_only`

Use cases:
- inspect existing artifacts
- diagnose failed or incomplete runs
- compare reference-only material

Rules:
- must not accumulate formal improvement claims
- can support debugging and protocol-fix prioritization

### `synthetic_rehearsal`

Use cases:
- protocol or bridge validation only

Rules:
- must never be used as formal training evidence
- must not be mixed with `formal_train` results in the same evidence or comparability claims

## Allowed Automation Activities

Automation is allowed to:
- generate candidate configuration proposals within allowed tuning fields
- validate frozen fields before run launch
- assign comparability group identity under policy
- invoke training only when mode and protocol permit
- collect and summarize artifacts
- register checkpoint metadata and hashes without copying checkpoint binaries
- publish round bundles into `RRL_test`
- generate `config_diff.json`
- generate `artifact_digest.json`
- generate `comparability_report.json`
- generate `round_summary.json`
- read and apply `gpt_decision.json` when present and valid
- pause execution and request manual review when scope is unclear

Allow-list rule:
- if a controller action cannot be mapped to an allowed activity, it must be blocked and escalated for review.

## Prohibited Automation Activities

Automation is prohibited from:
- free network architecture search
- automatic reward semantics redesign
- automatic state tensor schema changes
- automatic Shared Semantic Layer semantic changes
- automatic value-branch semantic changes
- automatic formal protocol changes
- uncontrolled runtime toggle exploration
- launching real training before protocol and dry-run validation
- treating failed or incomplete runs as formal winners
- mixing incomparable runs into the same comparability group
- using synthetic rehearsal as formal evidence
- using global outbox as formal decision source
- uploading `.pt` / `.pth` / `.ckpt` files to `RRL_test`
- copying full raw outputs directories into `RRL_test`
- treating `round_0001` as method-performance superiority evidence

Block rule:
- prohibited actions must fail closed, not fail open.

## Manual Review Required

The following fields and changes require manual review:
- reward structure or reward semantics
- core network architecture
- state tensor schema
- Dynamic Cumulative Belief Map semantics
- Shared Semantic Layer semantics
- Dual-State Input / Dual-Branch Encoding role separation
- Semantic Dueling Decision Head semantics
- value branch semantics
- `formal_posthoc_trainselect_v1` protocol changes
- held-out final probe definition
- checkpoint winner policy
- environment distribution
- evaluation seed policy
- major runtime mode affecting numerical behavior

Manual-review rules:
- manual review does not automatically approve comparability
- reviewed semantic changes may still require a new comparability group
- until approval and regrouping are explicit, controller must treat such changes as blocked

## Routine Automation Scope

Routine automation scope is outer-loop and protocol-compatible only.

Examples of routine fields:
- `total_env_steps`
- `epsilon_decay_steps`
- `epsilon_end`
- warmup settings
- posthoc candidate selection settings
- final probe candidate count
- stopping or extension policy parameters
- logging and artifact summarization settings
- controller dry-run checks

Boundary notes:
- this section defines scope boundaries, not an exhaustive policy table
- authoritative tuning rules are defined in `docs/07_tuning_policy.md`
- if a proposed field touches frozen semantics, it exits routine scope and requires manual review

## Scope Guards For DRL_automatic

Before real automated training resumes, `DRL_automatic` must implement or pass review for these guards:
- explicit mode flag and mode-specific execution path
- dry-run and no-train capability
- frozen-field validation
- allowed-tuning-field validation
- comparability group assignment logic
- config diff generation
- run identity tracking
- source commit and baseline identity tracking
- artifact discovery and missing-artifact handling
- checkpoint binary exclusion from exchange publication
- round publication validation
- GPT decision consumption guard
- rollback and failed-run handling
- no stale outbox dependency

Guard quality requirements:
- guards must be machine-enforced, not documentation-only
- guard failures must be explicit and auditable
- guard outcomes must be reflected in round evidence and controller logs

Readiness rule:
- these guards must be reviewed before real automated training is resumed.

## Relationship To Formal Protocol

Automation scope must respect `formal_posthoc_trainselect_v1`.

Required alignment:
- training-time validation or recheck must not be reintroduced by automation unless protocol migration is manually approved
- candidate selection remains posthoc and train-side
- held-out final probe is applied only to selected candidates
- `last.pt` remains diagnostic, not formal winner

Protocol-consistency rule:
- automation convenience must not rewrite protocol semantics.

## Relationship To Other Protocol Docs

Cross-document scope map:
- `02_system_architecture.md`: repository and agent ownership boundaries
- `03_current_mainline.md`: frozen method semantics
- `04_automation_scope.md`: allowed and prohibited automation activities
- `05_round_protocol.md`: detailed round lifecycle and state transitions
- `07_tuning_policy.md`: allowed tuning fields and comparability groups
- `09_stopping_policy.md`: stop, continue, branch, and manual-review decision policy
- `10_output_contract.md`: GPT decision and controller-action schema

Use-order note:
- this document constrains controller behavior
- lifecycle, tuning, stopping, and output details come from their dedicated docs

## Non-Goals

This document does not authorize:
- immediate training launch
- method redesign
- reward redesign
- architecture migration
- checkpoint upload to exchange repo
- outbox revival
- formal promotion claim for `round_0001`

It also does not provide:
- full round state machine
- full tuning parameter table
- full output schema
- implementation-level code interface specification

Non-goal enforcement rule:
- if an action is justified only by material outside scope, treat it as out-of-scope until explicit protocol approval is recorded.
