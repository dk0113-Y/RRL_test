# Project Context

`RRL_test` is the public exchange surface for a real reinforcement-learning research loop, not a rehearsal-first demo repo.

## Long-Term Goal

The project goal is to improve the real exploration agent in `../代码1` without losing comparability, stability, or wall-clock discipline. Formal conclusions must come from the real training artifacts produced by that repository.

## Source Of Truth

- Formal training source of truth: `../代码1`
- Real training entry: `train_q_agent.py`
- Real formal artifacts: `train_steps.csv`, `train_episodes.csv`, `eval_metrics.csv`, `final_probe.csv`, `best.pt`, `last.pt`, `metric_snapshot.json`, `benchmark_summary.json`, `config_snapshot.json`, `artifact_index.json`
- Best checkpoint rule: `eval_success_rate`, tie-break by `eval_mean_coverage`
- Final probe rule: probe `best.pt` if available, otherwise probe the online last checkpoint

## Exchange Repo Responsibility

This repository exists to let a fresh GPT chat inherit:

1. The current research mainline
2. The current decision boundary
3. The current stopping policy
4. The current round bundle and its comparability status

It is not allowed to redefine formal semantics from old rehearsal notes or synthetic outputs.

## Current Evidence Status

- Formal artifact generation is now wired into the real training repo.
- Historical runs under `../代码1/outputs/` were scanned and backfilled into the formal artifact shape.
- Historical threshold calibration is still bootstrap-only because old runs do not contain complete `config_snapshot.json` payloads with full train configs.
- Any strong stop or promotion claim must therefore stay gated by comparability and may still require manual review.
