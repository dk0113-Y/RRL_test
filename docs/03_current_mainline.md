# 03 Current Mainline

## Purpose

This document defines the current formal method mainline for the DRL autonomous exploration program.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`
- `docs/02_system_architecture.md`

This document fixes method semantics for the current automated tuning stage and provides a stable semantic contract across reviewers, controller logic, and round publication.

This document is designed to prevent historical naming residue and legacy framing from contaminating current formal judgement.

This document does not define:
- detailed training protocol lifecycle
- detailed tuning policy tables
- detailed evaluation criteria
- full decision output schema

Those are handled by the corresponding numbered protocol documents.

## Formal Mainline Summary

Current formal mainline:
- Dynamic Cumulative Belief Map
- Shared Semantic Layer
- Dual-State Input / Dual-Branch Encoding
- Semantic Dueling Decision Head

Mainline status:
- this is the active formal mainline for the current automated tuning stage
- this is the method narrative that new GPT sessions, Codex sessions, and human reviewers must use
- `round_0001` registers an engineering speed baseline under this mainline and must not be interpreted as method-superiority evidence

Mainline consistency rule:
- all round-level interpretation and comparability reasoning must be grounded in this formal mainline unless a manual-reviewed migration explicitly replaces it

Current-stage reminder:
- project stage remains `automated_tuning_system_design`
- current priority remains protocol design, exchange alignment, and upcoming `DRL_automatic` review
- this is not a free method-redesign period

## State And Input Schema

Current formal input keys:
- `advantage_canvas`
- `value_block_features`
- `value_entry_features`
- `value_block_mask`
- `value_entry_mask`

High-level semantic definitions:

`advantage_canvas`:
- local or action-relevant decision canvas
- supports advantage-side action discrimination
- must not be casually replaced by unrelated local observation tensors

`value_block_features`:
- structured accessible unknown or value-region block representation
- supports global or semi-global value estimation

`value_entry_features`:
- entry-cluster or frontier-entry level structured representation
- supports value-branch reasoning over accessible exploration opportunities

`value_block_mask`:
- validity mask for value blocks

`value_entry_mask`:
- validity mask for value entries

Schema boundary rule:
- this schema is part of the current formal mainline contract
- changing schema keys, schema meaning, or schema role mapping is a mainline-semantic change
- any such change requires manual review before becoming eligible for formal comparability claims

Interpretation discipline:
- names alone are not sufficient; semantic role is the binding contract
- if implementation aliases exist, the exchange narrative must still map back to the formal keys above

## Component Semantics

### Dynamic Cumulative Belief Map

Semantic role:
- represents accumulated exploration belief over time
- acts as an upstream belief-state source for downstream semantic construction

Boundary rules:
- it should not be replaced by one-step observation-only state without explicit method migration
- temporary implementation simplification must not be presented as a formal semantic equivalent
- any reinterpretation that removes cumulative belief behavior is a manual-review change

### Shared Semantic Layer

Semantic role:
- provides shared semantic construction before dual-state encoding
- aligns upstream belief representation with downstream decision-relevant semantics

Boundary rules:
- this layer is a mainline semantic component, not disposable preprocessing
- changes to its semantics require manual review
- silent redefinition as generic feature plumbing is not permitted in formal narrative

### Dual-State Input / Dual-Branch Encoding

Semantic role:
- advantage-side and value-side inputs have different decision roles
- advantage side supports action discrimination
- value side supports broader exploration opportunity and value estimation

Boundary rules:
- the two branches should not be collapsed, swapped, or semantically merged without explicit architecture review
- branch-specific meaning must remain traceable in formal interpretation
- routine tuning must not erase role separation

### Semantic Dueling Decision Head

Semantic role:
- current decision-head semantics for combining value and advantage reasoning
- binds dual-branch outputs into the formal decision surface

Boundary rules:
- must not be silently replaced by generic head variants or legacy fusion designs
- semantic changes require manual review
- if a new head is proposed, it must be treated as method migration with explicit comparability reclassification

## Implementation Anchors

Reference anchors (non-exhaustive):
- `ExplorationQNetwork`
- `StateTensorAdapter`
- `AdvantageCanvasEncoder`
- `ValueTreeEncoder`
- `SemanticDuelingHead`

Anchor usage rules:
- these names are implementation anchors for current mainline orientation
- they are not a complete code map
- code-level details must be inspected in the training repository when needed
- this exchange document fixes semantics, not low-level implementation specifics

Compatibility reminder:
- implementation refactors that preserve the semantic contract may still remain mainline-compatible
- implementation refactors that alter semantic role boundaries are method changes and require manual review

## Historical Semantics Boundary

Historical boundary statement:
- old near/mid/token framing is historical/reference only
- it may appear in old discussion notes, legacy logs, or residual implementation naming
- it must not be used as the current formal method narrative
- it must not be used to reinterpret the current state tensor schema
- it must not be used to justify architecture changes during routine automated tuning

Migration rule:
- any return to near/mid/token-like semantics is a method migration
- method migration requires manual review and a new comparability group
- migration claims must be explicit; implicit reinterpretation is not allowed

Reviewer rule:
- if a document or comment uses near/mid/token as if it were active formal semantics, flag it as stale and non-authoritative for current rounds

## Frozen Mainline Fields

The following fields are frozen in routine automated tuning:
- state tensor schema
- Dynamic Cumulative Belief Map semantics
- Shared Semantic Layer semantics
- advantage-side state meaning
- value-side block and entry representation meaning
- value mask semantics
- dual-branch encoding role separation
- Semantic Dueling Decision Head semantics
- reward semantics, unless manual review explicitly opens a reward branch
- formal protocol, unless manual review explicitly opens protocol migration

Freeze-policy meaning:
- these fields are not normal hyperparameter search dimensions
- they must be treated as manual-review fields
- controller-side automation must enforce boundaries so frozen fields are not changed by routine config generation

Comparability consequence:
- unreviewed changes to frozen fields invalidate same-group comparability assumptions
- if frozen fields change, round classification must be escalated for protocol review

## Allowed Routine Tuning Outside This Document

This document does not define complete tunable parameter policy. It defines semantic boundaries that later tuning policy must respect.

Routine automated tuning should focus on outer-loop parameters such as:
- total environment steps
- epsilon schedule
- warmup settings
- posthoc candidate selection settings
- final probe candidate count
- stopping or extension policy

Interpretation notes:
- the list above is illustrative, not authoritative
- authoritative tuning rules are defined in `docs/07_tuning_policy.md`
- any proposed tuning dimension that touches frozen semantics must be escalated to manual review

Stage discipline:
- the current stage is protocol hardening, not unrestricted search-space expansion
- routine tuning should remain controlled, comparable, and auditable

## Mainline Claim Boundaries

Claim rules:
- this document does not claim that `round_0001` proves method-performance superiority
- engineering speedup must not be presented as method innovation
- method claims require comparable formal evidence and proper evaluation
- missing `eval_metrics.csv` in the baseline-registration context must not be used to invalidate this mainline contract
- mainline stability does not imply every future tuning round is automatically comparable
- comparability must be checked per round using active protocol evidence

Round-0001 boundary reminder:
- `round_0001` is `baseline_registration`
- baseline role is `engineering_speed_baseline_candidate`
- it is a starting anchor for later controlled automated tuning
- it is not a promotion statement for method superiority

Formal protocol consistency reminder:
- active protocol remains `formal_posthoc_trainselect_v1`
- `best.pt` winner logic and best-vs-last monitoring remain protocol constraints
- method narrative and protocol narrative must stay aligned

## Relationship To Other Protocol Docs

Scope relationship map:
- `01_project_context.md`: project identity and current stage
- `02_system_architecture.md`: repository and agent responsibilities
- `03_current_mainline.md`: formal method semantics and frozen mainline
- `04_automation_scope.md`: what automation may and may not operate on
- `05_round_protocol.md`: training and round lifecycle
- `06_formal_artifact_map.md`: artifact evidence for mainline-compatible rounds
- `07_tuning_policy.md`: allowed tuning fields and frozen fields
- `08_evaluation_charter.md`: evaluation criteria under this mainline

Reading boundary rule:
- use this document to lock semantic interpretation first
- use downstream protocol documents for lifecycle, artifact, tuning, and evaluation details
- if conflict appears, apply entry-guide authority rules and escalate semantic conflicts for manual review
