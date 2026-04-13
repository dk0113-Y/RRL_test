# Evaluation Charter

Formal evaluation has three layers.

## 1. Long-Term Fixed Standards

- Completeness gate
  - Required artifact family must exist
  - Missing required JSON or CSV means the round cannot support a formal verdict
- Primary task metrics
  - Use best_eval, last_eval, and final_probe together
  - Primary metrics: `success_rate`, `coverage`, `reward`
- Stability metrics
  - Use timeout, stall, zero-info, and revisit behavior
- Efficiency metrics
  - Prefer runtime and `env_steps_to_best`
- Not-comparable gate
  - If comparability fails, no formal promotion/regression claim may accumulate

## 2. Current Phase Standards

Current phase emphasis:

- Do not break the current shared-semantic dual-state mainline
- Judge wall-clock and final performance together when runtime data exists
- Keep semantic monitoring from degrading materially
- Treat best_eval, last_eval, and final_probe as a joint packet rather than trusting a single scalar

## 3. Single-Round Local Verdicts

Each round should expose at least:

- `primary_metric_verdict`
- `secondary_metric_verdict`
- `stability_verdict`
- `efficiency_verdict`
- `overall_round_verdict`

The current exchange protocol also carries:

- `decision_zone`
- `stop_window_state`
- `manual_review_reasons`
- `insufficient_evidence_flags`
