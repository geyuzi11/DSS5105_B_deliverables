# B1 · 30 条金标交叉核对记录

仓库:`https://github.com/kailinghew/DSS5105_Project`(核对时克隆的当前版本)
执行人:B(Evaluation lead) · 待与 A 确认

> **状态更新(10-24):A 已同意 R01 的处理方案。** 已在金标中补上 `matches: 2` 并用评分器复核(仍为 30/30/30)。最终交付物见文末"交付清单"。

## 结论(先看这里)

**30 / 30 条金标全部验证通过:行为、订单号、数量/日期、chosen/排除的 workshop、拒绝原因、matches 数量、impossible / on-time 判定,没有发现事实性错误。** 仓库自带评分器 `python -m eval.run_inbox` 复核结果为 30/30/30,与金标一致。

唯一需要与 A 坐下来确认的是两条"类别边界"判定:

- **R01**:金标归为 `missing`,我盲评归为 `ambiguous` —— 行为都是 `clarify`,不影响打分,但金标的 `expect` 漏检了"有 2 个候选订单"这件事。
- **R29**:金标归为 `ambiguous`(matches=6),我盲评第一反应是 `missing` —— 核对数据后我撤回,金标正确(在制背心订单确实有 6 个)。

## 方法

按 B1 要求,先不看 `eval/gold_labels.json`,只用原始数据自己打标,再打开金标逐条对比:

- 输入:`data/dispatch_requests.txt`(30 条)+ `data/orders.csv` + `data/workshops.csv` + `data/data_dictionary.md`
- 判定口径:`desk/schemas.py` 的 5 种行为 + 数据字典的 7 类;ETA / 可行性用 `desk/tools.py` 的确定性算法(`queue + work + 0.5·work·defect + lead`,四舍五入到天)独立复算
- 未提前读取 `eval/gold_labels.json`(盲评完成后才打开对比)

## 30 条逐条对比

