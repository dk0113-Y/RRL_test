# GPT Input Package

## 1. Current Round Basics
- Round id: `round_0022`
- Experiment mode: `formal_train`
- Source of truth repo: `C:\Users\Dk\Desktop\SCI\代码1`
- Round state status: `success`
- Run directory: `C:\Users\Dk\Desktop\SCI\代码1\outputs\sched_turn005_revisit010_entry8_20260410_234142`
- Target program: `train_q_agent.py`

## 2. Comparability
- Comparability status: `not_comparable`
- Comparability group: `formal_mainline_v1__bf21a9e8fbc5`
- Baseline round id: `round_0021`
- Baseline commit sha: `832dee8042424a2009fe2fff2f5137b6efbc15c5`
- Checks: `{'baseline_available': True, 'same_comparability_group': True, 'same_eval_metrics_header': False, 'same_final_probe_header': False, 'same_final_env_steps': True, 'target_has_full_config_snapshot': False, 'baseline_has_full_config_snapshot': False}`

## 3. Metric Verdict Layer
- Primary verdict: `improvement`
- Secondary verdict: `improvement`
- Stability verdict: `improvement`
- Efficiency verdict: `regression`
- Overall verdict: `not_comparable`

## 4. Best Eval / Last Eval / Final Probe
- best_eval: `{'reward': 85.20313262939453, 'coverage': 0.8778499960899353, 'success_rate': 0.5833333134651184, 'episode_length': 472.9166564941406, 'repeat_visit_ratio': 0.32970184087753296}`
- last_eval: `{'reward': 92.06893157958984, 'coverage': 0.8749166131019592, 'success_rate': 0.5833333134651184, 'episode_length': 439.0833435058594, 'repeat_visit_ratio': 0.2732466161251068}`
- final_probe: `{'reward': 120.79263305664062, 'coverage': 0.9108062386512756, 'success_rate': 0.875, 'episode_length': 337.6875, 'repeat_visit_ratio': 0.2270648330450058}`
- benchmark_summary: `runtime=None, env_steps_to_best=288000`

## 5. Stop Window
- decision_zone: `not_comparable`
- recommended_action: `analyze_only`
- plateau_detected: `False`
- manual_review_required: `False`
- reasons: comparability_failed, historical_thresholds_are_bootstrap_only, runtime_summary_missing, backfilled_from_historical_run, train_config_unavailable_in_backfill_context, timing_summary_unavailable, total_runtime_unavailable, complete_train_config_not_recoverable_without_checkpoint_loader, train_config_unavailable, historical_thresholds_bootstrap_only

## 6. Manual Review / Evidence Gaps
- manual_review_reasons: runtime_summary_missing
- insufficient_evidence_flags: backfilled_from_historical_run, train_config_unavailable_in_backfill_context, timing_summary_unavailable, total_runtime_unavailable, complete_train_config_not_recoverable_without_checkpoint_loader, train_config_unavailable, historical_thresholds_bootstrap_only

## 7. What GPT Should Output
- Read `docs/reading_order.md`, `docs/current_mainline.md`, `docs/evaluation_charter.md`, and `docs/output_contract.md` before drafting the next decision.
- Any claim of improvement must remain subordinate to comparability. If comparability failed or evidence is insufficient, do not accumulate a positive formal conclusion.
- Output a single `next_gpt_decision.json` payload aligned with `docs/output_contract.md` and the dual-mode protocol schema.
