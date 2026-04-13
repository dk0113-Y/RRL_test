# Automation Scope

The system is dual-mode, but the default research track is now `formal_train`.

## Modes

- `formal_train`
  - Source of truth repo identity is `dk0113-Y/DRL-path-finding`
  - Local execution path is tracked separately as `local_execution_repo_path`
  - Decisions are based on real artifacts and comparability checks
  - This is the default mode for current research continuity
- `synthetic_rehearsal`
  - Retained only for protocol and bridge validation
  - Synthetic outputs must never be used as formal evidence

## Round Lifecycle

For a formal round:

1. `../代码1` runs real training and emits formal JSON summaries
   - Exchange-facing JSON keeps the repo identity in `source_of_truth_repo`
2. `codex_ui_bridge_demo` builds a round bundle and comparability report
3. The bundle is published here under `rounds/round_xxxx/`
4. GPT reads docs, then `CURRENT_ROUND.json`, then the round manifest and structured files
5. GPT emits one `next_gpt_decision.json` payload

If `CURRENT_ROUND.json.exchange_state = awaiting_new_round_publish`, the exchange repo has been intentionally cleared and there is no active round bundle yet.

## Boundaries

- Formal promotion, regression, plateau, and stop decisions must be machine-readable.
- If comparability fails, GPT may analyze the round but must not accumulate a formal improvement claim.
- If evidence is insufficient, GPT must keep that explicit in the decision payload instead of inventing certainty.
