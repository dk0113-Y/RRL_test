# Codex Analysis Report

Round: `round_0015`

## 1. 本次检查到的 run 与文件

- Target run: `outputs/sched_turn002_revisit010_entry8_20260412_125100`
- Logs inspected: `outputs/sched_turn002_revisit010_entry8_20260412_125100/logs/train_steps.csv`, `outputs/sched_turn002_revisit010_entry8_20260412_125100/logs/train_episodes.csv`, `outputs/sched_turn002_revisit010_entry8_20260412_125100/logs/eval_metrics.csv`, `outputs/sched_turn002_revisit010_entry8_20260412_125100/logs/final_probe.csv`
- Plots inspected: `outputs/sched_turn002_revisit010_entry8_20260412_125100/plots/reward_curve.png`, `outputs/sched_turn002_revisit010_entry8_20260412_125100/plots/coverage_curve.png`, `outputs/sched_turn002_revisit010_entry8_20260412_125100/plots/success_rate_curve.png`, `outputs/sched_turn002_revisit010_entry8_20260412_125100/plots/loss_curve.png`
- Checkpoints inspected: `outputs/sched_turn002_revisit010_entry8_20260412_125100/checkpoints/best.pt`, `outputs/sched_turn002_revisit010_entry8_20260412_125100/checkpoints/last.pt`

## 2. 关键指标摘要

- Reward: 目标 run 的 `train_steps.csv` 末值为 `113.6150`、最大值为 `123.5220`；`eval_metrics.csv` 末值为 `116.1150`、最大值为 `126.0220`；`final_probe.csv` 的 `reward_mean_5` 为 `117.2713`。相对上一轮 `revisit_penalty=0.14` 的 run，reward 全程恢复并额外上移；相对 best/manual 参考，也有稳定小幅提升。
- Coverage: 目标 run 的 `train_steps.csv` 末值为 `0.9407`，`eval_metrics.csv` 末值为 `0.9507`，`final_probe.csv` 的 `coverage_mean_5` 为 `0.9292`。相对三个对比对象均小幅高 `0.0008`，没有看到 coverage 饱和被推迟。
- Success rate: 目标 run 的 `train_steps.csv` 末值为 `0.9603`，`eval_metrics.csv` 末值为 `0.9753`，`final_probe.csv` 的 `success_rate_mean_5` 为 `0.9304`。相对上一轮 `0.14` run 有恢复；相对 best/manual 参考基本持平。
- Loss: 目标 run 的 `train_steps.csv` 末值为 `0.1982`、最小值为 `0.1287`；`eval_metrics.csv` 末值为 `0.1582`。相对所有对比对象都小幅低 `0.002`，没有出现新的不稳定迹象。

## 3. 与对比对象的差异

- Compare target 1: 相比 `outputs/sched_turn003_revisit014_entry8_20260412_103324`，目标 run 呈现明确恢复。`train_steps.csv` 与 `eval_metrics.csv` 中 reward 全程高 `3.0`，coverage 全程高 `0.0008`，loss 全程低 `0.002`；success rate 在训练端大多高 `0.002`、在评估端全程高 `0.002`。`train_episodes.csv` 的 `episode_reward` 全程高 `3.0`，但 `episode_length`、`coverage`、`success` 轨迹不变；`final_probe.csv` 中 `reward_mean_5` 高 `3.0`、`success_rate_mean_5` 高 `0.002`、`coverage_mean_5` 高 `0.0008`。
- Compare target 2: 相比 `outputs/sched_turn003_revisit010_entry8_20260411_214522` 以及 `outputs/sched_turn003_revisit010_entry8_20260411_193824`，目标 run 仍有稳定但更小的正向偏移。`train_steps.csv` / `eval_metrics.csv` 的 reward 全程高 `0.8`，coverage 全程高 `0.0008`，loss 全程低 `0.002`，success rate 与这两个参考保持一致；`train_episodes.csv` 的 `episode_reward` 全程高 `0.8`，但 `episode_length` 不变；`final_probe.csv` 的 `reward_mean_5` 高 `0.8`、`coverage_mean_5` 高 `0.0008`，`success_rate_mean_5` 持平。manual compare target 与 best known reference 在本次检查范围内表现一致，因此结论一致。

## 4. 曲线 / CSV 现象

