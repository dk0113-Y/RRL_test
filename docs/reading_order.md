# Reading Order

Use this order to avoid reading verdict text before you know which files are authoritative.

## Long-Term Docs Layer

1. `CURRENT_ROUND.json`
   - First decide whether the exchange repo is in clean waiting state or points to an active round.
2. `docs/gpt_index_guide.md`
   - Build the file-role map before reading any summary or output contract.
3. `docs/project_context.md`
   - Lock project goal, source-of-truth identity, and exchange responsibility.
4. `docs/current_mainline.md`
   - Lock the current formal semantics before reading round evidence.
5. `docs/evaluation_charter.md`
   - Lock the judgement criteria.
6. `docs/stopping_policy.md`
   - Lock the decision-action rules.
7. `docs/output_contract.md`
   - Lock the output format only after the evidence model is clear.
8. `docs/formal_artifact_map.md`
   - Confirm which artifacts are formal evidence and how truth-repo identity is expressed.
9. `docs/automation_scope.md`
   - Confirm the formal vs rehearsal boundary.
10. `docs/tuning_policy.md`
   - Confirm what is tunable and what breaks comparability.

If `exchange_state = awaiting_new_round_publish` or `current_round_id = null`, stop here and stay on docs-only context.

## Current Round Evidence Layer

11. `rounds/<current_round>/index_manifest.json`
   - Confirm the round bundle contents before reading any verdict.
12. `rounds/<current_round>/comparability_report.json`
   - Read the comparability gate before discussing improvement or regression.
13. `rounds/<current_round>/metric_snapshot.json`
   - Read the actual metric packet.
14. `rounds/<current_round>/config_snapshot.json`
   - Read the observed run contract and comparability-relevant config fields.
15. `rounds/<current_round>/benchmark_summary.json`
   - Add runtime and timing support evidence.
16. `rounds/<current_round>/historical_baseline_summary.json`
   - Add historical calibration context when present.
17. `rounds/<current_round>/artifact_index.json`
   - Verify evidence completeness before trusting any synthesis.
18. `rounds/<current_round>/round_summary.json`
   - Use only as structured synthesis after the lower layers are read.
19. `rounds/<current_round>/codex_report.md`
   - Use as auxiliary text, not as the formal fact layer.
20. `rounds/<current_round>/gpt_input.md`
   - Use as packaged assistant input, not as the only fact source.

Only after that should GPT draft `next_gpt_decision.json`.
