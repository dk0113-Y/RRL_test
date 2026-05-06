# Formal Round Evidence Report

## Evidence Scope

- Task type: formal round evidence report only.
- Skill used: `formal-round-evidence-report`.
- Destination mode: `exchange_published_report`.
- Run ID: `value_bcchild_gated_statequery_effopt_formal_500k_decay320k_end005_minreplay12000_confirm_20260506_114441_20260506_114445`.
- Published report directory: `reports/formal_evidence/value_bcchild_gated_statequery_effopt_formal_500k_decay320k_end005_minreplay12000_confirm_20260506_114441_20260506_114445/`.
- Decision authority: GPT. This Codex report is factual evidence only.
- Tuning recommendation provided: `false`.

## Active Round / Run Identity

- Active exchange round: not applicable for this task. The prompt requested a run-scoped published report and explicitly prohibited updating `CURRENT_ROUND.json`.
- Run identity source: source run directory basename.
- Experiment mode from structured artifacts: `formal_train`.
- Formal protocol revision: `formal_posthoc_trainselect_v1`.
- Budget mode: `env_steps`.
- Total env steps: `500000`.
- Training source commit recorded in run config: `43c4cd7da0ef61f08ee2a66a58324f73df4a8fa3` on branch `main`.
- Run mode recorded in structured artifacts: `cli`.

## Files Inspected

Structured artifacts inspected:

- `logs/metric_snapshot.json`
- `logs/benchmark_summary.json`
- `logs/config_snapshot.json`
- `logs/artifact_index.json`
- `logs/posthoc_selection_summary.json`
- `logs/final_probe_summary.json`
- `logs/best_vs_last_gap_summary.json`
- `logs/formal_selection_manifest.json`

CSV artifacts inspected for concise aggregate facts only:

- `logs/train_steps.csv`
- `logs/train_episodes.csv`
- `logs/posthoc_candidate_scores.csv`
- `logs/final_probe.csv`

Text summary inspected for concise facts only:

- `logs/training_summary.txt`

Checkpoint files inspected as metadata/presence only:

- `checkpoints/best.pt`
- `checkpoints/last.pt`
- `checkpoints/ckpt_step_460000.pt`
- `checkpoints/ckpt_step_480000.pt`
- `checkpoints/ckpt_step_500000.pt`

## Artifact Completeness Checklist

| Artifact | Status | Notes |
| --- | --- | --- |
| `logs/metric_snapshot.json` | present, parseable | Structured metric snapshot. |
| `logs/benchmark_summary.json` | present, parseable | Runtime summary available. |
| `logs/config_snapshot.json` | present, parseable | Includes full train config, comparability metadata, observed run contract, and evaluation contract. |
| `logs/artifact_index.json` | present, parseable | Source-side artifact index available. |
| `logs/posthoc_selection_summary.json` | present, parseable | Posthoc train-side candidate selection summary available. |
| `logs/final_probe_summary.json` | present, parseable | Held-out final probe summary available. |
| `logs/best_vs_last_gap_summary.json` | present, parseable | Winner-vs-last diagnostic summary available. |
| `logs/formal_selection_manifest.json` | present, parseable | Formal selection manifest available. |
| `logs/train_steps.csv` | present, parseable | 992 rows; aggregate facts only extracted. |
| `logs/train_episodes.csv` | present, parseable | 957 rows; aggregate facts only extracted. |
| `logs/posthoc_candidate_scores.csv` | present, parseable | 16 candidate rows; selected top-3 facts extracted. |
| `logs/final_probe.csv` | present, parseable | 3 final-probe rows extracted. |
| `checkpoints/best.pt` | present, metadata only | Checkpoint not copied. |
| `checkpoints/last.pt` | present, metadata only | Checkpoint not copied. |
| `logs/model_select_eval.csv` | missing | Marked optional legacy model-selection CSV in artifact index. |
| `logs/best_recheck_eval.csv` | missing | Marked optional legacy model-selection CSV in artifact index. |
| `logs/eval_metrics.csv` | missing | Marked optional legacy diagnostic CSV in artifact index. |
| plots | not present | Artifact index lists no plots. |
| trajectories | not present | Artifact index lists no trajectories; config disables trajectory saving. |

## Formal Configuration and Protocol Facts

- Rows x columns: `40 x 60`.
- Observation size: `6`.
- Scan radius: `10`.
- Obstacle ratio: `0.2`.
- Total env-step budget: `500000`.
- Warmup steps: `4000`.
- Collect steps per iteration: `16`.
- Learner updates per iteration: `2`.
- Replay capacity: `100000`.
- Batch size: `128`.
- Minimum replay size: `12000`.
- Gamma: `0.99`.
- N-step: `3`.
- Learning rate: `0.0001`.
- Target update interval: `1000`.
- Epsilon schedule: start `1.0`, end `0.05`, decay steps `320000`.
- Final greedy episodes: `100`.
- Fixed train seeds: `true`, base `20259323`.
- Fixed final-probe seeds: `true`, base `20261323`.
- Periodic checkpoint interval: `20000` env steps.
- Posthoc candidate start: `200000` env steps.
- Posthoc final-probe top-k: `3`.
- Best checkpoint selection during training: `false`.

