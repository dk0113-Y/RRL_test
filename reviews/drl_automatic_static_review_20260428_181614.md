# DRL_automatic Static Review

## Scope

This review is a static protocol-alignment review only.

It does not:
- launch training
- modify `DRL-path-finding`
- modify the automation control repository
- create a real `round_0002`
- publish checkpoint binaries

The review target is the local repository resolved as the practical automation-control candidate:
- `C:\Users\Dk\Desktop\SCI\codex_ui_bridge_demo`

The exact path `C:\Users\Dk\Desktop\SCI\DRL_automatic` was not present locally.

## Repository Resolution

Resolution result:
- exact requested path `C:\Users\Dk\Desktop\SCI\DRL_automatic`: missing
- local candidate reviewed: `C:\Users\Dk\Desktop\SCI\codex_ui_bridge_demo`

Why this candidate was reviewed:
- it is a git repository
- it contains controller/orchestrator files such as `scheduler.py`, `prepare_round.py`, `build_exchange_bundle.py`, `publish_round_to_exchange.py`, `ingest_exchange_decision.py`, and `comparability.py`
- its own `README.md` explicitly describes the repository as a local control-plane repository that publishes to `../RRL_test`

Review caution:
- the repository name does not match `DRL_automatic`
- the review conclusions therefore apply to the local control candidate that appears to implement the automation role

## Protocol Baseline

This review treats the numbered protocol set in `RRL_test` as authoritative:
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
- `docs/10_output_contract.md`

Active baseline context:
- `round_0001`
- `baseline_registration`
- `engineering_speed_baseline_candidate`
- `decision_required_from_gpt=false`

## Entry Points

Observed entry points and orchestrators:
- `scheduler.py::main`
  - CLI entry for launching `fake_train.py` or `train_q_agent.py`
  - accepts `--decision-file`
- `prepare_round.py::main`
  - creates local round directory, placeholder files, and `round_state.json`
- `build_exchange_bundle.py::main`
  - builds a formal bundle from an existing run directory
- `publish_round_to_exchange.py::main`
  - publishes a local round bundle into `../RRL_test`
- `ingest_exchange_decision.py::main`
  - ingests GPT-produced decision JSON back into local automation rounds
- `run_rehearsal_loop.py::main`
  - synthetic loop controller for rehearsal/demo flow

Main architecture finding:
- the repository has a real controller chain
- the chain is still built around the legacy formal/rehearsal protocol family, not the new numbered `RRL_test` protocol

## Capability Matrix

