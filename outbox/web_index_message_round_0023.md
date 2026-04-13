# New Formal Train Round: round_0023

A new formal_train exchange bundle is ready.

Repository: https://github.com/dk0113-Y/RRL_test
Branch: main
Manifest: `rounds/round_0023/index_manifest.json`

Required reading order:
1. Read `docs/reading_order.md`
2. Read `docs/project_context.md`
3. Read `docs/current_mainline.md`
4. Read `docs/evaluation_charter.md`
5. Read `docs/stopping_policy.md`
6. Read `docs/output_contract.md`

After the docs, read `CURRENT_ROUND.json`, then `rounds/round_0023/index_manifest.json`, then the structured round files (`metric_snapshot.json`, `benchmark_summary.json`, `config_snapshot.json`, `artifact_index.json`, `historical_baseline_summary.json`, `comparability_report.json`, `round_summary.json`).

Formal judgement rules:
- Treat the real training repository artifacts as the only source of truth.
- Do not reuse rehearsal semantics as evidence for formal improvement.
- Check comparability before claiming improvement, regression, plateau, or stopping.
- If evidence is insufficient or comparability failed, keep that explicit in the next decision JSON.

Return only one JSON payload wrapped between `DECISION_JSON_BEGIN` and `DECISION_JSON_END`.
