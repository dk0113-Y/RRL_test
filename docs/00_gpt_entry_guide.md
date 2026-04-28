# 00 GPT Entry Guide

## Purpose

This file is the first entry point for any fresh GPT session, Codex agent, or human reviewer who opens this exchange repository. It defines where to start, how to route reading, how to rank evidence, and how to avoid protocol mistakes.

This guide is intentionally narrow. It is not the full protocol compendium. It must be used to establish orientation and decision boundaries before any round judgement or instruction drafting.

This guide covers:
- entry routing
- current-state resolution
- reading order
- evidence priority
- round-phase behavior
- decision boundary and failure prevention

This guide does not replace the detailed protocol specifications. Full semantics and schemas must be read from the numbered protocol documents (`01` to `10`) as they are introduced and finalized.

## First Rule: Resolve Current State

Read first:
- `CURRENT_ROUND.json`

No round-level judgement is valid unless it is anchored to `CURRENT_ROUND.json`. Every reader must resolve these fields before opening any round directory:
- `current_round_id`
- `current_round_path`
- `current_phase`
- `status`
- `baseline_run_name`
- `next_expected_action`
- `decision_required_from_gpt`

Mandatory rules:
- If `current_round_id` is null or empty, you must not generate a round verdict.
- If `current_phase` is `baseline_registration`, you may confirm baseline registration state only. You must not infer or claim significant method-performance superiority.
- If `decision_required_from_gpt` is `false`, you must not proactively generate a formal `gpt_decision.json` unless the user explicitly requests one.
- If `current_round_path` points to a specific round, only that round is the active round for current judgement.
- Backup directories, historical snapshots, and legacy exchange leftovers must not override `CURRENT_ROUND.json` pointers.

Current repository baseline context at the time of writing:
- active round: `round_0001`
- round nature: baseline registration
- baseline role: engineering speed baseline candidate
- formal protocol: `formal_posthoc_trainselect_v1`

For `round_0001`, readers must preserve claim boundaries:
- this is a baseline registration round
- this is not a claim of method-performance superiority

## Repository Role Summary

High-level role map:
- `DRL-path-finding` = Training Execution Repository
- `DRL_automatic` = Automation Control Repository
- `RRL_test` = Public Exchange and Protocol Repository
- Web GPT = Decision Review Agent
- Codex = Bounded Code Modification Agent

Use this map to avoid role confusion:
- training outputs come from the training execution repo
- automation orchestration and control logic belong to the control repo
- protocol records, round bundles, and judgement artifacts belong in this exchange repo

Detailed architecture, interfaces, and responsibility contracts are handled in:
- `docs/02_system_architecture.md`

## Long-Term Docs Reading Order

Target numbered structure (some files may not exist yet):
1. `docs/00_gpt_entry_guide.md`
2. `docs/01_project_context.md`
3. `docs/02_system_architecture.md`
4. `docs/03_current_mainline.md`
5. `docs/04_automation_scope.md`
6. `docs/05_round_protocol.md`
7. `docs/06_formal_artifact_map.md`
8. `docs/07_tuning_policy.md`
9. `docs/08_evaluation_charter.md`
10. `docs/09_stopping_policy.md`
11. `docs/10_output_contract.md`

Migration note:
- the repository may still contain legacy file names such as `project_context.md`, `current_mainline.md`, `gpt_index_guide.md`, and `reading_order.md`
- migration to numbered docs is incremental
- this file is the entry for the numbered system, but migration is not yet complete

Operational reading rule during migration:
- read this guide first
- then read the best-available equivalent for each numbered topic
- treat naming mismatch as migration state, not as protocol contradiction

## Current Round Evidence Reading Order

When `CURRENT_ROUND.json` points to an active round, read in this order:
1. `rounds/<current_round>/index_manifest.json`
2. `rounds/<current_round>/round_summary.json`
3. `rounds/<current_round>/comparability_report.json`
4. `rounds/<current_round>/config_diff.json`
5. `rounds/<current_round>/artifact_digest.json`
6. `rounds/<current_round>/baseline_registration.json`, if this is a baseline registration round
7. `rounds/<current_round>/artifacts/metadata/csv_summaries.json`, if present
8. `rounds/<current_round>/artifacts/logs/*`, only when detailed inspection is necessary
9. `rounds/<current_round>/gpt_decision.json` or `rounds/<current_round>/gpt_decision_placeholder.json`

Required-evidence guardrails for the new protocol:
- do not require `metric_snapshot.json`, `benchmark_summary.json`, or `artifact_index.json` as mandatory evidence in the new round protocol
- if such files exist in historical or backup material, treat them as legacy/reference only
- legacy files must not override the required evidence set for current active rounds