Comparability metadata from `config_snapshot.json`:

- Comparability group: `formal_posthoc_trainselect_v1__a04764801e18`.
- Allowed tuning fields recorded: `reward_turn_penalty_scale`, `reward_turn_weight_45`, `reward_turn_weight_90`, `reward_turn_weight_135`, `reward_turn_weight_180`, `reward_revisit_penalty`.
- Manual review field recorded: `max_entries_per_block`.
- Observed run contract available: `true`.
- Evaluation contract available: `true`.

## Checkpoint-Selection Evidence

Formal selection manifest:

- Protocol: `formal_posthoc_trainselect_v1`.
- Candidate range: `200000` to `500000` env steps.
- Checkpoint interval: `20000` env steps.
- Selected candidate steps: `480000`, `460000`, `500000`.
- Final probe episode count: `100`.
- Final probe seed base: `20261323`.
- Winner step: `460000`.
- Winner checkpoint: `checkpoints/ckpt_step_460000.pt`.
- Formal primary checkpoint path: `checkpoints/best.pt`.
- Diagnostic endpoint checkpoint path: `checkpoints/last.pt`.

Posthoc train-side selected candidates from `posthoc_candidate_scores.csv`:

| Selection rank | Candidate step | Window | Reward | Coverage | Success rate | Episode length | Repeat visit ratio | Selection score |
| --- | ---: | --- | ---: | ---: | ---: | ---: | ---: | ---: |
| 1 | 480000 | 440000-480000 | 135.637171 | 0.927109 | 0.650741 | 440.203457 | 0.223375 | 1.097158 |
| 2 | 460000 | 420000-460000 | 133.549723 | 0.921892 | 0.637901 | 445.906420 | 0.240080 | 0.981857 |
| 3 | 500000 | 460000-500000 | 131.152732 | 0.917562 | 0.590741 | 448.000124 | 0.245420 | 0.858645 |

## Final Probe Evidence

Final probe rows from `final_probe.csv` and `final_probe_summary.json`:

| Final probe rank | Candidate step | Source | Formal winner | Eval episodes | Reward | Coverage | Success rate | Episode length | Repeat visit ratio |
| --- | ---: | --- | --- | ---: | ---: | ---: | ---: | ---: | ---: |
| 1 | 460000 | `posthoc_final_winner` | true | 100 | 123.947060 | 0.891101 | 0.640000 | 427.450012 | 0.253678 |
| 2 | 500000 | `posthoc_candidate_final_probe` | false | 100 | 113.017677 | 0.856415 | 0.610000 | 442.549988 | 0.326926 |
| 3 | 480000 | `posthoc_candidate_final_probe` | false | 100 | 122.082718 | 0.886499 | 0.500000 | 448.890015 | 0.292244 |

The final-probe summary marks `460000` as the formal winner and maps the formal object to `checkpoints/best.pt`. This is reported as selection evidence only, not as a tuning decision.

## Training Dynamics Summary

Concise aggregate facts from `metric_snapshot.json`, `train_steps.csv`, and `training_summary.txt`:

- `train_steps.csv` rows: `992`.
- `train_episodes.csv` rows: `957`.
- Final logged train step: `500000`.
- Completed train episodes at final logged train step: `951`.
- Learner steps at final logged train step: `61002`.
- Replay size at final logged train step: `100000`.
- Epsilon at final logged train step: `0.05`.
- Recent train metrics at `500000` env steps:
  - Reward: `125.423492`
  - Coverage: `0.899773`
  - Success rate: `0.570000`
  - Episode length: `450.730011`
  - Repeat visit ratio: `0.288113`
- Training dynamics final-window stats match the recent train metrics above.
- Growth rates over the late-stage window:
  - Reward: `0.123885`
  - Coverage: `0.000292`
  - Success rate: `0.000552`
  - Repeat visit ratio: `-0.000578`
- Threshold reach steps:
  - Success rate `0.50`: `313008`
  - Coverage `0.90`: `271008`
  - Custom reward threshold: missing / null.
- Late-stage variance:
  - Reward: `50.651858`
  - Coverage: `0.000270`
  - Success rate: `0.003753`
  - Repeat visit ratio: `0.001179`
- Train-final consistency verdict recorded by artifact: `supports_final_probe`.
- Train-final consistency counts: `aligned=5`, `final_probe_stronger=0`, `final_probe_weaker=0`, `insufficient_evidence=0`.

