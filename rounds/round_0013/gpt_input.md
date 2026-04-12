# GPT Input Package

## 1. Round metadata
- Round id: `round_0013`
- Round state status: `success`
- Run directory: `outputs/sched_turn003_revisit014_entry8_20260412_103324`
- Training return code: `0`
- Bridge status: `not_invoked`

## 2. Previous decision summary
- Decision status: `run_next_round`
- Target program: `fake_train.py`
- Parameter changes:
| Parameter | Old | New | Delta | Reason |
| --- | --- | --- | --- | --- |
| `revisit_penalty` | `0.1` | `0.14` | `0.04` | Previous round showed no observable change versus the prior successful run. Increase revisit_penalty more decisively while keeping turn_penalty fixed, so the effect is interpretable. |
- Compare target requests:
  - Previous successful run: `outputs/sched_turn003_revisit010_entry8_20260411_214522`
  - Best known reference: `outputs/sched_turn003_revisit010_entry8_20260411_193824`
- Raw compare target tokens from decision:
  - `previous_round_run`
  - `best_known_reference`
- Explicit reference targets:
  - Best known reference: `outputs/sched_turn003_revisit010_entry8_20260411_193824`
  - Manual compare target: UNSET

## 3. Codex request summary
- Required files / focus objects:
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/train_steps.csv`
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/train_episodes.csv`
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/eval_metrics.csv`
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/final_probe.csv`
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/reward_curve.png`
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/coverage_curve.png`
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/success_rate_curve.png`
  - `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/loss_curve.png`
- Core questions from codex_request.md:
  1. Compared with the previous successful run, did the larger revisit_penalty create a measurable behavioral delta?
  2. Did coverage saturation improve without causing a clear collapse in terminal reward?
  3. Do late-stage curves still show diminishing returns or a new useful regime?
  4. Should the next round keep revisit_penalty in this direction, revert it, or instead adjust turn_penalty?

## 4. Codex report
# Codex Analysis Report

Round: `round_0013`

## 1. 本次检查到的 run 与文件

- Target run: `outputs/sched_turn003_revisit014_entry8_20260412_103324`
- Logs inspected: `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/train_steps.csv`, `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/train_episodes.csv`, `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/eval_metrics.csv`, `outputs/sched_turn003_revisit014_entry8_20260412_103324/logs/final_probe.csv`
- Plots inspected: `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/reward_curve.png`, `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/coverage_curve.png`, `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/success_rate_curve.png`, `outputs/sched_turn003_revisit014_entry8_20260412_103324/plots/loss_curve.png`
- Checkpoints inspected: `outputs/sched_turn003_revisit014_entry8_20260412_103324/checkpoints/best.pt`, `outputs/sched_turn003_revisit014_entry8_20260412_103324/checkpoints/last.pt`

## 2. 关键指标摘要

- Reward: 目标 run 的 `train_steps.csv` 末值为 `110.6150`，最大值为 `120.5220`；`eval_metrics.csv` 末值为 `113.1150`，最大值为 `123.0220`；`final_probe.csv` 的 `reward_mean_5` 为 `114.2713`。
- Coverage: 目标 run 的 `train_steps.csv` 末值为 `0.9399`，`eval_metrics.csv` 末值为 `0.9499`；`final_probe.csv` 的 `coverage_mean_5` 为 `0.9284`。从已检查文件看，coverage 饱和位置与两个对比对象一致。
- Success rate: 目标 run 的 `train_steps.csv` 末值为 `0.9583`，`eval_metrics.csv` 末值为 `0.9733`；`final_probe.csv` 的 `success_rate_mean_5` 为 `0.9284`。
- Loss: 目标 run 的 `train_steps.csv` 末值为 `0.2002`、最小值为 `0.1307`；`eval_metrics.csv` 末值为 `0.1602`。loss 曲线与两个对比对象没有观察到可见差异。

## 3. 与对比对象的差异

- Compare target 1: 相比 `outputs/sched_turn003_revisit010_entry8_20260411_214522`，目标 run 存在可测但负向的稳定偏移。`train_steps.csv` 与 `eval_metrics.csv` 中 reward 全程约低 `2.2`，success rate 全程约低 `0.002`，coverage 与 loss 不变；`train_episodes.csv` 中 `episode_reward` 也全程约低 `2.2`，而 `episode_length`、`coverage`、`success` 不变；`final_probe.csv` 中 `reward_mean_5` 低 `2.2`，`success_rate_mean_5` 低约 `0.002`，`coverage_mean_5` 不变。
- Compare target 2: 相比 `outputs/sched_turn003_revisit010_entry8_20260411_193824`，结论与 compare target 1 相同。依据当前工作区文件，这个 best known reference 与 previous successful run 在关键 CSV/plot 上表现一致，因此两个对比对象对本轮的判断方向完全一致。

## 4. 曲线 / CSV 现象

- train_steps.csv: 目标 run 的 reward 与 success rate 相比两个对比对象全程小幅下移，coverage 与 loss 逐行保持一致，说明行为差异是可测的，但没有换来 coverage 增益。
- train_episodes.csv: 共 24 个 episode。`episode_reward` 从首轮到末轮都比两个对比对象低约 `2.2`；`episode_length`、`coverage`、`success` 轨迹一致，说明更大的 `revisit_penalty` 没有改变 episode 长度或覆盖终态，只是压低了回报。
- eval_metrics.csv: `eval_reward` 全程低约 `2.2`，`eval_success_rate` 全程低约 `0.002`，`eval_coverage` 与 `eval_loss` 一致。末段没有出现“先吃亏后受益”的反转。
- final_probe.csv: 末端探针同样表现为 reward 与 success 轻微变差、coverage 不变，因此目标 run 没有表现出更好的终态饱和。
- reward_curve.png: 与两个对比对象不同，差异区域覆盖主曲线区间，表现为整体下移，而不是后段出现新的抬升区间。
- coverage_curve.png: 与两个对比对象字节级一致，说明 coverage 饱和过程没有观察到任何改善。
- success_rate_curve.png: 与两个对比对象存在轻微差异，方向与 CSV 一致，为小幅下移，没有出现新的有利 regime。
- loss_curve.png: 与两个对比对象字节级一致，说明优化/收敛形态没有变化，当前差异不是由 loss 曲线新阶段驱动。

## 5. 对请求问题的逐项回答

1. Compared with the previous successful run, did the larger revisit_penalty create a measurable behavioral delta?
是。差异是可测的，而且在多个文件里方向一致：reward 大约全程低 `2.2`，success rate 大约全程低 `0.002`，coverage 与 loss 不变。所以“更大的 `revisit_penalty` 产生了行为变化”这一点可以确认，但该变化不是正向改进。
2. Did coverage saturation improve without causing a clear collapse in terminal reward?
没有。coverage saturation 没有改善，`coverage_curve.png` 与基线一致，`final_probe.csv` 的 `coverage_mean_5` 也没有提升。terminal reward 没有发生断崖式崩塌，但存在清晰回落，例如 `eval_reward` 末值从 `115.3150` 降到 `113.1150`，`reward_mean_5` 从 `116.4713` 降到 `114.2713`。
3. Do late-stage curves still show diminishing returns or a new useful regime?
仍然是 diminishing returns，没有看到新的有用 regime。后段 coverage/loss 与基线相同，reward/success 只是维持小幅更差的平行下移，没有出现后程补偿或新平台。
4. Should the next round keep revisit_penalty in this direction, revert it, or instead adjust turn_penalty?
建议不要继续沿这个方向上调 `revisit_penalty`。更合理的是先回退到上一个成功值 `0.10`，如果下一轮还要寻找新信号，再优先调整 `turn_penalty`。原因是这次单变量实验已经足够说明：`revisit_penalty: 0.10 -> 0.14` 带来了可测负向代价，但没有带来任何 coverage 收益。

## 6. 简短结论

- Recommended next step: 将 `revisit_penalty` 回退到 `0.10`，不要继续按当前方向加大；若下一轮需要新的可解释实验，优先固定 `revisit_penalty` 并改动 `turn_penalty`。
- Confidence / caveat: 对相对比较的信心较高，因为两个对比对象给出的结论一致，且 reward/success 的负向偏移在 logs、plots、final probe 中重复出现。保留的 caveat 是：仅依据 request 指定文件可以确认“结果差异”，但无法进一步证明其更深层机制。

## 5. What GPT should decide next
- Decide whether to continue to another round or stop.
- If continuing, specify which parameters should change, in what direction, and by approximately how much.
- Decide whether the next round needs updated Codex analysis focus or different required logs / plots.
- Decide whether compare targets should be updated, including any explicit best-known reference.

## 6. Output contract
- Produce the next full `gpt_decision.json` content for a future round in this repository.
- Include the next round decision_status, target_program, run_args, parameter_changes, codex_analysis_focus, reference_targets, and controller_notes.
- Explicitly state the next round Codex analysis focus and compare targets.
- This GPT input package was generated from `automation_rounds/round_0013/gpt_input.md`.