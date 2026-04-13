# Codex Report

## 1. Formal Round
- Round id: `round_0021`
- Experiment mode: `formal_train`
- Source of truth repo: `dk0113-Y/DRL-path-finding`
- Local execution repo path: `C:\Users\Dk\Desktop\SCI\代码1`
- Target run: `outputs/4.9_30万轮基线`
- Baseline round id: `UNSET`
- Baseline commit sha: `UNSET`
- Comparability group: `formal_mainline_v1__bf21a9e8fbc5`

## 2. Comparability Gate
- Comparability status: `insufficient_evidence`
- Decision zone: `insufficient_evidence`
- Stop action: `pause_for_manual_review`
- Manual review reasons: runtime_summary_missing
- Insufficient evidence flags: backfilled_from_historical_run, train_config_unavailable_in_backfill_context, timing_summary_unavailable, total_runtime_unavailable, complete_train_config_not_recoverable_without_checkpoint_loader, train_config_unavailable, historical_thresholds_bootstrap_only
- Historical calibration: `available=True, insufficient_history_for_calibration=True`

## 3. Verdicts
- Primary verdict: `insufficient_evidence`
- Secondary verdict: `insufficient_evidence`
- Stability verdict: `insufficient_evidence`
- Efficiency verdict: `insufficient_evidence`
- Overall verdict: `insufficient_evidence`

## 4. Final Probe
- Reward: `86.50942993164062`
- Coverage: `0.8751125335693359`
- Success rate: `0.625`
- Episode length: `454.9375`
- Repeat visit ratio: `0.38662803173065186`

## 5. Stability / Monitoring
- Timeout flag: `0.375`
- Stall trigger count: `213.1875`
- Zero info step count: `295.625`
- Recent revisit count: `177`

## 6. Efficiency
- total_runtime_sec: `None`
- env_steps_to_best: `264000`

## 7. Recommendation
- Recommended next step: `pause_for_manual_review`
- Confidence / caveat: `comparability=insufficient_evidence; bootstrap_thresholds=True`
