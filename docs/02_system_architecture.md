# 02 System Architecture

## Purpose

This document defines the repository-level and agent-level architecture contract for the automated tuning exchange system.

It must be read after:
- `docs/00_gpt_entry_guide.md`
- `docs/01_project_context.md`

It defines ownership boundaries for:
- training execution
- automation control
- exchange publication
- decision review
- bounded code and document modification

It does not define full round state transitions, full artifact schema details, full tuning policy tables, or full decision output schema. Those details are defined in later numbered protocol documents.

## Architecture Overview

The system has five primary roles:
1. `DRL-path-finding`
2. `DRL_automatic`
3. `RRL_test`
4. Web GPT
5. Codex

Role summary:
- `DRL-path-finding` executes training.
- `DRL_automatic` controls and orchestrates automated tuning.
- `RRL_test` publishes exchange protocol and round evidence.
- Web GPT reviews evidence and produces decisions.
- Codex performs bounded code or document modifications only when explicitly instructed.

System posture:
- This is a controlled semi-automated tuning loop.
- This is not a fully autonomous AutoML system.
- Human review remains mandatory for protocol changes, reward semantics, network architecture, state tensor schema, and formal protocol changes.

Current context constraints:
- project stage is `automated_tuning_system_design`
- active round is `round_0001` in `baseline_registration`
- baseline role is `engineering_speed_baseline_candidate`
- `decision_required_from_gpt` is currently false

This architecture must preserve those constraints while enabling later controlled iteration.

## Repository Responsibilities

### DRL-path-finding: Training Execution Repository

Primary responsibilities:
- receive training configuration from automation control
- execute formal training runs
- save checkpoints
- emit train-side statistics
- emit final probe results when required by formal protocol
- preserve current formal method semantics unless manual review approves migration

Required behavior:
- must execute only the configuration and protocol invoked by controller context
- must emit artifacts that can be parsed and registered by automation and exchange layers
- must maintain traceability between run configuration and emitted artifacts

Prohibited behavior:
- must not decide the next hyperparameter search direction
- must not publish exchange decisions
- must not silently change reward semantics, core network architecture, state tensor schema, or formal protocol

Training-repo authority boundary:
- it is the source of training execution facts
- it is not the source of global protocol decisions

### DRL_automatic: Automation Control Repository

Primary responsibilities:
- generate candidate configurations
- enforce allowed tuning fields and frozen fields
- invoke `DRL-path-finding`
- collect training artifacts
- parse logs and build machine-readable summaries
- generate artifact digest and related evidence summaries
- publish round bundles into `RRL_test`
- read GPT decision or controller action after review
- decide whether to run the next round only according to protocol rules

Required behavior:
- must enforce comparability constraints before launching a run
- must enforce frozen-field constraints before launching a run
- must track run identity, round identity, and publication identity consistently
- must preserve failure modes and missing-evidence states explicitly

Prohibited behavior:
- must not bypass comparability guard
- must not treat failed or incomplete runs as formal winners
- must not mix incomparable experiments into the same comparability group
- must not use stale outbox files
- must not modify training method semantics without manual review

Program status note:
- `DRL_automatic` is the next-stage primary review target.
- Its interfaces and guards must be validated in dry-run mode before real automated training is resumed.

### RRL_test: Public Exchange and Protocol Repository

Primary responsibilities:
- store long-term protocol documents
- store `CURRENT_ROUND.json`
- store round bundles under `rounds/round_xxxx/`
- store structured summaries, artifact digests, comparability reports, config diffs, and GPT decisions
- provide fresh GPT sessions with stable, reviewable context

Required behavior:
- must preserve clear distinction between protocol files and round fact files
- must preserve active-round pointer integrity
- must preserve claim boundaries in published summaries and decisions

Prohibited behavior:
- must not store checkpoint binaries
- must not be treated as the training execution repository
- must not contain full raw outputs directories
- must not use global outbox as formal decision source

Exchange-repo role boundary:
- it is a public protocol and evidence exchange layer
- it is not a training artifact warehouse

## Agent Responsibilities

### Web GPT: Decision Review Agent

Primary responsibilities:
- read `CURRENT_ROUND.json`
- read numbered docs and protocol constraints
- read active round evidence
- judge comparability posture
- detect missing evidence and missingness states
- produce `gpt_decision.json` only when required or explicitly requested
- generate Codex instructions when bounded modifications are needed
- maintain claim boundaries in all outputs

Required behavior:
- must align decisions with active round and protocol scope
- must classify uncertainty explicitly when evidence is missing or incomparable
- should request manual review when boundaries are crossed or ambiguous

Prohibited behavior:
- must not directly launch training
- must not directly modify code
- must not invent artifact values
- must not override formal protocol
- must not treat synthetic rehearsal as formal evidence

Decision-role boundary:
- Web GPT produces review and decision artifacts
- it does not execute training or controller operations directly

