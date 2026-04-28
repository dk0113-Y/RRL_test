# 01 Project Context

## Purpose

This document defines the project-level context contract for the current automated tuning system phase.

It must be read after `docs/00_gpt_entry_guide.md`.

It fixes:
- project identity
- current stage and operating posture
- current formal mainline anchors
- current baseline registration context
- source-of-truth boundaries
- claim boundaries for formal communication

It does not define detailed architecture, full method internals, full tuning policy tables, full evaluation criteria, full stopping logic, or output schemas. Those are defined in the corresponding numbered protocol documents.

## Project Identity

This project is a DRL autonomous exploration formal training and automated hyperparameter tuning exchange system.

Repository role contract:
- `DRL-path-finding` is the training execution repository.
- `DRL_automatic` is the automation control repository.
- `RRL_test` is the public exchange and protocol repository.
- Web GPT is the decision review agent.
- Codex is the bounded code modification agent.

System goal:
- controlled, comparable, reviewable automated tuning
- not unrestricted AutoML

This project is explicitly not:
- a new unrestricted DRL method redesign
- a reward-search playground
- a runtime-only optimization campaign
- a synthetic rehearsal benchmark

All decisions and artifacts must preserve comparability and reviewability. Any change that breaks those properties must be flagged for manual review before adoption.

## Current Stage

Current stage is `automated_tuning_system_design`.

Current operating facts:
- active round is `round_0001`
- `round_0001` is registered as a baseline registration round
- current work priority is protocol design alignment in the exchange repository and subsequent control-flow review in `DRL_automatic`

Immediate next action at project level:
- review `DRL_automatic` control flow and validate dry-run / no-train publication behavior

Execution constraints for this stage:
- do not launch new formal training before protocol and dry-run validation are completed
- do not generate formal GPT decisions when `decision_required_from_gpt=false`, unless the user explicitly requests one
- do not interpret protocol-migration state as permission to bypass comparability boundaries

This stage is a control and protocol hardening stage first, not a free experiment launch stage.

## Current Formal Mainline

Current formal mainline (high-level contract):
- Dynamic Cumulative Belief Map
- Shared Semantic Layer
- Dual-State Input / Dual-Branch Encoding
- Semantic Dueling Decision Head

Current formal input keys:
- `advantage_canvas`
- `value_block_features`
- `value_entry_features`
- `value_block_mask`
- `value_entry_mask`

Narrative boundary:
- old near/mid/token framing is historical/reference only
- it must not be used to redefine the current formal method narrative

Mainline semantics must remain stable unless an explicit protocol-level review approves migration.

## Formal Protocol Status

Current formal protocol:
- `formal_posthoc_trainselect_v1`

Core protocol rules:
- no validation or recheck during training
- training phase saves checkpoints and train-side statistics only
- post-training, train-side posthoc candidate selection chooses candidate checkpoints
- held-out final probe is applied only to selected candidates
- formal winner is copied or registered as `best.pt`
- `last.pt` is only an end-of-training diagnostic checkpoint, not the formal winner
- best-vs-last gap is a stability risk that must be monitored in later automated tuning

Protocol contamination guardrails:
- do not describe the current protocol as eval-metrics-centric
- do not state that `best.pt` is selected by periodic `eval_success_rate` unless that is explicitly true for a specific legacy round
- do not require `eval_metrics.csv`, `metric_snapshot.json`, `benchmark_summary.json`, or `artifact_index.json` as mandatory evidence for the current new baseline-registration protocol

## Current Baseline: round_0001

Registered baseline identity:
- round_id: `round_0001`
- round_type: `baseline_registration`
- baseline_role: `engineering_speed_baseline_candidate`
- baseline_run_name: `value_bcchild_gated_statequery_effopt_formal_500k_decay240k_end005_20260422_210814`

