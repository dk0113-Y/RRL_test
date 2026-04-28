# 06 Formal Artifact Map

## Purpose

This document defines artifact roles, artifact evidence boundaries, and exchange publication rules for the automated tuning system.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`
- `docs/03_current_mainline.md`
- `docs/04_automation_scope.md`
- `docs/05_round_protocol.md`

This document maps source-side training outputs into exchange-side round evidence and defines what should be copied, what should be metadata-only, and what must be excluded.

This document does not define:
- full lifecycle state transitions
- full tuning policy
- evaluation scoring rules
- exact GPT decision schema

Those are defined in `05`, `07`, `08`, and `10`.

## Artifact Authority Model

Artifact authority sources:
- Training fact source: `DRL-path-finding` outputs and logs.
- Automation fact source: `DRL_automatic` parsing, digesting, and publication records.
- Exchange fact source: `RRL_test/rounds/<current_round>/` structured JSON and copied lightweight logs.
- Decision fact source: `gpt_decision.json` when present and valid for the active round.

Authority constraints:
- `CURRENT_ROUND.json` selects the active round.
- Active round structured JSON is fact authority for exchange-facing evidence.
- Backup directories are reference-only.
- Human-readable prose is auxiliary and must not override structured evidence.

Interpretation rule:
- when evidence statements conflict, use entry-guide authority order and resolve at structured JSON level before interpreting prose.

## Artifact Classes

### Source-side training artifacts

Typical contents:
- training logs
- train-side statistics
- posthoc selection summaries
- final probe outputs
- config snapshots
- checkpoint metadata
- checkpoint binaries

Origin rule:
- these artifacts originate from `DRL-path-finding` execution and related source-side run directories.

Transfer rule:
- not all source-side artifacts should be copied to `RRL_test`.

### Exchange-side structured artifacts

Core files:
- `index_manifest.json`
- `round_summary.json`
- `artifact_digest.json`
- `config_diff.json`
- `comparability_report.json`
- `baseline_registration.json` when applicable
- `gpt_decision.json` or `gpt_decision_placeholder.json`

Role:
- these are primary exchange-facing structured evidence files
- they should be machine-readable and auditable
- they should be sufficient for a fresh GPT reader to understand round identity, evidence condition, missingness state, and claim boundary

### Copied lightweight evidence

Typical files:
- `artifacts/logs/*.csv`
- `artifacts/metadata/csv_summaries.json`
- other small text, JSON, or CSV evidence when explicitly allowed

Role:
- copied lightweight evidence supports structured artifacts
- copied lightweight evidence must not replace `artifact_digest.json` or `comparability_report.json` as primary structured controls

### Metadata-only artifacts

Typical files or references:
- checkpoints (`.pt`, `.pth`, `.ckpt`)
- large timing and profile files
- large raw logs
- local source paths
- full raw outputs directories

Policy:
- these may be registered by path, size, sha256, and `reason_if_not_copied`
- they must not be copied into `RRL_test` unless protocol review explicitly reclassifies them

### Forbidden exchange artifacts

Forbidden in `RRL_test` publication:
- `.pt`
- `.pth`
- `.ckpt`
- full raw `outputs/` directories
- uncompressed large profiler dumps unless explicitly reviewed
- stale global outbox files
- synthetic evidence presented as formal evidence

Fail-closed rule:
- forbidden artifacts must be blocked from publication and logged as protocol violations if attempted.

## Round Bundle Minimum Requirements

This section defines round-level minimum evidence presence by round type.

### `baseline_registration`

Minimum bundle should include:
- `index_manifest.json`
- `round_summary.json`
- `baseline_registration.json`
- `artifact_digest.json`
- `config_diff.json`
- `comparability_report.json`
- `gpt_decision_placeholder.json` or `gpt_decision.json`

Interpretation rules:
- baseline registration may not include every artifact expected from later formal training rounds
- missing legacy files such as `eval_metrics.csv` must not invalidate registration by default
- claim boundary must be explicit: registration anchor, not method-superiority claim

### `formal_train_result`

Minimum bundle should include:
- `index_manifest.json`
- `round_summary.json`
- `artifact_digest.json`
- `config_diff.json`
- `comparability_report.json`
- `gpt_decision.json` or placeholder
- train-side logs or summaries
- final probe evidence for selected candidates
- checkpoint metadata and hash records

Interpretation rules:
- complete required field schema is not defined here
- bundle must be sufficient for comparability and evaluation review
- missing required formal evidence must be explicit and may block formal decision

### `dry_run_no_train`

Minimum bundle should include:
- `index_manifest.json`
- `round_summary.json`
- `config_diff.json`
- explicit dry-run marker
- missing-artifact explanation
- comparability or preflight report if applicable
- GPT placeholder or dry-run decision record

Interpretation rules:
- missing training artifacts are expected only when dry-run is explicit
- dry-run artifacts must not be interpreted as formal training evidence

### `analysis_only`

Minimum bundle should include:
- `index_manifest.json`
- `round_summary.json`
- source or reference description
- `artifact_digest.json` or equivalent evidence inventory when applicable
- claim boundary that no formal improvement claim is accumulated

### `failed_or_rejected`

Minimum bundle should include:
- `index_manifest.json`
- `round_summary.json`
- explicit failure or rejection reason
- missing-artifact state when relevant
- rollback or correction notes when relevant

Traceability rule:
- failed or rejected rounds must stay traceable and must not be silently removed.

## Current Baseline Artifact Map: `round_0001`

Current status:
- `round_0001` is a baseline registration round.
- It registers run identity:
  - `value_bcchild_gated_statequery_effopt_formal_500k_decay240k_end005_20260422_210814`
- Its artifact map is valid baseline-registration evidence, not a full template for all future formal training bundles.

Known copied lightweight logs:
- `logs/final_probe.csv`
- `logs/train_episodes.csv`
- `logs/train_steps.csv`

Known digest-only artifacts:
- `logs/posthoc_selection_summary.json`
- `logs/config_snapshot.json`

Known metadata-only checkpoint records:
- `checkpoints/best.pt`
- `checkpoints/last.pt`

Known missing artifacts (explicitly recorded):
- `logs/eval_metrics.csv`
- `logs/posthoc_candidate_selection.csv`
- `logs/timing*.csv`
- `logs/profile*.csv`

Interpretation boundaries:
- missing `eval_metrics.csv` does not invalidate `round_0001` baseline registration by default
- missing timing/profile files should be recorded as missingness, not invented
- checkpoint binaries are not copied and must remain metadata-only or hash-only records
- `round_0001` remains an engineering speed baseline candidate, not method-performance superiority evidence

## Formal Protocol Artifact Expectations

Protocol alignment target:
- `formal_posthoc_trainselect_v1`

Training phase should produce:
- checkpoints
- train-side statistics
- `train_episodes` and `train_steps` style logs when implemented
- no training-time validation or recheck requirements under this protocol

Posthoc phase should produce or register:
- candidate selection summary
- candidate checkpoint metadata
- candidate checkpoint hashes when available
- final probe results for selected candidates

Final probe phase should produce:
- held-out final probe results for selected candidates
- formal winner indicator
- final probe ranking or winner evidence when available

Diagnostic tracking should preserve:
- last checkpoint metadata
- best-vs-last gap indicators when available
- explicit missingness when gap evidence is unavailable

Scope boundary:
- this document defines artifact expectations and mapping boundaries
- scoring and judgement criteria are defined in `docs/08_evaluation_charter.md`

## Artifact Digest Requirements

`artifact_digest.json` should record at minimum:
- `source_run_dir` or source identity
- `scanned_at`
- `relative_path`
- `absolute_path` when local
- `exists`
- `size_bytes`
- `sha256` when available
- `copied_to_exchange`
- `reason_if_not_copied`
- `missing_expected_files`
- `copied_files`
- `skipped_large_or_binary_files`

Digest quality rules:
- `sha256` should be used for reproducibility and audit when feasible
- missing files must be explicit
- binary and large files should remain metadata-only
- digest must represent observed state, not inferred state

## Copy Policy

May copy to exchange:
- small CSV logs
- small JSON summaries
- small metadata files
- generated `csv_summaries.json`

Metadata-only in exchange:
- checkpoints
- large timing or profile files unless explicitly reviewed
- large raw logs
- local full outputs directories

Must not copy to exchange:
- `.pt`
- `.pth`
- `.ckpt`
- full raw outputs directory
- stale global outbox content
- unlabeled synthetic evidence presented as formal evidence

Publication hygiene rule:
- copy policy must be enforced before commit and before push.

## Legacy Artifact Handling

Legacy artifact examples:
- `eval_metrics.csv`
- `metric_snapshot.json`
- `benchmark_summary.json`
- `artifact_index.json`

Legacy handling rules:
- these may appear in older protocols or historical bundles
- they are legacy or reference-only unless explicitly present and required by a specific active-round protocol
- they must not be treated as mandatory evidence for current `round_0001` baseline registration
- legacy artifacts must not override active round structured JSON
- if old docs still require legacy fields, mark those docs stale rather than automatically invalidating current rounds

Migration rule:
- protocol migration should update requirements explicitly instead of silently inheriting old eval-centric assumptions.

## Missingness And Incompleteness Rules

Missingness policy:
- missing artifacts must be recorded explicitly
- missingness must not be hidden behind synthetic placeholders
- missing expected artifact does not always mean invalid round; interpretation depends on round type

Round-type interpretation:
- for `formal_train_result`, missing core evidence may block formal decision
- for `baseline_registration`, missing legacy eval-centric artifacts may be acceptable when digest records missingness clearly
- for `dry_run_no_train`, missing training artifacts are acceptable only when dry-run marker is explicit

Incompleteness policy:
- incomplete bundles must be marked as incomplete, failed, analysis-only, or dry-run as appropriate
- incomplete bundles must not be promoted as complete formal evidence packages

Auditability rule:
- missingness and incompleteness states must remain queryable from structured round files
- future rounds should improve completeness without rewriting prior observed states

## Relationship To Other Protocol Docs

Cross-document boundary map:
- `05_round_protocol.md`: lifecycle and minimum round bundle concepts
- `06_formal_artifact_map.md`: artifact roles and copy/hash/missingness policy
- `07_tuning_policy.md`: config changes and comparability groups
- `08_evaluation_charter.md`: metric interpretation and judgement criteria
- `09_stopping_policy.md`: stop, continue, branch, and manual-review actions
- `10_output_contract.md`: exact GPT decision and controller-action schema

Use-order note:
- use this document to classify and handle artifacts
- use lifecycle/tuning/evaluation/output docs for adjacent control logic
- if conflict appears, follow entry-guide authority and fail closed until resolved
