# Automation Scope

The system is dual-mode, but the default research track is now `formal_train`.

## Modes

- `formal_train`
  - Source of truth is `../代码1`
  - Decisions are based on real artifacts and comparability checks
  - This is the default mode for current research continuity
- `synthetic_rehearsal`
  - Retained only for protocol and bridge validation
  - Synthetic outputs must never be used as formal evidence

## Round Lifecycle

For a formal round:

1. `../代码1` runs real training and emits formal JSON summaries
2. `codex_ui_bridge_demo` builds a round bundle and comparability report
3. The bundle is published here under `rounds/round_xxxx/`
4. GPT reads docs, then `CURRENT_ROUND.json`, then the round manifest and structured files
5. GPT emits one `next_gpt_decision.json` payload

## Boundaries

- Formal promotion, regression, plateau, and stop decisions must be machine-readable.
- If comparability fails, GPT may analyze the round but must not accumulate a formal improvement claim.
- If evidence is insufficient, GPT must keep that explicit in the decision payload instead of inventing certainty.