## Best-vs-Last Diagnostic Evidence

From `best_vs_last_gap_summary.json`:

- Role: `diagnostic_best_vs_last`.
- Comparison mode: `heldout_winner_vs_heldout_last_checkpoint`.
- Best source: `logs/final_probe.csv::formal_winner`.
- Last source: `final_probe.csv::last_checkpoint_candidate`.
- Last checkpoint path: `checkpoints/last.pt`.
- Last env steps: `500000`.

| Metric | Best | Last | Best minus last |
| --- | ---: | ---: | ---: |
| Reward | 123.947060 | 113.017677 | 10.929382 |
| Coverage | 0.891101 | 0.856415 | 0.034686 |
| Success rate | 0.640000 | 0.610000 | 0.030000 |
| Episode length | 427.450012 | 442.549988 | -15.099976 |
| Repeat visit ratio | 0.253678 | 0.326926 | -0.073247 |

These are diagnostic facts only. They are not a method-performance superiority claim.

## Runtime / Benchmark Facts

From `benchmark_summary.json`:

- Total runtime: `14375.981678` seconds.
- Total runtime HMS: `03:59:36`.
- Total train episodes completed: `951`.
- Best checkpoint env steps: `460000`.
- Last checkpoint env steps: `500000`.
- Model-selection eval count: `0`.
- Recheck eval count: `0`.
- Insufficient evidence flags: none.
- Runtime switches recorded:
  - AMP: `false`.
  - Inference AMP: `false`.
  - Torch compile: `false`.
  - cuDNN benchmark: `true`.
  - TF32: `true`.
  - Strict reproducibility: `false`.
  - Plot generation: `false`.
  - Train/final-probe trajectory saving: `false`.
- Detailed collector/learner/replay timing summaries: missing / null because timing switches were disabled.

Runtime facts are reported only as runtime evidence and are not treated as method-performance evidence.

## Exchange Publication Facts

- Exchange repository target identity: `RRL_test`.
- Report destination: `reports/formal_evidence/value_bcchild_gated_statequery_effopt_formal_500k_decay320k_end005_minreplay12000_confirm_20260506_114441_20260506_114445/`.
- Authorized files for publication:
  - `reports/formal_evidence/value_bcchild_gated_statequery_effopt_formal_500k_decay320k_end005_minreplay12000_confirm_20260506_114441_20260506_114445/codex_evidence_report.md`
  - `reports/formal_evidence/value_bcchild_gated_statequery_effopt_formal_500k_decay320k_end005_minreplay12000_confirm_20260506_114441_20260506_114445/codex_execution_report.json`
- `CURRENT_ROUND.json` modified: `false`.
- Checkpoints copied: `false`.
- Model weights copied: `false`.
- Full CSV logs copied: `false`.
- Full raw training logs copied: `false`.

## Missing Evidence

- Required source-side structured artifacts from the skill checklist: none missing.
- Required CSV artifacts from the skill checklist: none missing.
- Required checkpoint metadata targets: present.
- Optional legacy artifacts missing:
  - `logs/model_select_eval.csv`
  - `logs/best_recheck_eval.csv`
  - `logs/eval_metrics.csv`
- Detailed timing summaries are null because timing collection switches were disabled.
- Control-plane bundle artifacts such as an exchange `comparability_report.json`, `config_diff.json`, or `artifact_digest.json` were not provided as inputs and were not used as source-run evidence for this run-scoped report.
- Active round pointer consistency was not evaluated because the task explicitly requested a run-scoped report and prohibited `CURRENT_ROUND.json` modification.

## Forbidden Artifact Check

- No checkpoint binaries were copied.
- No model-weight contents were copied.
- No full CSV rows were copied into this report.
- No full raw training logs were copied into this report.
- No private local absolute paths are intentionally included.
- No usernames, environment secrets, or sensitive command history are included.
- No tuning recommendation, next hyperparameter, stop/continue decision, branch decision, or method-performance superiority claim is provided.

## Unverified Items and Limitations

- This report summarizes run-local evidence only and does not compare against stale historical rounds.
- This report does not inspect or validate a full control-plane exchange bundle for this run because the prompt provided a source run directory and a run-scoped report destination, not a round bundle path.
- This report does not verify remote GitHub rendering after push; it verifies local written files and git push status.
- The execution report cannot embed the final commit hash of the commit that contains itself without a self-reference paradox; the pushed commit hash is reported in the final Codex response.
- GPT remains responsible for evidence interpretation and any tuning decision.

## Evidence Status

`sufficient`

The run-local formal evidence required for a factual single-run evidence report is present and parseable. This status is not a GPT acceptance decision and is not a tuning recommendation.
