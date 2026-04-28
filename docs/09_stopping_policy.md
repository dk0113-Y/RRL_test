# 09 Stopping Policy

## Purpose

This document converts evaluation verdicts into action-level stopping, continuation, branching, rejection, and manual-review policies.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`
- `docs/03_current_mainline.md`
- `docs/04_automation_scope.md`
- `docs/05_round_protocol.md`
- `docs/06_formal_artifact_map.md`
- `docs/07_tuning_policy.md`
- `docs/08_evaluation_charter.md`

It defines semantic action rules for Web GPT and `DRL_automatic`.

It does not define exact output schema fields. Controller-consumable schema is defined in `docs/10_output_contract.md`.

## Decision Preconditions

Before selecting any action, decision logic must verify:
- active round pointer from `CURRENT_ROUND.json`
- round type
- operating mode
- evaluation verdict(s) from `docs/08_evaluation_charter.md`
- comparability status
- artifact sufficiency
- `config_diff.json` and frozen-field status
- manual-review requirements
- whether `decision_required_from_gpt` is true
- whether round type is `baseline_registration`, `dry_run_no_train`, `formal_train_result`, `analysis_only`, or `failed_or_rejected`

Precondition rules:
- If `decision_required_from_gpt=false`, do not create a formal decision unless the user explicitly requests one.
- If evidence is incomplete, action must downgrade to `pause_for_manual_review`, `analyze_only`, or `inconclusive` handling path as appropriate.
- If comparability fails, do not accept as same-group improvement.
- If frozen fields are violated, fail closed.
- If source artifacts and exchange summaries conflict without resolution, route to manual review.

Current context reminder:
- active round is `round_0001`
- current phase is `baseline_registration`
- baseline role is `engineering_speed_baseline_candidate`
- this is not method-performance superiority evidence

## Action Vocabulary

The following are controller-level actions (not verdict labels).

### `accept_baseline`

Meaning:
- accept a candidate round as new baseline anchor, or confirm an existing baseline registration

Use conditions:
- evidence is sufficient for round type
- comparability is acceptable, or baseline-registration context is explicit
- claim boundary is explicit
- no unresolved frozen-field violation

### `run_next_round`

Meaning:
- allow controller to proceed to a next planned round

Use conditions:
- current state supports continued exploration
- next round uses new `round_id`
- next proposal obeys tuning and comparability constraints

### `continue_local_search`

Meaning:
- continue tuning in same direction, same comparability group or subgroup

Use conditions:
- result is promising but not decisive
- scope remains valid
- attribution remains clear enough for local iteration

### `branch_new_direction`

Meaning:
- start a new tuning branch or new comparability subgroup

Use conditions:
- objective changes
- comparability group changes
- result is informative but not same-group comparable
- multi-change attribution is weak but diagnostically useful

### `stop_direction`

Meaning:
- stop current tuning direction

Use conditions:
- plateau persists
- regression repeats
- no useful improvement after adequate local search
- instability persists
- compute cost is no longer justified

### `reject_round`

Meaning:
- reject current round from formal accumulation

Use conditions:
- protocol violation
- frozen-field violation
- failed preflight
- invalid artifact bundle
- failed or incomplete run presented as formal result
- checkpoint binary publication violation
- stale outbox dependency

### `pause_for_manual_review`

Meaning:
- pause automation until human review resolves ambiguity or scope violation

Use conditions:
- manual-review field changed
- comparability is ambiguous
- artifact contradiction unresolved
- GPT decision missing when required
- method/reward/protocol/runtime numerical behavior changed

### `analyze_only`

Meaning:
- preserve evidence for diagnosis without formal accumulation claim

Use conditions:
- not comparable
- incomplete evidence
- synthetic rehearsal
- dry-run outputs
- historical or reference-only material

Action boundary:
- these are actions, not evaluation verdicts
- exact serializable schema is defined in `docs/10_output_contract.md`

## Verdict-To-Action Guidance

Guidance from evaluation verdicts (`docs/08_evaluation_charter.md`) to actions:

### `clear_improvement`

Typical actions:
- `accept_baseline`
- `run_next_round`

Required checks:
- comparability valid
- artifact sufficiency valid
- stability acceptable

Stability caveat:
- if best-vs-last gap is large, prefer `accept_baseline` with explicit stability warning or `continue_local_search` over unconditional aggressive progression

### `weak_improvement`

Typical actions:
- `continue_local_search`
- sometimes `run_next_round`

Policy:
- should not automatically replace baseline
- should preserve attribution clarity through controlled next-step proposal

### `neutral_or_mixed`

Typical actions:
- `continue_local_search`
- `branch_new_direction`
- `stop_direction`

Selection logic:
- choose based on trend persistence, cost, and diagnostic clarity
- if performance neutral but efficiency positive, isolate efficiency claim and avoid method-promotion language

### `regression`

Typical actions:
- `reject_round`
- `stop_direction`
- rollback to previous baseline context

Optional diagnostic path:
- if regression is diagnostically useful, retain as `analyze_only` evidence

### `efficiency_improvement_only`

Typical actions:
- efficiency-focused branch continuation
- `accept_baseline` only in explicit engineering-speed baseline context with preserved formal outcome boundaries

Hard boundary:
- must not be treated as method-performance improvement

### `stability_risk`

Typical actions:
- `continue_local_search`
- `pause_for_manual_review`
- `branch_new_direction`

Severity rule:
- severe unresolved stability risk may block baseline promotion
- best-vs-last gap must be reflected in action rationale

### `not_comparable`

Typical actions:
- `analyze_only`
- `branch_new_direction`
- `pause_for_manual_review`

Hard boundary:
- must not support same-group baseline promotion

### `inconclusive`

Typical actions:
- `pause_for_manual_review`
- `analyze_only`
- rerun planning only when protocol permits

Hard boundary:
- must not support baseline promotion

### `analysis_only`

Typical actions:
- `analyze_only`
- optional branch planning input

Hard boundary:
- no formal baseline update

## Baseline Promotion Rules

Baseline replacement or confirmation must be explicit.

Promotion requirements:
- sufficient evidence for round type
- clear comparability status
- no unresolved protocol violation
- explicit claim boundary

Promotion cautions:
- stronger final probe alone may be insufficient when stability risk, comparability ambiguity, or missing core evidence is severe
- runtime speedup alone may support engineering baseline context only, not method-performance baseline

Current baseline policy:
- `round_0001` is already registered as engineering speed baseline candidate
- it is not method-performance baseline evidence
- if later round becomes new baseline, prior baseline must remain traceable

Traceability rule:
- baseline updates must preserve historical chain and rationale

## Continue / Stop / Branch Rules

### Continue local search

Prefer `continue_local_search` when:
- improvement is weak but consistent
- controlled single-axis tuning remains plausible
- stability issue appears tunable
- no major comparability violation
- budget remains acceptable for another local attempt

### Stop direction

Prefer `stop_direction` when:
- regression repeats under comparable setup
- plateau persists across adequate attempts
- best-vs-last gap remains persistently large without trend improvement
- efficiency cost is too high for marginal value
- direction conflicts with frozen-field or scope policy

### Branch new direction

Prefer `branch_new_direction` when:
- evidence is promising but not same-group comparable
- multi-change attribution is unclear
- objective shifts (performance vs efficiency vs stability)
- runtime A/B exploration requires separate group
- manual-reviewed semantic change requires new group

## Manual Review Triggers

Manual review is required when any of the following is true:
- reward semantics changed
- architecture changed
- state tensor schema changed
- Shared Semantic Layer semantics changed
- value or advantage branch semantics changed
- formal protocol changed
- final probe definition changed
- checkpoint winner policy changed
- environment distribution changed
- evaluation seed policy changed
- runtime numerical behavior changed
- artifact contradiction unresolved
- final probe missing for `formal_train_result`
- candidate-selection evidence missing for `formal_train_result`
- unknown fields appear in `config_diff`
- stale decision dependency or outbox dependency detected
- checkpoint binary accidentally published

Manual-review rules:
- manual review does not automatically preserve comparability
- manual-review outcomes must be recorded in a later decision or round summary
- unresolved review items block autonomous continuation

## Round-Type-Specific Action Rules

### `baseline_registration`

Action policy:
- confirm baseline registration or keep placeholder when no decision required
- do not infer method-performance improvement
- `round_0001` may remain baseline anchor without formal accept/reject decision if `decision_required_from_gpt=false`
- when explicitly requested, `accept_baseline` is allowed with explicit engineering-speed claim boundary

### `formal_train_result`

Action policy:
- use full evaluation verdict path
- require comparability and artifact sufficiency before baseline promotion
- downgrade action when instability is material
- regression must not overwrite current baseline

### `dry_run_no_train`

Action policy:
- actions concern controller readiness, not training performance
- `run_next_round` may be used only toward protocol/controller next step
- failed dry-run should trigger `pause_for_manual_review` or `reject_round` for controller path until fixed

### `analysis_only`

Action policy:
- default action is `analyze_only`
- may inform branch plan or protocol fix
- no formal baseline update

### `failed_or_rejected`

Action policy:
- default action is `reject_round` or `pause_for_manual_review`
- failed round cannot be formal winner
- failure evidence must be preserved

## Best-vs-Last And Stability Action Policy

Stability-action rules:
- large best-vs-last gap is not automatic rejection, but it blocks overconfident promotion
- if winner final probe is strong but last checkpoint is poor, action should carry explicit stability warning
- if late-stage degradation repeats across rounds, prefer `stop_direction`, `branch_new_direction`, or `pause_for_manual_review`
- if gap narrows while winner remains strong, confidence in continuation or promotion may increase
- repeat visits, stalls, timeouts, and zero-info behavior should inform stability-oriented action choices when available

Risk-handling rule:
- unresolved stability risk may reduce action confidence even when efficiency improves

## Efficiency Action Policy

Efficiency-action rules:
- runtime improvement can support `efficiency_improvement_only`
- engineering-speed baseline acceptance requires explicit claim boundary
- runtime speedup does not imply method innovation
- runtime-toggle changes require separate group or manual review when numerical behavior may change
- efficiency regression may justify `stop_direction` or `branch_new_direction` even with performance gains, depending on objective and budget

Current-runtime context reminder:
- baseline runtime toggles are AMP false, inference AMP false, `torch.compile` false, `channels_last` false, TF32 true, `cudnn_benchmark` true

## Fail-Closed Rules

The following conditions must fail closed:
- frozen-field violation
- unknown config field default-pass attempt
- preflight failed but training attempted
- checkpoint binary publication
- stale global outbox decision consumed
- synthetic rehearsal used as formal evidence
- failed or incomplete run presented as formal winner
- `formal_train_result` missing core final-probe evidence
- comparability failed but same-group improvement claimed

Fail-closed actions:
- `reject_round`
- `pause_for_manual_review`
- `analyze_only`

Fail-closed rule:
- no automatic continuation is allowed until violation is resolved and documented.

## Relationship To Other Protocol Docs

Cross-document boundary map:
- `07_tuning_policy.md`: tuning and comparability constraints
- `08_evaluation_charter.md`: evaluation verdicts and metric interpretation
- `09_stopping_policy.md`: maps verdicts to controller-level actions
- `10_output_contract.md`: exact decision schema and controller-action fields

Use-order guidance:
- use evaluation charter to derive verdicts first
- use this document to map verdicts and constraints to actions
- use output contract to serialize decisions for controller consumption
