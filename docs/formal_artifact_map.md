# Formal Artifact Map

Use `docs/gpt_index_guide.md` for the evidence-layer reading order; this file only defines artifact roles.

This map describes the formal artifact contract emitted by the real training repo.

## Required CSV

- `logs/train_steps.csv`
  - Rolling train-side optimization and recent performance summaries
- `logs/train_episodes.csv`
  - Episode-level training records
  - Current formal runs may include `train_episode_idx`, `phase_episode_idx`, `episode_seed`, and `map_fingerprint`
- `logs/eval_metrics.csv`
  - Periodic evaluation records and final eval
- `logs/final_probe.csv`
  - End-of-run probe metrics

## Required Checkpoints

- `checkpoints/best.pt`
  - Selected by `eval_success_rate`, tie-break by `eval_mean_coverage`
- `checkpoints/last.pt`
  - Last online checkpoint at run end

## Required Structured JSON

- `logs/metric_snapshot.json`
  - Unified recent train, last eval, best eval, and final probe snapshot
  - May also expose best/last checkpoint train-episode indices when the source run emitted them
- `logs/benchmark_summary.json`
  - Runtime and timing summary when available, with explicit insufficiency flags when not
  - May include `budget_mode`, `train_episodes_to_best`, and `total_train_episodes_completed`
- `logs/config_snapshot.json`
  - Full config for new formal runs, or partial bootstrap metadata for old runs
  - Full config may include `budget_mode`, `total_train_episodes`, `warmup_episodes`, `eval_interval_episodes`, `log_interval_episodes`, `train_print_interval_episodes`
  - Full config may include `use_fixed_train_episode_seeds` and `fixed_train_episode_seed_base`
  - Includes `observed_run_contract.final_env_steps`
  - Includes `observed_run_contract.final_train_episode_idx`
  - Includes `observed_run_contract.train_episodes_header`
  - Includes `observed_run_contract.train_steps_header`
  - Includes `observed_run_contract.eval_metrics_header`
  - Includes `observed_run_contract.final_probe_header`
- `logs/artifact_index.json`
  - Existence map for required and optional artifacts
- `historical_baseline_summary.json`
  - Published in the exchange round bundle when available
  - Explains bootstrap historical thresholds and whether calibration is still insufficient

## Optional Artifacts

- `plots/`
  - Useful but not required for formal judgement
- `trajectories/`
  - Optional and switch-controlled
- `logs/training_summary.txt`
  - Human-readable support summary

## Exchange-Facing Identity

- `source_of_truth_repo`
  - Public repo identity such as `dk0113-Y/DRL-path-finding`
- `local_execution_repo_path`
  - Local controller execution path
  - Published separately so exchange JSON does not rely on a workstation-specific absolute path as the sole truth identifier

## Exchange Pointer Metadata

- `CURRENT_ROUND.json.exchange_anchor_commit_sha`
  - The published bundle anchor commit for the active round
  - This points to the commit that first introduced the round bundle into the exchange repo
- `CURRENT_ROUND.json.last_exchange_commit_sha`
  - Deprecated compatibility alias for the same anchor commit
  - It is not defined as the final HEAD commit that also edits `CURRENT_ROUND.json`
- `CURRENT_ROUND.json.exchange_state`
  - `awaiting_new_round_publish` means the exchange repo has been intentionally cleared and no active round bundle should be read yet
