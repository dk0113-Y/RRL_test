# GPT Input Package

## 1. Current Round Basics
- Round id: `round_0021`
- Experiment mode: `formal_train`
- Source of truth repo: `dk0113-Y/DRL-path-finding`
- Local execution repo path: `C:\Users\Dk\Desktop\SCI\代码1`
- Round state status: `success`
- Run directory: `outputs/4.9_30万轮基线`
- Target program: `train_q_agent.py`

## 2. Comparability
- Comparability status: `insufficient_evidence`
- Comparability group: `formal_mainline_v1__bf21a9e8fbc5`
- Baseline round id: `UNSET`
- Baseline commit sha: `UNSET`
- Checks: `{'baseline_available': False, 'same_comparability_group': False, 'same_train_steps_header': False, 'same_eval_metrics_header': False, 'same_final_probe_header': False, 'same_final_env_steps': False, 'target_has_full_config_snapshot': False, 'baseline_has_full_config_snapshot': False}`
- Historical calibration: `available=True, insufficient_history_for_calibration=True`

## 3. Metric Verdict Layer
- Primary verdict: `insufficient_evidence`
- Secondary verdict: `insufficient_evidence`
- Stability verdict: `insufficient_evidence`
- Efficiency verdict: `insufficient_evidence`
- Overall verdict: `insufficient_evidence`

## 4. Best Eval / Last Eval / Final Probe
- best_eval: `{'reward': 37.00354766845703, 'coverage': 0.8192999958992004, 'success_rate': 0.25, 'episode_length': 568.75, 'repeat_visit_ratio': 0.5247381329536438}`
- last_eval: `{'reward': 3.671288251876831, 'coverage': 0.6947166919708252, 'success_rate': 0.1666666716337204, 'episode_length': 559.75, 'repeat_visit_ratio': 0.6131370067596436}`
- final_probe: `{'reward': 86.50942993164062, 'coverage': 0.8751125335693359, 'success_rate': 0.625, 'episode_length': 454.9375, 'repeat_visit_ratio': 0.38662803173065186}`
- benchmark_summary: `runtime=None, env_steps_to_best=264000`

## 5. Stop Window
- decision_zone: `insufficient_evidence`
- recommended_action: `pause_for_manual_review`
- plateau_detected: `False`
- manual_review_required: `True`
- reasons: comparability_inputs_incomplete, historical_thresholds_are_bootstrap_only, runtime_summary_missing, backfilled_from_historical_run, train_config_unavailable_in_backfill_context, timing_summary_unavailable, total_runtime_unavailable, complete_train_config_not_recoverable_without_checkpoint_loader, train_config_unavailable, historical_thresholds_bootstrap_only

## 6. Manual Review / Evidence Gaps
- manual_review_reasons: runtime_summary_missing
- insufficient_evidence_flags: backfilled_from_historical_run, train_config_unavailable_in_backfill_context, timing_summary_unavailable, total_runtime_unavailable, complete_train_config_not_recoverable_without_checkpoint_loader, train_config_unavailable, historical_thresholds_bootstrap_only
- historical_baseline_summary: `path=historical_baseline_summary.json, run_count_total=19, insufficient_history_for_calibration=True`

## 7. What GPT Should Output
- Read `docs/reading_order.md`, `docs/current_mainline.md`, `docs/evaluation_charter.md`, and `docs/output_contract.md` before drafting the next decision.
- Any claim of improvement must remain subordinate to comparability. If comparability failed or evidence is insufficient, do not accumulate a positive formal conclusion.
- Output a single `next_gpt_decision.json` payload aligned with `docs/output_contract.md` and the dual-mode protocol schema.
