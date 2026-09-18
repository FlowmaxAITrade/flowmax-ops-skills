---
name: review-period
description: 复盘某时间段所有交易员的全局表现——谁决策最多、成功率、标的分布、各 agent 的交易额/手续费/回撤、异常轮次。当用户要求「复盘本周/最近 N 天/某段时间」「全局复盘」「整体表现怎么样」「各 agent 交易额/手续费/回撤」时使用。
---

# 周期全局复盘

复盘某时间段内所有 PM 交易员的整体表现。

## 前置

依赖 MCP server **`timon`**。工具用全限定名 `mcp__timon__<tool>`。

## 步骤

1. **确定时间窗**：把用户的时间表述转成 RFC3339 的 `start` / `end`（默认近 7 天）。

2. **拉全局决策**：调 `mcp__timon__search_decisions`（传 `start`/`end`，不传 `pm_id`）。可能需要翻页（`page`）取全。

3. **拉目录**：调 `mcp__timon__list_pm_agents` 拿到交易员名单（名称/交易所/标的）。

4. **拉绩效**：调 `mcp__timon__pm_agent_stats`（`period_unit` 按时间窗长度选 day/week/month）。每个 agent 除净盈亏/收益率/胜率外，还含 `trading_volume`（交易额）、`total_fee`（手续费）、`max_drawdown`（最大回撤额）、`max_drawdown_pct`（最大回撤率）。

## 输出（Markdown 复盘报告）

- **整体画像**：决策总数、created/skipped/execution_failed 分布、成功率（created 占比）
- **活跃度排行**：各交易员决策次数排序
- **绩效排行**：净盈亏 / 收益率 / 胜率 / 交易额 / 手续费 / 最大回撤（额与率，从 pm_agent_stats 的排行榜取）
- **标的分布**：哪些 symbol 被频繁操作、集中度
- **异常轮次**：skipped/execution_failed 的轮次清单（round_id + 交易员 + 状态），供后续 `review-round` 深入

标注统计口径（时间窗、账户类型），避免误导。
