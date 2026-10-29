# B5 · Validation plan slide(给 D,10-27)

> 用法:标题/要点/脚注可直接贴进 D4 的 PPT;台词约 40 秒。所有数字来自冻结代码:
> `agent/CHANGELOG.md`(dev 集)、`eval/results_sim.csv`(simulator,10 seeds)、
> `eval/heldout_labels.json` + `run_inbox --file`(held-out,regex)。
> 唯一空缺:held-out 集的 **Claude 分数 = A4 单次终跑**,还没跑,先留占位。

## Slide 内容(一页,双栏)

**标题:How we know the desk works — validation plan & results**

**左栏:Inbox · 30 requests,scored 3 ways**

- 分开打分(评分标准就是这么分的):**behaviour** / **decision details** / **explanation faithfulness**
- Dev 集(调参用,证明"能跑"):regex **30/30/30** · Claude **30/30/30**
- Held-out 集(30 条换说法、封存到终跑,证明"会泛化"):regex **30/30/30** · Claude **30/30/30(A4 单次终跑)**
- Faithfulness(B3 收紧):回复里每个数字都要追溯到工具输出、且对上同类事实(日期对 ETA、金额对成本)

**右栏:Simulator · 120 orders replay,10 seeds,normal & shock**

- 3 个朴素 baseline + 简单规则,对比 % late(正常 / 关店冲击):
  - random 34% / 38% · greedy-biggest 84% / 85% · cheapest 92% / 92% · earliest-finish rule **1.7% / 1.9%**
- 我们的策略:
  - lateness(主目标)**1.7% / 1.9%** late —— 追平简单规则,冲击下几乎不退化
  - hybrid:late 20% / 21%,换 defect 4.6%(lateness 是 6.8%)与更低成本
  - fair:单店最大份额压到 **20%**(规则是 26%)
- 冲击实验:第 30–44 天随机关停一家店、不给策略任何提示;10 seeds 取平均,因为一次 seed 只是一次掷骰

**脚注**:behaviour / decision / explanation 分开计分 · dev 证明管道正确、held-out 证明 LLM 不是背题 · 全部数字来自冻结代码(26 Oct 11:59 PM)

## 台词(~40 秒)

> "On the inbox we score three things separately, because the brief grades them
> separately: the right behaviour, the right decision, and a faithful explanation.
> The dev set is where the parser was tuned — both regex and Claude reach 30/30/30 —
> so it only proves the pipeline works. The real test is the held-out set: thirty
> reworded requests the parser has never seen, kept sealed until one final Claude run.
> Regex already scores 30/30/30 there.
> On the simulator we replay all 120 orders through three naive baselines, the simple
> earliest-finish rule, and our objectives, averaged over ten seeds in normal and shock
> conditions. The naive baselines are late 34 to 92 percent of the time. Our lateness
> objective is late 1.7 percent — matching the simple rule — and stays at 1.9 percent
> when a workshop is closed for two weeks without warning. The hybrid trades about 20
> percent lateness for fewer defects and lower cost, and fairness caps any one shop at
> 20 percent of the load. Every number on this slide comes from the frozen code."

## 数字出处与口径

| 数字 | 值 | 来源 | 备注 |
|---|---|---|---|
| Dev regex / Claude | 30/30/30 各 | `agent/CHANGELOG.md` run dev_v2 | behaviour/decision/faithful |
| Held-out regex | 30/30/30 | `python -m eval.run_inbox --file eval/heldout.txt --labels eval/heldout_labels.json` | B2 冻结集,30 条 |
| Held-out Claude | 占位 TBD | A4 单次终跑 | **尚未运行**,跑法:`compare_parsers --file eval/heldout.txt --labels eval/heldout_labels.json --name heldout` |
| random % late | 34.2 / 38.2 | `eval/results_sim.csv` | normal / shock,10 seeds |
| greedy-biggest % late | 84.3 / 84.9 | 同上 | |
| cheapest % late | 91.8 / 91.8 | 同上 | 成本最低但几乎全迟 |
| rule_earliest % late | 1.7 / 1.9 | 同上 | 要打平的简单规则 |
| desk_lateness % late | 1.7 / 1.9 | 同上 | 主目标 |
| desk_hybrid % late / % defect | 19.8 / 20.9 · 4.6 | 同上 | lateness 的 defect 为 6.8 |
| desk_fair max share | 20.1 | 同上 | 规则的 max share 为 26.3 |