### Codex: Bounded Code Modification Agent

Primary responsibilities:
- execute explicit file, document, or code modifications
- report changed files clearly
- run local verification checks when requested
- avoid changing forbidden files
- avoid expanding task scope beyond instructions

Required behavior:
- must respect repository and file-level boundaries defined by the request
- must preserve existing protocol constraints unless explicitly asked to revise protocol docs
- should surface ambiguity before touching out-of-scope components

Prohibited behavior:
- must not start training unless explicitly instructed
- must not modify reward semantics, architecture, state tensor schema, or formal protocol unless explicitly approved
- must not upload checkpoint binaries
- must not silently delete round evidence
- must not change experiment scope by interpretation

Execution boundary:
- Codex is an implementation agent under explicit constraints
- Codex is not a free controller for experiment design

## Control Flow

Architecture-level control flow:
1. `DRL_automatic` proposes a config within allowed tuning fields.
2. `DRL_automatic` performs pre-run comparability and frozen-field checks.
3. `DRL_automatic` calls `DRL-path-finding` to execute training.
4. `DRL-path-finding` emits checkpoints, train-side statistics, and final probe artifacts according to `formal_posthoc_trainselect_v1`.
5. `DRL_automatic` collects and summarizes artifacts.
6. `DRL_automatic` publishes a round bundle to `RRL_test`.
7. Web GPT reads `RRL_test` docs and active round evidence.
8. Web GPT produces a decision or requests manual review when required.
9. `DRL_automatic` consumes the decision and either proposes the next round, pauses, stops, or requests Codex changes.

Scope boundary:
- Full round lifecycle details and state transitions are defined later in `docs/05_round_protocol.md`.

## Data Flow And Artifact Boundaries

Allowed data flow across components:
- config proposal
- config diff
- training logs
- train-side statistics
- final probe results
- checkpoint metadata and hashes
- artifact digest
- comparability report
- round summary
- gpt decision
- controller action

Restricted or prohibited flow:
- checkpoint binaries must not be copied to `RRL_test`
- full raw outputs directories should not be copied to `RRL_test`
- local absolute paths are execution context only
- public truth identity should use repository names and structured round metadata
- backup directories are reference-only

Data hygiene requirements:
- missing artifacts must be represented explicitly, not hidden
- copied files and skipped files must be traceable in structured summaries
- round facts must remain machine-readable and reviewable

## Authority Boundaries

Authority map:
- `CURRENT_ROUND.json` is pointer authority
- `docs/` are protocol authority
- `rounds/<current_round>/` structured JSON is active round fact authority
- `DRL-path-finding` outputs are training fact source
- `DRL_automatic` logs and configs are automation fact source
- Web GPT decisions become decision artifacts only after they are written to the round decision file

Conflict handling boundary:
- If conflict occurs, follow evidence-authority rules defined in `docs/00_gpt_entry_guide.md`.

Interpretation discipline:
- protocol authority defines how to interpret and constrain
- active round fact authority defines what happened in the active round
- pointer authority defines which round is current

## DRL_automatic Review Targets

The next architecture-critical review must inspect these `DRL_automatic` interfaces and guards:
- entry script and CLI/API interface
- config generation logic
- allowed tuning fields and frozen-field enforcement
- comparability group assignment
- pre-run validation gates
- training invocation behavior
- artifact discovery and parsing
- missing artifact handling
- checkpoint handling and hash registration
- round directory generation
- `CURRENT_ROUND.json` update behavior
- GPT decision consumption
- rollback and failed-run handling
- dry-run and no-train mode behavior

Review objective:
- verify controller correctness before scaling automated rounds
- ensure protocol guardrails are enforceable by code path, not only by documentation

Mandatory sequencing:
- dry-run and no-train validation should be performed before launching real automated training.

## Non-Goals

This architecture document must not be used to justify:
- free architecture search
- automatic reward redesign
- automatic method semantic migration
- uncontrolled runtime toggle exploration
- uploading model checkpoints to exchange repo
- treating `RRL_test` as the training output store

Additional non-goal boundary:
- this document does not claim that `round_0001` demonstrates method-performance superiority
- this document does not authorize controller-side bypass of manual review for semantic changes

## Relationship To Other Protocol Docs

Cross-document boundary map:
- `01_project_context.md`: project identity and current stage
- `02_system_architecture.md`: repository and agent architecture
- `04_automation_scope.md`: allowed operating modes and automation boundaries
- `05_round_protocol.md`: detailed round lifecycle and state transitions
- `06_formal_artifact_map.md`: artifact roles and evidence requirements
- `07_tuning_policy.md`: allowed and frozen tuning fields
- `10_output_contract.md`: GPT decision and controller action schema

Reading rule:
- use this architecture document to fix ownership boundaries first
- then use downstream docs to execute detailed protocol decisions
