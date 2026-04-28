# Output Contract

Each round bundle under `rounds/round_xxxx/` must include at least:

- `index_manifest.json`
- `round_summary.json`
- `artifact_digest.json`
- `comparability_report.json`
- `config_diff.json`
- `gpt_decision.json` or `gpt_decision_placeholder.json`

Optional round-scoped control files:

- `codex_instruction.md`
- `controller_action.json`

Protocol rule:

- GPT decisions are archived inside the round directory (`rounds/round_xxxx/`), not in a global exchange folder.
- A global `outbox/` directory is not a formal decision source and must not be used as the authoritative protocol channel.
