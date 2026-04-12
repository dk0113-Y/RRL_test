# Automation Scope

The primary recommended track for automation is currently **Exchange Mode**.

## Understanding a Round Cycle

A single round iteration in the mainline cycle should be understood as follows:

1. Local `round` / `scheduler` / training outputs
2. → `codex_request.md`
3. → Codex analyzes to produce `codex_report.md`
4. → `prepare_gpt_input.py` running
5. → `publish_round_to_exchange.py`
6. → `RRL_test/outbox/web_index_message_round_xxxx.md`
7. → `exchange_web_bridge.py`
8. → `tmp/round_xxxx_gpt_reply.md`
9. → `tmp/next_real_decision_round_xxxx.json`
10. → `ingest_exchange_decision.py`
11. → New round created
12. → `scheduler.py`

## Current Rehearsal Objective

The current objective is **"single round promotion / single round closure validation"**. It is **not** a "fully unattended closed-loop".

- **Operationally Passing:** Execution of the training run, markdown report generation, publishing to the exchange repo, and the bridge cycle creating and ingesting the reply into a new round have been successfully tested in sequence.
- **Pending Validation:** The continuous, unattended triggering of the full cycle remains under integration test. Do not assume the system is running completely hands-off yet.