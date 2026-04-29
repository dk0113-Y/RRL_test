# Round 0002 Summary: Accepted epsilon_decay320k Formal Tuning Result

## Registration Scope

- round_id: `round_0002`
- round_type: `formal_train_result`
- public registration label: `accepted schedule-level formal tuning result`
- parent/baseline round: `round_0001`
- run_name: `value_bcchild_gated_statequery_effopt_formal_500k_decay320k_end005_20260429_122557`
- formal protocol: `formal_posthoc_trainselect_v1`

## Registration Boundary

round_0002 registers a first formal tuning result on epsilon schedule axis only.

Accepted statement:

> epsilon_decay320k is an accepted schedule-level tuning candidate that improves final-probe peak performance and reduces late-stage winner-vs-last drift relative to the round_0001 baseline.

Not allowed statement classes:

- method performance improvement due to architectural changes
- new network design improvement
- reward redesign improvement
- state schema improvement

## Core Metrics (Public-Safe)

- baseline 480k winner: reward `131.472458`, coverage `0.914549`, success `0.730000`, length `397.660004`, RVR `0.242042`
- baseline 500k last: reward `117.888260`, coverage `0.879560`, success `0.570000`, length `435.869995`, RVR `0.310634`
- decay360k winner: reward `125.676727`, coverage `0.901223`, success `0.640000`, length `428.010010`, RVR `0.280817` (diagnostic only, not accepted)
- decay320k winner (480k): reward `141.042191`, coverage `0.925500`, success `0.800000`, length `388.320007`, RVR `0.184088`
- decay320k last (500k): reward `136.166809`, coverage `0.912461`, success `0.740000`, length `398.519989`, RVR `0.187611`

Key deltas:

- decay320k winner - baseline winner: reward `+9.569733`, coverage `+0.010951`, success `+0.070000`, length `-9.339996`, RVR `-0.057955`
- decay320k last - decay320k winner: reward `-4.875381`, coverage `-0.013039`, success `-0.060000`, length `+10.199982`, RVR `+0.003523`
- baseline last - baseline winner: reward `-13.584198`, coverage `-0.034989`, success `-0.160000`, length `+38.209991`, RVR `+0.068592`

## Key Judgments

- run status: `completed`
- comparability: `comparable`
- only effective tuning axis confirmed: `yes`
- accepted tuning candidate: `yes`
- proposal criteria assessment: `success`
- drift improved vs baseline: `yes`
- peak performance recovered vs decay360k: `yes`
- drift vs decay360k: `worse`

## Residual Risks And Limits

- decay320k final-probe winner is 480k, while last is 500k.
- decay320k still has nonzero winner-vs-last drift, although substantially smaller than baseline.
- posthoc train-side winner does not equal final-probe winner.
- final-probe winner is train-side rank 3, not top-2.
- posthoc_final_probe_topk=3 remains necessary; do not simplify to top-1 or top-2.
- this registration does not prove architecture-level method improvement.
- checkpoint binaries are intentionally excluded from public repository.
