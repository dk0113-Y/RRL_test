# GPT Index Guide

## Purpose

This file is the stable exchange-side index prompt for a fresh GPT chat.

- Use the public exchange repo to read the formal research context for `formal_train`.
- Build a file-role map first, then read the current round bundle.
- Do not treat summary files as hard evidence when the underlying artifacts exist.

## Entry Rule

Start from `CURRENT_ROUND.json`.

- Use it to confirm whether an active round bundle exists.
- Use `recommended_entry_docs` and `stable_context_files` to locate the long-term docs layer.
- If `exchange_state = awaiting_new_round_publish`, stay on the long-term docs layer and do not invent a round verdict.

## Long-Term Docs Layer

These files are stable context, not round-local evidence.

- `docs/project_context.md`
  - Project goal, source-of-truth identity, exchange responsibility, and high-level evidence status.
- `docs/current_mainline.md`
  - Current formal architecture/mainline; use this to avoid drifting back to historical semantics.
- `docs/evaluation_charter.md`
  - Long-term judging standards and what counts as primary, stability, and efficiency evidence.
- `docs/stopping_policy.md`
  - Valid controller actions and the conditions for stop, pause, run, or analyze-only.
- `docs/output_contract.md`
  - Required GPT output shape and decision-payload rules.
- `docs/formal_artifact_map.md`
  - Formal artifact contract and exchange-facing truth-identity rules.
- `docs/automation_scope.md`
  - Lifecycle and scope boundaries across `formal_train` and `synthetic_rehearsal`.
- `docs/tuning_policy.md`
  - What may change and what must stay frozen for comparable formal series.
- `docs/reading_order.md`
  - High-level read sequence across the long-term docs layer and the current round evidence layer.

Use them by role:

- Long-term goal and truth boundary: `project_context.md`
- Current mainline semantics: `current_mainline.md`
- Judging standards: `evaluation_charter.md`
- Stop/pause/run policy: `stopping_policy.md`
- Output format: `output_contract.md`
- Artifact and truth-repo contract: `formal_artifact_map.md`
- Lifecycle scope: `automation_scope.md`
- Tunable vs frozen boundary: `tuning_policy.md`
- Sequence control: `reading_order.md`

## Current Round Evidence Layer

When `CURRENT_ROUND.json` points to an active round, read the files under `rounds/<current_round>/` by evidence tier.

### Bundle Locator And Completeness

- `index_manifest.json`
  - Bundle locator, file inventory, and top-level metadata for the active round.
- `artifact_index.json`
  - Check whether required artifacts are actually present before trusting any verdict text.

### Primary Hard Evidence

- `comparability_report.json`
  - Read first for formal comparability status and gating.
- `metric_snapshot.json`
  - Main performance packet: recent train, best eval, last eval, final probe.
- `config_snapshot.json`
  - Run contract, config, git identity, and comparability-relevant fields.
- `benchmark_summary.json`
  - Runtime and timing support evidence; use as efficiency support, not as the only verdict source.

### Historical Calibration Evidence

- `historical_baseline_summary.json`
  - Bootstrap historical thresholds and calibration sufficiency for the current decision frame.

### Structured Summary Layer

- `round_summary.json`
  - Structured synthesis of the round. Useful, but it must not override deeper evidence when files disagree.

### Auxiliary Text Layer

- `codex_report.md`
  - Helpful analysis text from Codex. It is not a substitute for the structured JSON artifacts.
- `gpt_input.md`
  - A packaged GPT-facing input view. It is not the only fact source and must not override the bundle artifacts.

## Recommended Reading Order

Read in this order because each step prevents a common failure mode.

1. `CURRENT_ROUND.json`
   - Determine whether there is an active round at all and whether the repo is in clean waiting state.
2. `docs/gpt_index_guide.md`
   - Build the file-role map before reading any summary or verdict file.
3. Long-term docs in this order:
   - `docs/project_context.md`: lock project goal and truth boundary.
   - `docs/current_mainline.md`: lock current semantics.
   - `docs/evaluation_charter.md`: lock judging criteria.
   - `docs/stopping_policy.md`: lock valid decision actions.
   - `docs/output_contract.md`: lock output format.
   - `docs/formal_artifact_map.md`: lock artifact contract and truth-repo wording.
   - `docs/automation_scope.md` and `docs/tuning_policy.md`: confirm scope and frozen boundaries.
4. `rounds/<current_round>/index_manifest.json`
   - Confirm the bundle path and expected file set before reading verdict content.
5. `rounds/<current_round>/comparability_report.json`
   - Formal claims are gated here; read before discussing improvement or regression.
6. `rounds/<current_round>/metric_snapshot.json` and `config_snapshot.json`
   - Read the actual metric packet and the observed run contract before trusting summaries.
7. `rounds/<current_round>/benchmark_summary.json` and `historical_baseline_summary.json`
   - Add efficiency context and bootstrap calibration context.
8. `rounds/<current_round>/artifact_index.json`
   - Verify nothing required is missing.
9. `rounds/<current_round>/round_summary.json`
   - Use as structured synthesis after reading the lower evidence layers.
10. `rounds/<current_round>/codex_report.md` and `gpt_input.md`
   - Use only as assistant-facing helpers after the structured evidence has been read.

## Decision Rules

- Read comparability first. If comparability is missing, failed, or insufficient, do not make a formal improvement or degradation claim.
- When evidence is incomplete, say it is incomplete. Insufficient evidence is a valid conclusion.
- Do not use `synthetic_rehearsal` semantics to replace `formal_train` judgement.
- Do not treat a workstation path such as `local_execution_repo_path` as the only truth-repo expression. Use `source_of_truth_repo` for exchange-facing identity.
- Do not use `round_summary.json` alone for a formal conclusion.
- Do not let `codex_report.md` or `gpt_input.md` override the structured JSON artifact layer.

## Clean Waiting State

If `CURRENT_ROUND.json.exchange_state = awaiting_new_round_publish`:

- There is no active round bundle under `rounds/` to judge.
- Read only the long-term docs layer.
- Do not fabricate round-level metrics, comparability, or verdicts.
- The correct outcome is docs-only context plus an explicit statement that the current round evidence is absent.

## Minimal Judgement Principle

Use the strongest justified claim, not the strongest imaginable claim.

- If only long-term docs exist, produce no round verdict.
- If a round exists but comparability is weak, keep the result provisional.
- If summary files and lower artifacts disagree, trust the lower artifacts.