- train_steps.csv: 相对上一轮 `0.14` run，reward、coverage、success rate 均有恢复，loss 略降；相对 best/manual，主要表现为 reward 的稳定上移、coverage 的微小上移与更低 loss，success rate 保持同一轨迹。
- train_episodes.csv: `episode_reward` 出现稳定上移，但 `episode_length` 在三个对比关系下都保持一致，说明 lower `turn_penalty` 没有把 episode 拉长；`coverage` 仅表现为微小平移，`success` 轨迹不变。
- eval_metrics.csv: 与训练侧结论一致。相对 `0.14` run，`eval_reward` 和 `eval_success_rate` 明显恢复；相对 best/manual，`eval_reward` 继续稳定高 `0.8`，`eval_coverage` 略高 `0.0008`，`eval_success_rate` 持平，`eval_loss` 略低。
- final_probe.csv: 末端探针验证了同样方向。与上一轮相比，reward gap 与 slight success-rate loss 已恢复；与 best/manual 相比，新增的是小幅 reward/coverage 优势，而不是新的 success 优势。
- reward_curve.png: 与所有对比对象都不同。相对上一轮是明显上移；相对 best/manual 也能看到较小但全程一致的上移，说明不是局部噪声尖峰。
- coverage_curve.png: 与所有对比对象都不同，但差异很小，形态上更像轻微上移而不是更慢饱和，因此没有证据表明 lower `turn_penalty` 延迟 coverage saturation。
- success_rate_curve.png: 与上一轮不同，表现为恢复；与 best/manual 字节级一致，说明这次调 `turn_penalty` 没有带来新的 success 轨迹变化。
- loss_curve.png: 与所有对比对象都不同，方向为整体略低；这支持“lower `turn_penalty` 主要改善 reward level，同时保持训练稳定”的判断。

## 5. 对请求问题的逐项回答

1. Compared with the previous round run (revisit_penalty=0.14), does restoring revisit_penalty to 0.10 and lowering turn_penalty to 0.02 recover the reward gap and slight success-rate loss while keeping coverage and loss unchanged?
是，且恢复幅度清晰。相对 round_0013 的 `0.14` run，reward 在训练、评估和 final probe 中都全程回升 `3.0`，success rate 在评估端全程回升 `0.002`、训练端基本回升 `0.002`；coverage 没有被破坏，反而全程微升 `0.0008`；loss 也没有变差，反而全程低 `0.002`。因此可以认为 reward gap 与 slight success-rate loss 已被恢复，而且 coverage / loss 没有付出代价。
2. Compared with the best known reference (turn_penalty=0.03, revisit_penalty=0.10), does turn_penalty=0.02 improve reward or success without delaying coverage saturation or lengthening episodes?
从当前工作区真实文件看，是的，但提升主要体现在 reward，而不是 success。相对 best known reference 与 manual compare target，reward 在训练、评估和 final probe 中都稳定高 `0.8`，success 基本持平，coverage 反而略高 `0.0008`，`episode_length` 完全不变。因此 `turn_penalty=0.02` 没有延迟 coverage saturation，也没有拉长 episodes。
3. In train_episodes.csv and eval_metrics.csv, is the observed delta mainly a reward-level shift, or does the lower turn_penalty also change episode_length, coverage, or success trajectories?
主要是 reward-level shift。`train_episodes.csv` 里 `episode_reward` 稳定上移，但 `episode_length` 不变，`success` 不变，`coverage` 只有极小的一致性上移；`eval_metrics.csv` 里 `eval_reward` 稳定上移，`eval_success_rate` 对 best/manual 持平、对上一轮 `0.14` run 只是恢复，`eval_coverage` 仅微升 `0.0008`。没有证据表明 lower `turn_penalty` 改变了 episode 长度或 success 轨迹的形态。
4. After this run, does turn_penalty appear to be a meaningful behavioral tuning axis, or should the next clean search move to entry_k instead?
当前证据支持 `turn_penalty` 仍是有意义的调参轴，暂时不必立刻转去 `entry_k`。原因是相对 best/manual 参考，这次 `turn_penalty: 0.03 -> 0.02` 带来了小幅但跨日志/曲线一致的正向 reward 改善，同时没有损伤 success、coverage 或 episode length。效果量不大，但足够说明这不是无效轴；更合理的下一步是继续做一次干净的 `turn_penalty` 邻域验证，再决定是否切换到 `entry_k`。

## 6. 简短结论

- Recommended next step: 保持 `revisit_penalty=0.10`，继续把 `turn_penalty` 作为下一轮唯一搜索轴做邻域验证，例如再做一次小步单变量探测，以确认这次相对 best/manual 的稳定小幅收益是否可重复；在这之前不建议切换到 `entry_k`。
- Confidence / caveat: 对“已恢复 round_0013 的负向偏移”这一结论信心高；对“`turn_penalty` 是有意义的微调轴”这一结论信心中高，因为收益幅度较小，但在 train/eval/final probe 和 plots 中方向一致。仅依据 request 指定文件，无法进一步判断更大幅度下是否仍保持单调收益。
