# Level 1.5 Semi-Automatic Tuning Workflow

## Purpose

- The automation tuning system serves the main training project.
- The automation tuning system is not the main paper contribution.
- The goal is to reduce manual data movement and tuning-analysis burden.

## Workflow

1. Human launches training manually.
2. Codex performs one-shot post-run intake and analysis after completion.
3. Codex records a private report and lightweight evidence.
4. GPT decision agent reviews Codex outputs.
5. Human launches the next training command if accepted.
6. Human confirms public round registration only when needed.

## Round Semantics

- `rounds/` stores accepted reference states only.
- `CURRENT_ROUND.json` points to the current accepted reference.
- failed/diagnostic/risky/aborted runs do not become new rounds.
- completed but not accepted runs are recorded as evidence.

## Evidence Semantics

- `evidence_index.json` stores lightweight reviewed-run evidence.
- Evidence entries may include `accepted`, `failed`, `diagnostic`, `risky`, `aborted`, and `continuation`.
- Evidence does not update `CURRENT_ROUND.json`.
- Evidence may point to private `DRL_automatic` report paths.

## Automation Level Boundary

- Level 1.5 allows automatic post-run data flow and report generation.
- Level 1.5 does not allow automatic training launch.
- Level 1.5 does not allow automatic public round registration.
- Level 1.5 does not allow checkpoint publication.

## Decision Agent Boundary

- GPT can act as decision agent after Codex produces analysis.
- GPT can recommend next tuning axis, accept, reject, continue, or close axis.
- GPT recommendation is not a controller action unless explicitly converted later.

## Governance Principles

1. The main paper object is the training project, not the automation tuning system.
2. The automation tuning system only serves efficient generation, comparison, and filtering of tuning outcomes.
3. A public round is an accepted reference state, not a training-log collection.
4. failed/rejected/diagnostic/risky/aborted runs have decision value but do not advance `CURRENT_ROUND.json`.
5. Negative evidence should be recorded via lightweight evidence index, not full public rounds.
6. Codex default duty is one-shot post-run data flow and analysis.
7. GPT decision agent duty is giving next-round tuning suggestions from Codex outputs.
8. Human duty remains training-launch confirmation and final confirmation of accepted public rounds.
9. Under Level 1.5, data flow and analysis are automated; training launch and accepted-round publishing are not automated.
10. Do not add heavy public narrative documents for normal failed runs.
