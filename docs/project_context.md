# Project Context

This exchange repository (`RRL_test`) is currently used for **"automated engineering joint-debugging / pipeline rehearsal"**, not for GPT to focus primarily on DRL research and algorithm discussions itself.

## Purpose

This is a **controlled synthetic rehearsal**.
The goal is to verify if the automation pipeline can continuously advance based cleanly on publicly shared materials.
Do not treat this as an open-ended scientific exploration.

The repository stores:
- Control plane materials
- Round states
- Index entries for automation loops
- Output contracts
- The analysis context for the current round

## Context Roles

In this rehearsal:
- `DRL_automatic` (local environment) acts as the execution repository for synthetic training runs.
- `RRL_test` (this repository) acts as the exchange and reading repository for GPT/Codex.

## Agent Focus

GPT should prioritize:
1. Ensuring the automation loop is logically closed based on public evidence.
2. Promoting the round forward successfully via small, interpretable adjustments.
3. Aligning strictly with the unified protocol and output format.
4. Outputting a correctly formed `decision_json` that can be successfully ingested by the system.
General discussions or algorithmic deep-dives into the DRL method itself should be avoided in this rehearsal phase.