Current baseline conclusion boundary:
- runtime improved from about 12718.33s to about 11188.02s
- estimated speedup is about 12.03%
- formal outcome is basically preserved
- winner checkpoint remains 480k
- best-vs-last gap remains relatively large
- `round_0001` is the default starting anchor for later automated tuning
- `round_0001` must not be cited as method-performance superiority evidence

Runtime baseline toggles for this registered baseline context:
- AMP: false
- inference AMP: false
- torch.compile: false
- channels_last: false
- TF32: true
- cudnn benchmark: true

These baseline statements are calibration anchors for upcoming controlled tuning rounds, not proof of a new method class.

## Source-of-Truth Boundaries

The project uses four fact domains. They must not be mixed casually.

Training facts:
- source: `DRL-path-finding` real outputs, logs, checkpoint metadata, and training artifacts
- meaning: what the training process actually produced

Automation facts:
- source: `DRL_automatic` configuration proposals, orchestration behavior, artifact parsing, and round publication logic
- meaning: what the automation controller intended and executed in control flow

Exchange facts:
- source: current active round under `RRL_test/rounds/<current_round>/`
- especially structured JSON files such as:
  - `round_summary.json`
  - `artifact_digest.json`
  - `config_diff.json`
  - `comparability_report.json`
  - `baseline_registration.json`
- meaning: what is publicly registered for the active round bundle

Decision facts:
- source: `rounds/round_xxxx/gpt_decision.json` when it exists
- placeholder files are not formal decisions

Boundary rules:
- local absolute paths are execution context, not public truth identity
- repository identity must be used for public source-of-truth statements
- backup directories are reference-only
- synthetic rehearsal is not formal evidence
- global outbox is not a formal decision source
- checkpoint binaries must not be uploaded to `RRL_test`

## Claim Boundaries

The following claims are prohibited under the current context contract:
- Do not claim `round_0001` proves significant method-performance improvement.
- Do not treat engineering runtime speedup as method innovation.
- Do not invalidate baseline registration solely because `eval_metrics.csv` is absent.
- Do not use old near/mid/token semantics to override the current mainline.
- Do not let automated tuning modify reward semantics, core network architecture, state tensor schema, or formal protocol without manual review.
- Do not treat `last.pt` as the formal winner.
- Do not treat checkpoints without held-out final probe as final formal conclusions.
- Do not use synthetic rehearsal or backup directories to accumulate formal evidence.

Claim-generation rules:
- evidence must be tied to the active round and its structured files
- missing evidence must be stated explicitly
- unsupported extrapolation must be marked as analysis-only, not formal conclusion

## Relationship To Other Protocol Docs

Numbered protocol scope map:
- `00_gpt_entry_guide.md`: entry routing, evidence authority, and reading order
- `01_project_context.md`: project identity, current stage, baseline context, and source-of-truth boundaries
- `02_system_architecture.md`: three-repo architecture and agent responsibilities
- `03_current_mainline.md`: method semantics and frozen formal mainline
- `04_automation_scope.md`: automation boundaries and permitted operating modes
- `05_round_protocol.md`: round lifecycle and state transitions
- `06_formal_artifact_map.md`: artifact roles and evidence requirements
- `07_tuning_policy.md`: allowed tuning fields, frozen fields, and comparability groups
- `08_evaluation_charter.md`: metric interpretation and evaluation criteria
- `09_stopping_policy.md`: stop, continue, branch, and review criteria
- `10_output_contract.md`: GPT decision schema and controller-consumable outputs

Legacy unnumbered protocol wording may still appear in historical or backup material. In case of overlap, apply numbered-doc intent and constraints, and treat stale legacy wording as reference-only.

## Recommended Immediate Work

Recommended immediate work sequence:
1. review `DRL_automatic` control flow and exchange publishing logic;
2. verify dry-run / no-train generation of a future `round_0002` proposal;
3. confirm preflight and publication guards behave as documented;
4. only after protocol and dry-run validation should real automated training be launched.

Execution note:
- protocol consistency and dry-run traceability must be verified before real automated training
- any ambiguity discovered during migration should be resolved in numbered docs before controller expansion
