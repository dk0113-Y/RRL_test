# 05 Round Protocol

## Purpose

This document defines round lifecycle semantics, round types, state transitions, and publication/review/consumption rules for the automated tuning exchange system.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`
- `docs/03_current_mainline.md`
- `docs/04_automation_scope.md`

It coordinates round-level responsibilities across:
- `DRL_automatic`
- `DRL-path-finding`
- `RRL_test`
- Web GPT
- Codex

This document does not define:
- full artifact field schemas
- full tuning policy tables
- full stopping policy
- full decision output schemas

Those details belong to `06`, `07`, `09`, and `10` protocol documents.

## Definition Of A Round

A round is a bounded, auditable unit of protocol activity.

A round may:
- register a baseline
- run dry-run controller validation
- register a formal training result
- analyze existing evidence
- record protocol-review outcomes
- record failure or rejection outcomes

Core round identity rules:
- each round must have a stable `round_id`
- each round must live under `rounds/round_xxxx/`
- when active, each round must be discoverable through `CURRENT_ROUND.json`
- each round bundle must be self-contained enough for a fresh GPT reader to understand purpose, evidence condition, and decision status
- public `RRL_test` round numbering must not be consumed by routine controller-validation dry-runs by default
- creating a new public round requires explicit user approval

Interpretation rule:
- a round is not always equivalent to one newly launched training run
- `round_0001` is a baseline registration round that registers existing run evidence, not a newly launched training round

## Round Types

### `baseline_registration`

Purpose:
- register an existing run as current baseline anchor
- establish comparability starting context for later automation

Rules:
- no new training launch is required
- no method-performance superiority claim is implied
- `decision_required_from_gpt` may be false
- baseline identity and claim boundary must be explicit

Current instance:
- `round_0001` is currently this type
- baseline role is `engineering_speed_baseline_candidate`

### `protocol_review`

Purpose:
- docs migration
- schema cleanup
- exchange hardening
- controller review planning

Rules:
- no formal performance claim
- no training launch
- may produce protocol fixes and review notes

### `dry_run_no_train`

Purpose:
- validate `DRL_automatic` control flow without real training
- test config proposal and config diff generation
- test round directory creation and publication logic
- test missing-artifact placeholders and explicit dry-run reporting
- test decision consumption flow and pointer updates safely

Rules:
- missing training artifacts are expected only when explicitly marked as dry-run
- dry-run output cannot be treated as formal training evidence
- dry-run does not produce formal winner claims
- dry-run rounds may exist as local or private controller artifacts unless explicitly promoted to public exchange evidence

### `formal_train_result`

Purpose:
- register a real formal training result produced by `DRL-path-finding` under `formal_posthoc_trainselect_v1`

Rules:
- bundle must include sufficient structured evidence for review
- at minimum include config diff, artifact digest, comparability report, and round summary
- held-out final probe is applied only to posthoc-selected candidates
- automation must not reintroduce training-time validation or recheck

### `analysis_only`

Purpose:
- analyze incomplete, failed, historical, or reference-only evidence
- support diagnosis and protocol refinement

Rules:
- may produce analysis outputs
- cannot accumulate formal improvement claim
- cannot be promoted to formal winner state without a valid formal round

### `failed_or_rejected`

Purpose:
- preserve failed, invalid, incomplete, or rejected round outcomes

Rules:
- failure or rejection reason should be explicit
- failed or rejected rounds must remain traceable
- failed or rejected rounds must not become formal winners
- failed or rejected rounds must not be silently deleted

## Round Identity And Directory Rules

Round ID and path rules:
- round IDs should be sequential: `round_0001`, `round_0002`, ...
- each round must use one canonical directory: `rounds/round_xxxx/`
- active round is identified by:
  - `CURRENT_ROUND.json.current_round_id`
  - `CURRENT_ROUND.json.current_round_path`
- backup directories are reference-only and must not override active round selection
- global outbox is not used for current protocol decisions
- routine dry-run controller-validation artifacts must remain local or private unless explicitly approved for public publication

Minimum round bundle requirements (or explicit replacement marker with reason):
- `index_manifest.json`
- `round_summary.json`
- `artifact_digest.json`
- `config_diff.json`
- `comparability_report.json`
- `gpt_decision.json` or `gpt_decision_placeholder.json`

Additional type-specific requirements:
- baseline registration rounds should include `baseline_registration.json`
- dry-run rounds should include explicit dry-run marker and missing-artifact explanation
- failed or rejected rounds should include failure or rejection reason

Scope note:
- this section defines round-level minimum bundle requirements only
- detailed artifact roles are defined in `docs/06_formal_artifact_map.md`

## Lifecycle Overview

Standard lifecycle stages:
1. `round_planning`
2. `config_proposal_or_registration`
3. `preflight_validation`
4. `execution_or_registration`
5. `artifact_collection`
6. `bundle_generation`
7. `exchange_publication`
8. `gpt_review_or_placeholder`
9. `decision_consumption`
10. `round_closure_or_next_round`

Applicability rule:
- not every round type uses every stage identically
- baseline registration skips real training execution
- dry-run skips real training execution but must still test publication and decision flow
- formal training results should use full execution and evidence path

## Lifecycle Stage Rules

### 1) `round_planning`

Typical owner:
- `DRL_automatic` or human reviewer

Must determine:
- round type
- intended operating mode
- baseline reference
- allowed tuning scope
- expected outputs and review expectation

Must not:
- assume execution is allowed before preflight passes

### 2) `config_proposal_or_registration`

For formal and dry-run rounds:
- generate candidate configuration proposal
- generate config diff against active baseline or stated reference

For baseline registration rounds:
- register existing run identity
- record source run directory or source run identity
- do not claim new training configuration improvement by default

Must ensure:
- round intent is consistent with type
- evidence expectations are consistent with mode

### 3) `preflight_validation`

Must validate:
- allowed tuning fields
- frozen fields
- comparability group assignment logic
- mode validity
- checkpoint binary publication exclusion
- no stale outbox dependency
- no unapproved mainline semantic change
- no unapproved formal protocol change

Fail-closed rule:
- preflight failure must block execution
- when appropriate, preflight failure should create a reviewable failure record

Prohibited transition:
- preflight-failed rounds must not enter `running`

### 4) `execution_or_registration`

`formal_train_result` path:
- `DRL_automatic` invokes `DRL-path-finding`
- training runs under formal protocol

`dry_run_no_train` path:
- controller validates path without real training execution

`baseline_registration` path:
- existing run is registered without launching training

Must ensure:
- execution behavior matches round type
- registration behavior does not pretend to be execution

### 5) `artifact_collection`

Must collect when applicable:
- logs
- train-side statistics
- final probe outputs (for selected candidates under formal protocol)
- checkpoint metadata and hashes

Must not collect into exchange repo:
- checkpoint binaries

Missingness rule:
- missing artifacts must be explicitly recorded
- missingness must not be hidden by synthetic placeholders unless clearly marked

### 6) `bundle_generation`

Must generate:
- round directory
- index manifest
- round summary
- config diff
- artifact digest
- comparability report
- GPT decision placeholder when formal decision is not requested

Type-specific additions:
- baseline registration should include baseline registration file
- dry-run should include explicit dry-run markers
- failed or rejected should include failure reason

Must ensure:
- bundle is machine-readable
- bundle claim scope matches available evidence

### 7) `exchange_publication`

Must publish:
- round bundle into `RRL_test`

Pointer update rule:
- update `CURRENT_ROUND.json` only when bundle is valid for active pointer transition

Integrity rule:
- partial bundles must not be published as valid formal rounds unless explicitly marked incomplete or failed
- publication should preserve traceability for audit and review

### 8) `gpt_review_or_placeholder`

If `decision_required_from_gpt=true`:
- GPT may review and produce formal decision artifact

If `decision_required_from_gpt=false`:
- create decision placeholder
- do not infer formal decision implicitly

Round-type note:
- baseline registration rounds commonly use placeholder state

### 9) `decision_consumption`

Consumer:
- `DRL_automatic`

Consumption rules:
- consume decision only if it is valid and current
- must not consume stale decisions
- must not consume backup directories
- must not consume old outbox files

Fail-closed rule:
- if decision is missing, invalid, or ambiguous when required, pause for manual review

### 10) `round_closure_or_next_round`

Must:
- close accepted, rejected, failed, or analysis-only rounds explicitly
- create new round ID for each next round
- preserve prior round evidence without overwrite
- keep rejected or failed rounds traceable

Must not:
- rewrite previous round outcomes silently
- hide failed outcomes by pointer-only rewrites without explicit correction records

## State And Status Vocabulary

Recommended `current_phase` values:
- `baseline_registration`
- `protocol_review`
- `dry_run_no_train`
- `formal_training_result`
- `analysis_only`
- `failed_or_rejected`
- `awaiting_new_round`

Recommended `status` values:
- `planned`
- `preflight_passed`
- `preflight_failed`
- `running`
- `registered`
- `published`
- `review_requested`
- `decision_recorded`
- `consumed`
- `closed`
- `failed`
- `rejected`
- `paused_for_manual_review`

Semantics note:
- exact JSON field schema is defined by output-contract and controller implementation
- this document defines semantic meaning for lifecycle interpretation

## Transition Rules

Allowed transition expectations:
- `planned` may transition to `preflight_passed` or `preflight_failed`
- `preflight_failed` must not transition to `running`
- `preflight_passed` may transition to:
  - `running` for `formal_train_result`
  - `published` for some `dry_run_no_train` or `protocol_review` bundles after non-training checks
- `running` may transition to artifact collection path or `failed`
- failed runs must preserve failure evidence
- `published` may transition to `review_requested` or `decision_recorded` depending on `decision_required_from_gpt`
- `decision_recorded` may transition to `consumed` by `DRL_automatic`
- consumed decisions may lead to next round proposal, pause, stop, branch, or manual review routing

Global transition constraints:
- no transition may overwrite previous round evidence
- no transition may use global outbox
- no failed-preflight round may bypass to execution
- no failed or incomplete round may be promoted as formal winner

## Formal Train Specific Rules

Formal training rounds must obey `formal_posthoc_trainselect_v1`.

Protocol-level constraints:
- no validation or recheck during training
- training phase saves checkpoints and train-side statistics only
- candidate checkpoints are selected posthoc after training
- held-out final probe is applied only to selected candidates
- formal winner is registered or copied as `best.pt`
- `last.pt` is terminal diagnostic only
- best-vs-last gap must be recorded or explicitly flagged when available

Automation constraint:
- controller logic must not reintroduce training-time eval loops under this protocol unless protocol migration is manually approved

## Baseline Registration Specific Rules

Baseline registration rounds must:
- register existing run evidence and identity
- preserve baseline claim boundaries
- avoid introducing new training claims by default

Evidence expectation note:
- baseline registration may not contain every artifact expected from future formal training rounds
- missing legacy artifacts such as `eval_metrics.csv` must not invalidate registration by default

Current baseline statement:
- `round_0001` is the current baseline registration round
- its role is engineering speed baseline candidate
- it is not method-performance superiority evidence

Decision expectation:
- `decision_required_from_gpt` may be false
- decision placeholder is acceptable when formal decision is not requested

## Failure And Rollback Rules

Failure retention rules:
- failed runs must not be silently deleted
- failed rounds should preserve failure reason and missing-artifact state
- failed or incomplete rounds cannot become formal winners

Rollback rules:
- rollback must not delete published evidence
- if active pointer is wrong, correct pointer explicitly rather than silently rewriting history
- corrective updates should keep an auditable trail

Protocol-violation handling:
- accidental checkpoint publication is a protocol violation
- violation must be corrected with explicit removal and corrective commit history
- correction must not erase the record that a violation occurred

## Relationship To Other Protocol Docs

Cross-document boundaries:
- `02_system_architecture.md`: repository and agent ownership
- `04_automation_scope.md`: allowed modes and automation boundaries
- `05_round_protocol.md`: round lifecycle and state transitions
- `06_formal_artifact_map.md`: detailed artifact roles and evidence requirements
- `07_tuning_policy.md`: config changes and comparability groups
- `09_stopping_policy.md`: stop, continue, branch, and manual-review policy
- `10_output_contract.md`: exact decision and controller-action schema

Use-order guidance:
- use this document to validate lifecycle correctness
- use artifact, tuning, stopping, and output docs for deeper per-topic constraints
- where conflict appears, apply entry-guide authority rules and fail closed until resolved
