# 08 Evaluation Charter

## Purpose

This document defines how round outcomes should be evaluated in the automated tuning system.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`
- `docs/03_current_mainline.md`
- `docs/04_automation_scope.md`
- `docs/05_round_protocol.md`
- `docs/06_formal_artifact_map.md`
- `docs/07_tuning_policy.md`

It defines evaluation layers, metric roles, interpretation rules, and evidence sufficiency rules.

It does not define:
- stopping actions and branch actions
- controller action schema
- exact GPT decision schema

Those are defined by `09_stopping_policy.md` and `10_output_contract.md`.

## Evaluation Preconditions

Before any round evaluation, reviewers must check:
- active round pointer from `CURRENT_ROUND.json`
- round type
- operating mode
- artifact completeness and missingness
- comparability report
- config diff
- baseline reference
- whether the round is `baseline_registration`, `dry_run_no_train`, `formal_train_result`, `analysis_only`, or `failed_or_rejected`

Precondition rules:
- If comparability is missing or failed, no formal improvement or regression claim may be accumulated.
- If evidence is incomplete, conclusion must be downgraded to `inconclusive`, `analysis_only`, or `manual-review-required` as appropriate.
- Synthetic rehearsal is not formal evidence.
- Backup directories are reference-only.
- Exchange prose must not override active structured JSON evidence.

Current context note:
- active round is `round_0001`
- phase is `baseline_registration`
- `decision_required_from_gpt` is false
- `round_0001` is not method-performance superiority evidence

## Evaluation Layers

Evaluation must proceed through the following layers in order:
1. Protocol validity
2. Comparability validity
3. Artifact sufficiency
4. Primary task performance
5. Stability and degradation risk
6. Efficiency and wall-clock evidence
7. Diagnostic consistency
8. Claim boundary

### 1) Protocol validity

Must verify:
- current method mainline is preserved
- `formal_posthoc_trainselect_v1` is preserved
- no frozen-field violation is present

If invalid:
- fail closed to `not_comparable` or `manual-review-required`
- do not accumulate formal improvement claim

### 2) Comparability validity

Must verify:
- same group, subgroup, or reference-only status
- budget compatibility or subgroup explanation
- environment and evaluation seed consistency

If comparability is weak:
- downgrade claim strength
- prefer reference-only or inconclusive interpretation

### 3) Artifact sufficiency

Must verify:
- final probe evidence availability (when required by round type)
- train-side evidence availability
- explicit missingness in `artifact_digest.json`
- `config_diff.json` availability and quality

If core evidence is missing:
- do not infer values
- classify as inconclusive or analysis-only

### 4) Primary task performance

Must evaluate core outcomes such as:
- success behavior
- coverage behavior
- reward behavior
- task completion and episode-length behavior when available

Primary interpretation rule:
- reward must not be interpreted alone; success and coverage are core exploration outcomes.

### 5) Stability and degradation risk

Must evaluate:
- best-vs-last gap
- repeat-visit behavior
- stall and timeout tendencies
- late-stage degradation indicators

Stability rule:
- strong winner with weak last checkpoint indicates elevated risk.

### 6) Efficiency and wall-clock evidence

Must evaluate:
- total runtime
- runtime per step when available
- time to selected winner or selected candidate when available
- overhead from final probe and artifact generation when available

Efficiency claim rule:
- efficiency evidence supports engineering claims only; it does not alone prove method innovation.

### 7) Diagnostic consistency

Must check consistency across sources:
- train-side candidate quality should not severely contradict held-out final probe without explanation
- reward interpretation should be consistent with penalties and coverage or success behavior

If contradiction exists:
- downgrade confidence
- request manual review when unresolved

### 8) Claim boundary

Must classify claim type explicitly:
- engineering speed baseline claim
- method-performance claim
- reference-only evidence
- diagnostic-only evidence

Boundary rule:
- no method-performance claim may be made when comparability or evidence sufficiency is not satisfied.

## Metrics And Roles

### Primary task metrics

Primary metrics for formal performance interpretation:
- final probe success rate
- final probe coverage
- final probe reward

Role rules:
- these are central for `formal_train_result` evaluation under held-out final probe
- reward must be interpreted with success and coverage, not in isolation

### Secondary and diagnostic metrics

Diagnostic metrics include:
- episode length
- repeat-visit ratio (RVR)
- revisit penalty indicators
- stall penalty indicators
- timeout penalty indicators
- zero-information behavior
- information gain proxy when available
- train-side trend indicators
- selected candidate step or checkpoint step
- best-vs-last gap indicators

Role rules:
- diagnostic metrics help explain behavior and risk
- diagnostic metrics do not replace final probe as formal outcome evidence

### Efficiency metrics

Efficiency metrics include:
- total wall-clock runtime
- runtime per environment step when available
- time to selected winner or selected candidate
- environment steps to selected winner or selected candidate
- final probe and publication overhead when available

Role rules:
- efficiency metrics support engineering interpretations
- efficiency metrics alone do not prove method-performance superiority

## formal_posthoc_trainselect_v1 Evaluation Rules

Evaluation under `formal_posthoc_trainselect_v1` must follow protocol semantics.

### Training phase interpretation

Rules:
- only train-side statistics and checkpoints are expected during training
- absence of training-time `eval_metrics` does not automatically invalidate this protocol
- train-side improvement is candidate-selection evidence, not final proof

### Posthoc selection interpretation

Rules:
- candidate selection must be train-side and post-training
- selection criteria should be recorded or summarized
- selection quality should be evaluated through held-out final probe outcomes

### Held-out final probe interpretation

Rules:
- held-out final probe is main formal outcome evidence for selected candidates
- final probe must not be replaced by train-side statistics
- final probe interpretation must include candidate-count and selection-policy context

### Winner and last-checkpoint interpretation

Rules:
- formal winner is `best.pt` when registered under protocol
- `last.pt` is diagnostic only
- large best-vs-last gap indicates late-stage stability risk
- weak last checkpoint does not automatically invalidate winner, but it reduces stability confidence

## Baseline And Comparison Rules

Baseline rules:
- `round_0001` is current engineering speed baseline anchor
- it may be used as starting context for later automated tuning
- it must not be cited as method-performance superiority evidence

Comparison rules for future rounds:
- every round must state same-group comparable, subgroup comparable, or reference-only status
- different training budgets require subgroup-aware comparison
- a longer run beating a shorter run does not automatically prove method improvement
- runtime speedup must be interpreted separately from method performance

Reference-only rule:
- non-comparable rounds can still provide useful diagnostics but cannot accumulate formal superiority claims

## Stability And Best-vs-Last Interpretation

Stability rules:
- best-vs-last gap is a key stability diagnostic
- strong winner with poor last checkpoint indicates possible late-stage degradation or instability
- small best-vs-last gap supports stability but does not alone prove generalization
- if winner step is early relative to total budget, later-stage dynamics should be diagnosed
- repeat visits, stalls, timeouts, and zero-info behavior should be used to interpret exploration stability when available
- stability concerns may reduce confidence even when winner final probe is strong

Interpretation boundary:
- stability risk is not equal to automatic rejection
- stability risk must be reflected in verdict strength and downstream stopping review

## Efficiency Interpretation

Efficiency rules:
- runtime improvement can support engineering baseline claims
- efficiency should be evaluated under comparable runtime toggles, hardware context when available, and comparable budget context
- current baseline runtime toggles are:
  - AMP false
  - inference AMP false
  - `torch.compile` false
  - `channels_last` false
  - TF32 true
  - `cudnn_benchmark` true
- runtime-toggle changes may require separate efficiency A/B grouping
- efficiency improvement must not be presented as method innovation
- efficiency regression may matter even when performance improves, depending on objective and compute budget

Boundary rule:
- efficiency and performance conclusions must be reported separately before any combined interpretation.

## Missingness And Inconclusive Evidence

Missingness rules:
- missing artifacts must be recorded explicitly
- missing evidence must not be invented
- missing `eval_metrics.csv` does not invalidate `round_0001` baseline registration by default

Round-type interpretation:
- for `formal_train_result`, missing final probe or candidate-selection evidence may block formal evaluation
- for `dry_run_no_train`, missing training artifacts are expected only when explicitly marked dry-run
- for `analysis_only`, missingness may still permit diagnostic value with claim boundary controls

Verdict downgrade rules:
- if evidence is incomplete, allowed verdict should be `inconclusive`, `analysis_only`, or `manual-review-required`
- if comparability fails, verdict must include `not_comparable` and must not accumulate formal improvement claim

## Evaluation Verdict Vocabulary

Recommended evaluation verdict labels:
- `clear_improvement`
- `weak_improvement`
- `neutral_or_mixed`
- `regression`
- `efficiency_improvement_only`
- `stability_risk`
- `not_comparable`
- `inconclusive`
- `analysis_only`

Vocabulary rules:
- these are evaluation verdicts, not controller actions
- actions are defined in `09_stopping_policy.md` and represented through `10_output_contract.md`
- combined labels are allowed when justified, such as:
  - `neutral_or_mixed` + `efficiency_improvement_only`
  - `weak_improvement` + `stability_risk`

## Round-Type-Specific Evaluation

### `baseline_registration`

Must evaluate:
- round identity
- artifact map condition
- baseline role
- explicit claim boundary

Must not infer:
- method-performance improvement

Legacy-missingness rule:
- missing legacy eval-centric artifacts may be acceptable when explicitly recorded

### `formal_train_result`

Must evaluate in order:
- comparability validity
- artifact sufficiency
- held-out final probe outcomes
- winner-vs-last stability diagnostic
- efficiency context when available

Output rule:
- produce evaluation verdict(s), not stopping action directly

### `dry_run_no_train`

Must evaluate:
- controller path correctness
- schema generation quality
- preflight guard execution
- publication and decision-flow logic

Must not evaluate:
- training performance claims

### `analysis_only`

Must evaluate:
- diagnostic usefulness
- evidence transparency

Must not accumulate:
- formal improvement claim

### `failed_or_rejected`

Must evaluate:
- failure cause
- evidence preservation quality
- correction traceability

Must not classify as:
- formal winner

## Relationship To Other Protocol Docs

Cross-document boundary map:
- `06_formal_artifact_map.md`: artifact roles and evidence availability
- `07_tuning_policy.md`: config changes and comparability groups
- `08_evaluation_charter.md`: metric interpretation and evaluation verdicts
- `09_stopping_policy.md`: translates evaluation verdicts into stop/continue/branch/manual-review actions
- `10_output_contract.md`: exact decision schema and controller-action fields

Use-order guidance:
- use this document to evaluate evidence quality and meaning
- use stopping-policy document for action routing
- use output-contract document for schema-compliant decision publication
