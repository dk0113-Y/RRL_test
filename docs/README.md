# RRL_test Protocol Documents

## Numbered Protocol Set

- `00_gpt_entry_guide.md` - entry routing, evidence authority, reading order
- `01_project_context.md` - project identity, current stage, baseline context
- `02_system_architecture.md` - repository and agent architecture
- `03_current_mainline.md` - formal method semantics and frozen mainline
- `04_automation_scope.md` - automation operating scope and modes
- `05_round_protocol.md` - round lifecycle and state transitions
- `06_formal_artifact_map.md` - artifact roles and publication policy
- `07_tuning_policy.md` - allowed, frozen, and manual-review tuning fields
- `08_evaluation_charter.md` - metric interpretation and evaluation verdicts
- `09_stopping_policy.md` - verdict-to-action policy
- `10_output_contract.md` - machine-readable decision and controller-output contract

## Legacy Documents

Legacy unnumbered protocol documents were removed. The numbered `00` to `10` set is the current authoritative protocol set.

## Current Status

Current active baseline is `round_0001`. Its type is `baseline_registration`, its role is `engineering_speed_baseline_candidate`, and it must not be treated as method-performance improvement evidence.

## Next Expected Work

Next expected work is to review `DRL_automatic` control flow and validate the dry-run / no-train path before any real automated training is launched.
