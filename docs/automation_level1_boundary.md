# Automation Level 1 Boundary (Public)

After round_0002, this project supports Automation Level 1 only.

## Level 1 Allows

1. automated proposal generation
2. automated preflight generation and validation artifacts
3. automated post-run analysis artifact generation

## Level 1 Requires Human Approval

1. training launch
2. public round registration

## Public Output Guards

1. no gpt_decision*.json or controller_action*.json should appear in public round directories unless explicitly approved
2. checkpoint binaries are excluded from RRL_test

## round_0002 Status

1. accepted schedule-level tuning result uses epsilon_decay_steps=320000
2. epsilon schedule axis is closed for now
3. next public decision should focus on either posthoc selection diagnostics or automation protocol hardening