| Capability | Status | Evidence | Risk | Required Fix | Suggested Next Action |
|---|---|---|---|---|---|
| A. Repository discovery and path configuration | partial | `scheduler.py::execution_repo_root`, `run_rehearsal_loop.py` default `../RRL_test`, `README.md` points to `../RRL_test` and `../代码1` | medium | yes | code patch needed |
| B. Current round pointer reading | partial | `ingest_exchange_decision.py:68-75` reads `CURRENT_ROUND.json`, but only to extract anchor SHA; `publish_round_to_exchange.py` writes old pointer fields such as `current_round_manifest` | high | yes | code patch needed |
| C. Round id generation | present | `automation_protocol.py::next_round_id`, `prepare_round.py:36` auto-selects next round id | low | no immediate fix for ID generation itself | no change |
| D. Round type and mode handling | partial | `automation_protocol.py::load_decision_file` supports `experiment_mode` only; no new `round_type` handling for `baseline_registration`, `dry_run_no_train`, `failed_or_rejected` | high | yes | code patch needed |
| E. Dry-run / no-train support | missing | no explicit `dry_run_no_train` mode found; available modes are legacy `synthetic_rehearsal` and `formal_train`; `run_rehearsal_loop.py` is rehearsal, not protocol-aligned dry-run | high | yes | code patch needed |
| F. Config proposal generation | partial | `prepare_round.py` creates template decision; `run_rehearsal_loop.py` injects hardcoded synthetic parameters; no numbered-protocol formal proposal generator found | high | yes | code patch needed |
| G. Config diff generation | missing | no `config_diff.json` generation path found in controller scripts; `build_exchange_bundle.py` uses old `config_snapshot.json` instead | high | yes | code patch needed |
| H. Frozen-field validation | missing | no code path found that validates frozen semantic fields before `formal_train`; `scheduler.py::resolve_context` only checks decision status | high | yes | code patch needed |
| I. Allowed tuning field validation | missing | no allow-list validation layer found before launch; no `allowed_tuning` policy enforcement in controller code | high | yes | code patch needed |
| J. Manual-review field detection | partial | `formal_round_summary.py` and `comparability.py` can emit manual-review-style reasons from evidence, but not from config field taxonomy aligned to `docs/07` | medium | yes | code patch needed |
| K. Comparability group assignment | partial | `comparability.py::get_comparability_group` and `build_comparability_report` exist, but are driven by legacy `config_snapshot` / header checks | medium | yes | code patch needed |
| L. Preflight validation | missing | no dedicated preflight stage enforcing mode, frozen fields, allowed fields, runtime-toggle policy, and publication policy before launch | high | yes | code patch needed |
| M. Training invocation guard | partial | `scheduler.py::resolve_context` blocks unless legacy `decision_status == run_next_round`, but direct CLI mode still launches, and no new-protocol preflight is required | high | yes | code patch needed |
| N. Artifact discovery | present | `scheduler.py::validate_run_artifacts` checks expected subdirs/logs/checkpoints; `build_exchange_bundle.py::required_run_artifact_paths` discovers formal artifact files | medium | yes, because artifacts are legacy set | code patch needed |
| O. Artifact digest generation | missing | no `artifact_digest.json` generation path found; legacy flow expects `artifact_index.json` from source run | high | yes | code patch needed |
| P. CSV summary generation | missing | no `csv_summaries.json` generation logic found | medium | yes | code patch needed |
| Q. Checkpoint metadata/hash registration | missing | no checkpoint metadata-only/hash registration path found for exchange bundle; `build_exchange_bundle.py` does not produce metadata-only checkpoint records | high | yes | code patch needed |
| R. Checkpoint binary exclusion | partial | current bundle path copies `metric_snapshot/benchmark/config_snapshot/artifact_index` only, so checkpoints are not copied by that path; however no new-protocol metadata-only guard or explicit exclusion contract is enforced | medium | yes | code patch needed |
| S. Round bundle generation | partial | `build_exchange_bundle.py` and `publish_round_to_exchange.py` generate bundles, but bundle schema is legacy: `metric_snapshot.json`, `benchmark_summary.json`, `artifact_index.json`, `historical_baseline_summary.json` | high | yes | code patch needed |
| T. CURRENT_ROUND.json update behavior | partial | `publish_round_to_exchange.py:216-243` writes old fields like `current_round_manifest`, `exchange_state`, `expected_output_file`; does not align to new `current_round_path/current_phase/status` contract | high | yes | code patch needed |
| U. GPT decision consumption | partial | `ingest_exchange_decision.py` and `scheduler.py` consume decision files, but schema is old and wired to `next_gpt_decision.json` handshake | high | yes | code patch needed |
| V. Placeholder decision handling | missing | no `gpt_decision_placeholder.json` contract support found; placeholders exist only for `codex_request.md` and `gpt_input.md` text stubs in `prepare_round.py` / `automation_protocol.py` | high | yes | code patch needed |
| W. Controller action handling | missing | no `controller_action.json` support found | medium | yes | code patch needed |
| X. Stale outbox / legacy decision avoidance | missing | active use of `outbox` in `exchange_protocol.py`, `exchange_web_bridge.py`, `publish_round_to_exchange.py`; active `next_gpt_decision.json` flow in `automation_protocol.py`, `extract_gpt_decision.py`, `ingest_exchange_decision.py` | high | yes | code patch needed |
| Y. Failed/incomplete run handling | partial | `scheduler.py` records statuses like `run_dir_not_detected` and validates artifacts; `formal_round_summary.py` can emit insufficient-evidence/manual-review states | medium | yes | code patch needed |
| Z. Rollback / previous round preservation | partial | `automation_protocol.py::ingest_decision_payload` and `publish_round_to_exchange.py` both support `force` overwrite / directory removal; no robust no-overwrite preservation policy found | high | yes | code patch needed |

## Legacy Dependency Scan

Legacy dependency findings are explicit and material.

Found `outbox` dependency:
- `exchange_protocol.py:114-125`
  - initializes `outbox/`
- `exchange_web_bridge.py:111`
  - reads `outbox/web_index_message_<round>.md`
- `publish_round_to_exchange.py:245-263`
  - writes outbox index message into exchange repo

Found `next_gpt_decision.json` dependency:
- `automation_protocol.py:35`
  - `NEXT_DECISION_FILENAME = "next_gpt_decision.json"`
- `extract_gpt_decision.py`
  - writes `next_gpt_decision.json`
- `ingest_exchange_decision.py:52-64, 110-112`
  - reads and syncs `rounds/<source_round>/next_gpt_decision.json`
- `publish_round_to_exchange.py:209, 233`
  - still declares `expected_output_file = "next_gpt_decision.json"`

