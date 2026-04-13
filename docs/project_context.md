# Project Context

`RRL_test` is the public exchange surface for a real reinforcement-learning research loop, not a rehearsal-first demo repo.

## Long-Term Goal

The project goal is to improve the real exploration agent in `dk0113-Y/DRL-path-finding` without losing comparability, stability, or wall-clock discipline. Formal conclusions must come from the real training artifacts produced by that repository.

## Source Of Truth

- Formal training source of truth repo identity: `dk0113-Y/DRL-path-finding`
- Local execution repo path is tracked separately as `local_execution_repo_path`
- Real training entry: `train_q_agent.py`
- Real formal artifacts: `train_steps.csv`, `train_episodes.csv`, `eval_metrics.csv`, `final_probe.csv`, `best.pt`, `last.pt`, `metric_snapshot.json`, `benchmark_summary.json`, `config_snapshot.json`, `artifact_index.json`
- Historical calibration evidence artifact: `historical_baseline_summary.json`
- Best checkpoint rule: `eval_success_rate`, tie-break by `eval_mean_coverage`
- Final probe rule: probe `best.pt` if available, otherwise probe the online last checkpoint

## Exchange Repo Responsibility

This repository exists to let a fresh GPT chat inherit:

1. The current research mainline
2. The current decision boundary
3. The current stopping policy
4. The current round bundle and its comparability status

It is not allowed to redefine formal semantics from old rehearsal notes or synthetic outputs.

## Exchange Anchor Semantics

- `CURRENT_ROUND.json.exchange_anchor_commit_sha`
  - means the published bundle anchor commit
  - this is the commit that first contains the published round bundle
  - it is intentionally not defined as the self-referential final HEAD commit that also edits `CURRENT_ROUND.json`
- `CURRENT_ROUND.json.last_exchange_commit_sha`
  - deprecated compatibility alias
  - when a round is published, it mirrors `exchange_anchor_commit_sha`

## Current Evidence Status

- Formal artifact generation is now wired into the real training repo.
- Historical runs under the real training repo `outputs/` tree were scanned and backfilled into the formal artifact shape.
- Historical bundle evidence can be republished when needed, including `historical_baseline_summary.json`.
- The exchange repository may also be in a clean waiting state with no active round bundle, to avoid stale round pollution.
- Historical threshold calibration is still bootstrap-only because old runs do not contain complete `config_snapshot.json` payloads with full train configs.
- Any strong stop or promotion claim must therefore stay gated by comparability and may still require manual review.
