# Codex Report

## 1. Formal Round
- Round id: `round_0023`
- Experiment mode: `formal_train`
- Source of truth repo: `dk0113-Y/DRL-path-finding`
- Local execution repo path: `C:\Users\Dk\Desktop\SCI\代码1`
- Target run: `outputs/sched_turn003_revisit010_entry8_20260411_014043`
- Baseline round id: `round_0022`
- Baseline commit sha: `77abe585e060c615ec9621e6954e462420708825`
- Comparability group: `formal_mainline_v1__bf21a9e8fbc5`

## 2. Comparability Gate
- Comparability status: `bootstrap_comparable`
- Decision zone: `manual_review_required`
- Stop action: `pause_for_manual_review`
- Manual review reasons: comparability_only_bootstrap_confirmed, runtime_summary_missing
- Insufficient evidence flags: backfilled_from_historical_run, train_config_unavailable_in_backfill_context, timing_summary_unavailable, total_runtime_unavailable, complete_train_config_not_recoverable_without_checkpoint_loader, train_config_unavailable, bootstrap_thresholds_required, historical_thresholds_bootstrap_only
- Historical calibration: `available=True, insufficient_history_for_calibration=True`

## 3. Verdicts
- Primary verdict: `regression`
- Secondary verdict: `regression`
- Stability verdict: `regression`
- Efficiency verdict: `hold`
- Overall verdict: `regression`

## 4. Final Probe
- Reward: `88.30387115478516`
- Coverage: `0.8923500180244446`
- Success rate: `0.5625`
- Episode length: `479.75`
- Repeat visit ratio: `0.32285091280937195`

## 5. Stability / Monitoring
- Timeout flag: `0.4375`
- Stall trigger count: `238.4375`
- Zero info step count: `313.125`
- Recent revisit count: `124.375`

## 6. Efficiency
- total_runtime_sec: `None`
- env_steps_to_best: `288000`

## 7. Recommendation
- Recommended next step: `pause_for_manual_review`
- Confidence / caveat: `comparability=bootstrap_comparable; bootstrap_thresholds=True`