## Evidence Priority

Use this exact evidence priority stack.

Tier 0:
- `CURRENT_ROUND.json`

Tier 1:
- protocol docs under `docs/`

Tier 2:
- `rounds/<current_round>/index_manifest.json`

Tier 3:
- structured round JSON evidence:
- `round_summary.json`
- `comparability_report.json`
- `config_diff.json`
- `artifact_digest.json`
- `baseline_registration.json` (when applicable)

Tier 4:
- lightweight copied logs and summaries:
- `artifacts/metadata/csv_summaries.json`
- `artifacts/logs/*.csv`

Tier 5:
- human-readable auxiliary text (if present)

Tier 6:
- backup directories, historical notes, and legacy files
- reference only
- must not override active round files

Interpretation rules:
- higher tier always dominates lower tier if conflict exists
- if Tier 3 structured files conflict with Tier 5 prose, trust Tier 3
- if Tier 0 points to a different round than a backup index claims, trust Tier 0

## Round Phase Handling

Behavior must be phase-aware. Do not use one phase policy for all phases.

`baseline_registration`:
- confirm baseline identity
- confirm claim boundary
- do not output method-superiority claims
- do not propose automatic hyperparameter changes unless user explicitly asks for planning

`formal_training_result`:
- inspect comparability, config diff, artifact digest, final probe-related evidence, and train-side summary artifacts as available
- generate formal decision only when requested by user or when `decision_required_from_gpt = true`

`protocol_review`:
- review docs, schemas, and controller interfaces
- do not infer training performance from protocol-only context

`analysis_only`:
- analyze available evidence
- do not accumulate formal comparison claims

`awaiting_new_round` or no active round:
- stay in docs-only context
- do not invent round metrics
- do not invent decisions for non-existent bundles

## Decision Boundary

What GPT may do:
- read exchange protocol
- review current round evidence
- evaluate comparability boundaries
- generate `gpt_decision.json` when explicitly required
- generate Codex instructions when necessary for bounded changes
- identify protocol gaps and ambiguities

What GPT must not do:
- directly launch training
- directly modify training code in `DRL-path-finding` from exchange-only judgement context
- redefine method semantics without explicit protocol migration
- treat synthetic rehearsal as formal evidence
- use old near/mid/token framing to override the current formal mainline
- treat global `outbox` as a formal decision source
- invent missing artifact values
- treat checkpoint binaries as exchange evidence artifacts
- upload `.pt`, `.pth`, or `.ckpt` files to this exchange repo

Current method and runtime invariants to respect in round interpretation:
- method mainline includes Dynamic Cumulative Belief Map, Shared Semantic Layer, Dual-State Input / Dual-Branch Encoding, and Semantic Dueling Decision Head
- formal input keys are `advantage_canvas`, `value_block_features`, `value_entry_features`, `value_block_mask`, and `value_entry_mask`
- baseline runtime toggles are AMP off, inference AMP off, `torch.compile` off, `channels_last` off, TF32 on, and `cudnn_benchmark` on

These anchors are part of interpretation constraints. They must not be silently rewritten by ad hoc narrative.

## Common Failure Modes

Avoid these high-frequency errors:
- reading an old backup round as if it were the active round
- applying legacy `eval_metrics`-centric logic directly to `formal_posthoc_trainselect_v1`
- treating `round_summary.json` as stronger evidence than `artifact_digest.json` when conflict exists
- confusing source repository identity with local workstation path
- treating `round_0001` as proof of method-performance improvement
- ignoring best-vs-last gap risk when interpreting stability
- assuming missing `eval_metrics.csv` invalidates baseline registration by default
- restoring global outbox workflow as formal protocol path
- allowing automatic tuning scope to drift into reward-semantics or network-architecture modifications

Failure containment rules:
- when evidence is missing, record missingness explicitly
- when comparability is uncertain, downgrade confidence and request review
- when protocol naming is mixed old/new, follow Tier 0 and Tier 3 anchors first

## Handoff

After this entry guide, continue to the detailed protocol docs:
- project background: `docs/01_project_context.md`
- system architecture: `docs/02_system_architecture.md`
- current method semantics: `docs/03_current_mainline.md`
- automation scope: `docs/04_automation_scope.md`
- round lifecycle: `docs/05_round_protocol.md`
- artifact roles: `docs/06_formal_artifact_map.md`
- tuning boundary: `docs/07_tuning_policy.md`
- evaluation criteria: `docs/08_evaluation_charter.md`
- stopping policy: `docs/09_stopping_policy.md`
- output schema: `docs/10_output_contract.md`

Until migration is complete, use the best existing equivalents in `docs/` while preserving the constraints defined in this entry guide.