| 请求 | 我的盲评行为 | 金标 kind | 金标 behaviour | 金标 expect 关键字段 | 对比 | 备注 |
|---|---|---|---|---|---|---|
| R01 | clarify | missing | clarify | missing: pieces | ⚠️ kind 分歧 | 数据有 2 个候选订单 ORD-061/ORD-108,见争议 1 |
| R02 | decline | unanswerable | decline | reason: history | ✅ | |
| R03 | clarify | ambiguous | clarify | matches: 6 | ✅ | TrendCart 在制订单 6 个,复算一致 |
| R04 | allocate | complete | allocate | ORD-041, on_time | ✅ | 600 beanies / Apr 21 与 orders.csv 一致;最早 W6,4/8 前 |
| R05 | clarify | missing | clarify | missing: due_date | ✅ | 原文"will confirm the new date" |
| R06 | allocate | complete | allocate | ORD-096, on_time | ✅ | 1000 / Apr 17 一致 |
| R07 | allocate | complete | allocate | ORD-063, on_time | ✅ | 100 / Apr 29 一致 |
| R08 | refuse | ineligible | refuse | refused: W8, reason: cap | ✅ | FreshStart 上限 300,400 超限 |
| R09 | allocate | constraint | allocate | ORD-045, fastest, chosen W6 | ✅ | 复算最快 = W6(4/4 可交,早于 4/9) |
| R10 | clarify | missing | clarify | missing: order | ✅ | "details to follow" |
| R11 | refuse | ineligible | refuse | refused: W7, reason: SUSPENDED | ✅ | OldMill 已停用 |
| R12 | allocate | constraint | allocate | ORD-053, exclude W1, not_chosen W1, imposed_by Boss | ✅ | 排除 QuickStitch 后最早 W6,4/9 ≤ 4/10 |
| R13 | decline | unanswerable | decline | reason: price | ✅ | 数据里没有报价历史 |
| R14 | refuse | ineligible | refuse | refused: W1, reason: ACCESSORIES | ✅ | QuickStitch 只做 TOPS;另见第 6 节 A3 提醒 |
| R15 | clarify | ambiguous | clarify | (空) | ✅ | Loom & Leaf 在制订单 6 个,复算一致 |
| R16 | allocate | complete | allocate | ORD-024, on_time | ✅ | 300 vests / Apr 15 一致 |
| R17 | allocate | complete | allocate | ORD-017, on_time | ✅ | 800 / Apr 10 一致;W1 4/9 可交 |
| R18 | decline | unanswerable | decline | reason: unknown_order | ✅ | ORD-999 不在订单表 |
| R19 | clarify | ambiguous | clarify | (空) | ✅ | 无法定位"big reorder",问即可 |
| R20 | impossible | impossible | impossible | ORD-103 | ✅ | 2000 scarves,最早 4/17,赶不上 4/4 |
| R21 | allocate | constraint | allocate | ORD-081, cheapest_on_time, chosen W5 | ✅ | 按时可选 W1(1.5) / W5(1.1),最便宜=W5 |
| R22 | allocate | complete | allocate | ORD-040, on_time | ✅ | 1200 / Apr 21 一致 |
| R23 | allocate | constraint | allocate | ORD-005, split_allowed | ✅ | 单店 W6 已 4/7 交,拆分并不更快 |
| R24 | refuse | ineligible | refuse | refused: W4, reason: TOPS | ✅ | Little Loom 只做 ACCESSORIES |
| R25 | allocate | constraint | allocate | ORD-109, exclude W3, not_chosen W3, imposed_by Mei | ✅ | 排除 BudgetWorks 后最早 W6 |
| R26 | allocate | complete | allocate | ORD-010, on_time | ✅ | 1000 / Apr 25 一致 |
| R27 | allocate | constraint | allocate | ORD-095, lowest_defect_on_time, chosen W2 | ✅ | W2 缺陷率 0.03 且按时 |
| R28 | impossible | impossible | impossible | ORD-002 | ✅ | due 3/29 已逾期,最早 4/10 |
| R29 | clarify | ambiguous | clarify | matches: 6 | ✅(我盲评第一反应是 missing,复核后同意金标) | 在制背心 6 个,复算一致 |
| R30 | impossible | impossible | impossible | ORD-099 | ✅ | 1500 polo,最早 4/11,赶不上 4/5 |

## 争议清单(需与 A 确认)

### 争议 1 · R01 的类别:missing 还是 ambiguous?(✅ 已解决,10-24)

原文:`Need scarves for Harbor Knits sent out today, usual quantities.`

- 我的盲评:`ambiguous`。理由:数据里有 **2 个** Harbor Knits 在制围巾订单(ORD-061,800 件 / ORD-108,2000 件),系统除了问数量,还必须问"哪一个订单"。
- 金标:`missing`,expect 只查 `missing: pieces`。
- 行为层面双方一致(clarify),**不影响任何分数**。
- 建议处理:**保留金标不变**(“usual quantities”= 数量未知,归 missing 合理),但把 expect 收紧为 `{"missing": "pieces", "matches": 2}`,让决策细节检查覆盖"必须问清是哪一单"。
- **A 已同意该方案。** 最终改动:`"R01": {"kind": "missing", "behaviour": "clarify", "expect": {"missing": "pieces", "matches": 2}}`。改动后 `python -m eval.run_inbox` 复核仍为 30/30/30。可直接应用的补丁:`B1_gold_labels_R01.patch`。

### 争议 2 · R29(已解决,无需改动)

原文:`The vests need to go out this week — who takes them?`

- 我盲评第一反应是 `missing`(没有订单号)。
- 复核数据:在制 Vest 订单有 6 个(ORD-120/020/109/045/015/024),属于"多个订单匹配"→ `ambiguous`。
- **金标正确,我撤回我的盲评。** 已在对比表中记为 ✅。

## 逐字段复算结果(全部通过)

以下是我用 `desk/tools.py` 确定性算术独立复算的关键数值,与金标完全一致:

