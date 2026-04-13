# Formal Artifact Map

This map describes the formal artifact contract emitted by the real training repo.

## Required CSV

- `logs/train_steps.csv`
  - Rolling train-side optimization and recent performance summaries
- `logs/train_episodes.csv`
  - Episode-level training records
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
- `logs/benchmark_summary.json`
  - Runtime and timing summary when available, with explicit insufficiency flags when not
- `logs/config_snapshot.json`
  - Full config for new formal runs, or partial bootstrap metadata for old runs
- `logs/artifact_index.json`
  - Existence map for required and optional artifacts

## Optional Artifacts

- `plots/`
  - Useful but not required for formal judgement
- `trajectories/`
  - Optional and switch-controlled
- `logs/training_summary.txt`
  - Human-readable support summary
