# Tuning Policy

Formal rounds must preserve comparability before they are allowed to accumulate research conclusions.

## Allowed To Tune

- Runtime-efficient launch flags that do not alter the evaluation contract
- Clearly scoped training hyperparameters that stay inside the same comparability group
- Diagnostics, logging, and bundle-generation helpers

## Must Stay Frozen For A Comparable Series

- The real training entry (`train_q_agent.py`)
- The reward/evaluation contract that defines `eval_metrics.csv` and `final_probe.csv`
- The best-checkpoint rule
- The final-probe rule
- The semantic meaning of the current shared-semantic dual-state architecture

## Comparability Breakers

The following changes must be treated as comparability-breaking until proven otherwise:

- Changing the main state semantics back toward the old near/mid/token branch framing
- Changing the emitted metric schema in `eval_metrics.csv` or `final_probe.csv`
- Changing the best-checkpoint criterion
- Changing final probe evaluation from `best.pt if available else online last`
- Changing total env steps, total train episodes, budget mode, fixed train episode seed settings, or other fields that move a run into a different comparability group

## Current Calibration Status

- Historical thresholds are bootstrap-only
- Any round built from backfilled historical artifacts can support review and provisional judgement
- Hard promotion or stop decisions still require comparability plus explicit evidence review
