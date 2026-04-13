# Reading Order

For a fresh GPT chat, use this minimum order:

1. `CURRENT_ROUND.json`
2. `docs/project_context.md`
3. `docs/current_mainline.md`
4. `docs/evaluation_charter.md`
5. `docs/stopping_policy.md`
6. `docs/output_contract.md`
7. `rounds/<current_round>/index_manifest.json`
8. `rounds/<current_round>/comparability_report.json`
9. `rounds/<current_round>/round_summary.json`
10. `rounds/<current_round>/metric_snapshot.json`
11. `rounds/<current_round>/benchmark_summary.json`
12. `rounds/<current_round>/config_snapshot.json`
13. `rounds/<current_round>/historical_baseline_summary.json`
14. `rounds/<current_round>/artifact_index.json`

Only after that should GPT draft `next_gpt_decision.json`.
