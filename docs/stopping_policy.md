# Stopping Policy

Stopping is protocol-level, not a prose-only judgement.

## Valid Actions

- `run_next_round`
- `stop_experiment`
- `pause_for_manual_review`
- `analyze_only`

## Formal Decision Frame

- `run_next_round`
  - Comparability passed
  - Evidence is sufficient
  - The round is not showing a clear regression or compute-wasting plateau
- `stop_experiment`
  - Comparable evidence shows sustained plateau or regression and there is no clear reason to spend more compute
- `pause_for_manual_review`
  - Evidence is mixed
  - Comparability is only bootstrap-confirmed
  - Runtime or semantic monitoring needs explicit human review
- `analyze_only`
  - Comparability failed
  - The round may still be useful for diagnosis, but it cannot extend the formal conclusion chain

## Plateau And Bootstrap Status

- Historical calibration is currently bootstrap-only because older runs lack full config snapshots
- A plateau claim is therefore provisional unless later rounds provide stronger exact comparability metadata
- If a round is not comparable, it must not be treated as part of the same stopping window
