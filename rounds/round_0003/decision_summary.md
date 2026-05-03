# Decision Summary: round_0003

- previous_round: round_0002
- new_reference_run: value_bcchild_gated_statequery_effopt_formal_500k_decay320k_end005_minreplay8000_rerun
- accepted_axis: replay_warmup_stability
- effective_change: min_replay_size 4000 -> 8000
- decision_label: accepted_reference
- acceptance_basis: proposal_criteria_success
- winner_step: 500000
- last_step: 500000
- winner_equals_last: true
- rvr_length_objective: mixed
- drift_vs_round_0002: improved
- topk3_still_required: yes
- boundary: no training launch, no controller_action, no checkpoint copy
