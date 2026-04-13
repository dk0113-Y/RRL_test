# Codex Report

## 1. Formal Round
- Round id: `round_0022`
- Experiment mode: `formal_train`
- Source of truth repo: `C:\Users\Dk\Desktop\SCI\代码1`
- Target run: `C:\Users\Dk\Desktop\SCI\代码1\outputs\sched_turn005_revisit010_entry8_20260410_234142`
- Baseline round id: `round_0021`
- Baseline commit sha: `832dee8042424a2009fe2fff2f5137b6efbc15c5`
- Comparability group: `formal_mainline_v1__bf21a9e8fbc5`

## 2. Comparability Gate
- Comparability status: `not_comparable`
- Decision zone: `not_comparable`
- Stop action: `analyze_only`
- Manual review reasons: runtime_summary_missing
- Insufficient evidence flags: backfilled_from_historical_run, train_config_unavailable_in_backfill_context, timing_summary_unavailable, total_runtime_unavailable, complete_train_config_not_recoverable_without_checkpoint_loader, train_config_unavailable, historical_thresholds_bootstrap_only

## 3. Verdicts
- Primary verdict: `improvement`
- Secondary verdict: `improvement`
- Stability verdict: `improvement`
- Efficiency verdict: `regression`
- Overall verdict: `not_comparable`

## 4. Final Probe
- Reward: `120.79263305664062`
- Coverage: `0.9108062386512756`
- Success rate: `0.875`
- Episode length: `337.6875`
- Repeat visit ratio: `0.2270648330450058`

## 5. Stability / Monitoring
- Timeout flag: `0.125`
- Stall trigger count: `111.6875`
- Zero info step count: `172.75`
- Recent revisit count: `88.875`

## 6. Efficiency
- total_runtime_sec: `None`
- env_steps_to_best: `288000`

## 7. Recommendation
- Recommended next step: `analyze_only`
- Confidence / caveat: `comparability=not_comparable; bootstrap_thresholds=True`
