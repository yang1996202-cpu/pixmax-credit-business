# PIXMAX Credit Business - 积分消耗分析（业务/催单视角）

一个 WorkBuddy / Claude Code Skill，把客户导出的 PIXMAX 积分流水 CSV，一键变成**给自己（PIXMAX 侧）看的客户经营表**：回答「这客户什么时候该催单、值不值得加售、有没有流失风险」。

> 发给客户看的版本用姊妹仓库 [pixmax-credit-client](https://github.com/yang1996202-cpu/pixmax-credit-client)。**这个视角的表不要发给客户。**

## 它能做什么

**输入：** PIXMAX 后台导出的积分流水 CSV（企业版 25 列 / 团队版 15 列，自动识别）

**输出：** 5 张表的 Excel

| 表 | 内容 |
|---|---|
| 客户经营速览 | 核心结论（含**预计耗尽日**）+ 客户健康度 8 项 + 前期 vs 近期对比 + 建议动作 |
| 消耗节奏与趋势 | 逐日明细，断档日灰底标「⚠ 断档」 |
| 账号结构 | 各子账号消耗 + Top1/Top3 集中度（单点依赖风险） |
| 模型偏好 | 客户爱用什么模型（产品/加售参考） |
| 充值与续费 | 充值明细 + 消耗进度 + 预计耗尽日 |

## 核心指标（口径）

| 指标 | 算法 | 用途 |
|---|---|---|
| **预计耗尽日** | 最后消耗日 + 余额 ÷ 近7天日均 | **催单闹钟** |
| 消耗加速比 | 近期日均 ÷ 前期日均 | >1 扩张（加售机会）；<1 收缩（流失预警） |
| 断档天数 | 区间内无调用日数 | 连续断档 → 停工/转竞品风险 |
| 依赖集中度 | Top1 / Top3 占总消耗比 | >30% 单点依赖风险 |
| 实充是否用尽 | 实充积分已消耗 ÷ 实充总额 | 100% = 催单最有力理由 |

## 用法

```bash
python3 scripts/analyze.py <csv路径> --view business [--out <输出目录>]
```

依赖：Python 3.10+，`openpyxl`（`pip install openpyxl`）。

## 格式兼容

- 企业版 / 团队版自动识别
- 企业版未开子账号（`子账号ID` 全空）：归主账号统计，不崩溃、不出「（未知）」

## 自测

```bash
python3 scripts/analyze.py examples/sample-enterprise.csv --view business --out /tmp/demo
python3 scripts/analyze.py examples/sample-team.csv --view business --out /tmp/demo
```

样例已脱敏（`examples/`），可放心使用。

## License

MIT