Found old docs-name dependency:
- `exchange_protocol.py:25-60`
  - hardcodes `docs/gpt_index_guide.md`, `docs/reading_order.md`, `docs/project_context.md`, `docs/current_mainline.md`, `docs/evaluation_charter.md`, `docs/stopping_policy.md`, `docs/output_contract.md`
- `docs/codex_local_index.md`
  - references deleted `RRL_test` docs such as `docs/gpt_index_guide.md`, `docs/reading_order.md`, `docs/output_contract.md`
- local `README.md`
  - still documents old exchange protocol semantics, outbox, and old artifact set

Found old artifact dependency:
- `build_exchange_bundle.py:58-70`
  - requires `metric_snapshot.json`, `benchmark_summary.json`, `config_snapshot.json`, `artifact_index.json`
- `comparability.py`
  - compares `eval_metrics_header`, `train_steps_header`, `final_probe_header` in `observed_run_contract`
- `formal_round_summary.py`
  - formal verdict synthesis is centered on legacy `metric_snapshot` and `benchmark_summary`

Eval-centric / legacy formal logic finding:
- the repo is no longer purely eval-success-rate centric, but it still assumes the legacy formal artifact family rather than the new `artifact_digest/config_diff/csv_summaries` exchange schema
- `eval_metrics` still appears in comparability/header logic, local docs, and historical automation round artifacts

## Dry-run Readiness

Conclusion:
- `not_ready`

Why:
- no explicit numbered-protocol `dry_run_no_train` mode
- active dependency on `outbox` and `next_gpt_decision.json`
- active dependency on deleted old `RRL_test` docs
- active exchange schema still built around `metric_snapshot.json`, `benchmark_summary.json`, `artifact_index.json`, and old `CURRENT_ROUND.json` fields
- no visible frozen-field / allowed-field / manual-review-field preflight enforcement before `formal_train`
- no new-protocol `artifact_digest.json`, `config_diff.json`, `gpt_decision_placeholder.json`, or `controller_action.json` path

The repository is structurally useful as a starting control plane, but it is not yet safe to validate against the current `RRL_test` numbered protocol in dry-run/no-train mode.

## Required Patches Before Dry-run

### P0

1. Remove exchange `outbox` as active control dependency and migrate all decision-routing to round-scoped files under `rounds/<round_id>/`.
2. Replace `next_gpt_decision.json` pipeline with the new `gpt_decision.json` and `gpt_decision_placeholder.json` contract.
3. Replace hardcoded references to deleted old docs with the numbered `00` to `10` protocol set.
4. Add explicit `dry_run_no_train` mode with no training launch and no rehearsal-evidence pollution.
5. Add preflight enforcement for frozen fields, allowed tuning fields, manual-review fields, comparability group, runtime-toggle policy, and publication policy.
6. Align `CURRENT_ROUND.json` read/write logic to new fields such as `current_round_path`, `current_phase`, `status`, and `decision_required_from_gpt`.
7. Replace legacy bundle schema with new round bundle schema: `index_manifest.json`, `round_summary.json`, `artifact_digest.json`, `config_diff.json`, `comparability_report.json`, plus placeholders/decision files.
8. Prevent overwrite-style publication and ingestion paths from silently replacing prior evidence during normal operation.

### P1

1. Implement metadata-only checkpoint registration with size/hash/path and explicit exclusion from exchange copy.
2. Implement `csv_summaries.json` generation for copied lightweight logs.
3. Add `controller_action.json` support derived from valid active-round decisions only.
4. Add `gpt_decision_placeholder.json` semantics for `decision_required_from_gpt=false`, especially baseline-registration rounds.
5. Update local operator docs (`README.md`, `docs/codex_local_index.md`) to match numbered protocol authority and new exchange bundle structure.

### P2

1. Improve repository discovery so `DRL_automatic`/control repo and exchange repo paths are explicit and less hardcoded.
2. Add stronger stale-decision protections against backup directories and historical local round artifacts.
3. Add clearer rollback/no-overwrite policy encoding in controller code and round publication flow.

## Recommended Next Codex Task

Recommended next task:
- generate a bounded code-patch task for the automation control repository before any dry-run/no-train validation

Suggested scope for that task:
- add explicit `dry_run_no_train` mode
- replace `outbox` and `next_gpt_decision.json` dependencies
- align `CURRENT_ROUND.json` handling to new numbered protocol
- generate `config_diff.json`, `artifact_digest.json`, `gpt_decision_placeholder.json`, and optionally `controller_action.json`
- implement preflight guards for frozen fields, allowed fields, manual-review fields, comparability, and checkpoint exclusion

This should be done before attempting any real controller dry-run against `RRL_test`.
