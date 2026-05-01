# Level 2-B Monitor/Post-Run Workflow

## Purpose

- Level 2-B reduces manual information flow for active run tracking and post-run handoff preparation.
- The main paper object remains the main training project, not the automation controller.
- Level 2-B is not automatic public registration.

## Scope

1. Monitor active run status from launch state/record.
2. Detect process completion.
3. Detect artifact readiness.
4. Prepare or trigger one-shot post-run handoff package generation.
5. Prepare decision-agent handoff material for GPT review.

## Forbidden

- no unapproved training launch
- no public round creation
- no CURRENT_ROUND mutation
- no checkpoint publication
- no automatic accepted-reference update

## Relationship to Level 2-A

- Level 2-A: restricted auto-launch for single authorized candidate.
- Level 2-B: restricted auto-launch + active monitor + post-run handoff scaffold.
- Level 2-B still requires human/GPT approval for new axis decisions and accepted round publication.

## Operational Boundary

- Evidence can be updated after explicit post-run analysis completion.
- Round publication remains manual/human-confirmed.
- CURRENT_ROUND remains unchanged unless explicitly approved in a separate task.