| 检查项 | 结果 |
|---|---|
| 消息中口头给的数量/日期 vs orders.csv | 17 条含订单号的请求全部一致,无数字冲突 |
| R03 / R15 / R29 的 matches | 6 / 6 / 6 ✓ |
| R01 的候选订单 | ORD-061、ORD-108(2 个)✓ |
| R09 fastest | W6,ETA 4/4 ✓ |
| R21 cheapest_on_time | W5(1.1/pc,4/12 ≤ 4/13)✓ |
| R27 lowest_defect_on_time | W2(0.03,4/10 ≤ 4/26)✓ |
| R12 / R25 排除后选择 | 均 W6,且 on_time ✓ |
| R08 / R11 / R14 / R24 拒绝原因 | cap / SUSPENDED / ACCESSORIES / TOPS ✓ |
| R20 / R28 / R30 impossible | 最早 ETA 分别 4/17、4/10、4/11,均晚于 due ✓ |
| 类别计数 | complete 7 + impossible 3 + constraint 6 + ambiguous 4 + missing 3 + ineligible 4 + unanswerable 3 = 30 ✓ |

## 顺手发现的"可收紧项"(不影响结论,供 B 自己决定)

1. **R01** expect 漏了 `matches: 2`(见争议 1,建议补)。
2. **R15** 的 expect 是空 `{}`,可以补 `matches: 6`,与 R03/R29 保持同一严格度。
3. **R09 / R21 / R23 / R27** 没查 `imposed_by`(分别应为 Boss / Chen / Priya / Ravi)。数据字典要求"记录谁提出的约束",R12/R25 查了而这几条没查,属一致性缺口。
4. **complete 类(R04/06/07/16/17/22/26)** 不锁定 chosen workshop,只查 on_time。这可以接受(客观函数可在配置里切换),但记录在案:理论上"随便选一家能按时交货的店"也能通过 decision 检查。
5. **R14 与 A3 的衔接**:R14 目前只验证"拒绝 QuickStitch"。A3 的新要求是"被拒店的替代店也迟到时,回复必须同时说明 ORD-103 本身 impossible"。A3 落地后,建议给 R14 的 expect 补一条(或新增一个测试),把这个双重要求固定下来。

## 给 A 的确认消息(直接发群即可)

> B1 核对完 30 条金标:30/30 全部一致,无数字/订单/选择错误。只有 R01 一条类别判定想和你确认——我盲评是 ambiguous(有 ORD-061/ORD-108 两个候选),金标标的是 missing(usual quantities=数量未知),行为都是 clarify 不影响分数。我建议保留 missing,但 expect 补 `matches: 2`。你 OK 的话我改完 `gold_labels.json` 就提交。

## 交付清单(B1 收尾)

1. **改金标**(A 已同意,补丁已备好):`B1_gold_labels_R01.patch` → 在项目 checkout 里 `git apply B1_gold_labels_R01.patch`,或手动把 R01 的 expect 加上 `"matches": 2`。
2. **复核**:`python -m eval.run_inbox`,确认 TOTAL 仍是 30 / 30 / 30。
3. **提交并推送**:
   ```
   git add eval/gold_labels.json
   git commit -m "B1: tighten R01 gold label with matches: 2 (agreed with A)"
   git push
   ```
4. **记录争议**(B1 要求 "record them"):把"争议 1(R01)已按 missing + matches:2 解决、争议 2(R29)以金标为准"这两条发到小组群或写进 PR/commit 说明。
5. **可选收紧项**(B 自己决定,要做我再生成补丁):R15 的 expect 补 `matches: 6`;R09/R21/R23/R27 补 `imposed_by`;R14 与 A3 的"替代店也迟到"联动检查。

## 交付物对应关系

- 本文件 = B1 的"Agreed labels(核对版)+ list of disputed cases"
- `B1_gold_labels_R01.patch` = A 已同意的金标改动,可直接应用
- 下一步:B2(写 held-out 集)依赖本核对结论,现在可以开始
