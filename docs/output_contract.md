# Output Contract

GPT must emit one and only one JSON payload wrapped by the markers below.

DECISION_JSON_BEGIN
```json
{
  "schema_version": "2.0",
  "round_id": "round_xxxx",
  "experiment_mode": "formal_train",
  "source_of_truth_repo": "dk0113-Y/DRL-path-finding",
  "local_execution_repo_path": "../代码1",
  "decision_status": "pause_for_manual_review",
  "evaluation_mode": "formal_artifact_review",
  "comparability_group": "formal_mainline_v1__example",
  "baseline_round_id": "round_0022",
  "baseline_commit_sha": "abc123",
  "decision_zone": "manual_review_required",
  "stop_window_state": {
    "recommended_action": "pause_for_manual_review",
    "window_basis": "bootstrap_thresholds",
    "comparability_status": "bootstrap_comparable"
  },
  "manual_review_reasons": [
    "comparability_only_bootstrap_confirmed"
  ],
  "insufficient_evidence_flags": [
    "historical_thresholds_bootstrap_only"
  ],
  "target_program": "train_q_agent.py",
  "run_args": {
    "cli_args": ["--device", "cuda"],
    "run_name": null,
    "output_root": null,
    "notes": "Optional execution notes."
  },
  "parameter_changes": [],
  "codex_analysis_focus": {
    "compare_targets": ["best_known_reference"],
    "required_logs": [
      "logs/train_steps.csv",
      "logs/train_episodes.csv",
      "logs/eval_metrics.csv",
      "logs/final_probe.csv",
      "logs/metric_snapshot.json",
      "logs/benchmark_summary.json",
      "logs/config_snapshot.json",
      "logs/artifact_index.json"
    ],
    "required_plots": [],
    "questions": [
      "Is the next round formally comparable?",
      "What do best_eval, last_eval, and final_probe jointly imply?",
      "Should the controller run, stop, pause, or analyze only?"
    ],
    "expected_output_style": "Write a structured markdown report grounded in the formal JSON artifacts."
  },
  "reference_targets": {
    "best_known_reference": "path/to/reference/run",
    "manual_compare_targets": []
  },
  "controller_notes": "Explain the intended next-step scope."
}
```
DECISION_JSON_END

## Rules

1. `experiment_mode` must be either `formal_train` or `synthetic_rehearsal`.
2. Formal decisions must keep the real training repo identity in `source_of_truth_repo` and may use `local_execution_repo_path` only as local execution context.
3. `decision_status` must be one of `run_next_round`, `stop_experiment`, `pause_for_manual_review`, or `analyze_only`.
4. `decision_zone`, `stop_window_state`, `manual_review_reasons`, and `insufficient_evidence_flags` must align with the current round evidence.
5. If comparability failed or evidence is insufficient, do not output a payload that implies a formal improvement claim.
6. Do not emit any prose outside the markers and do not insert comments into the JSON.
