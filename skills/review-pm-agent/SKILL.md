---
name: review-pm-agent
description: 复盘单个 PM agent 的整体表现——收益、胜率、决策风格、最近动作。当用户要求「复盘某个 PM agent」「某某最近表现如何」「分析某个基金经理」时使用。
---

# 复盘 PM agent

复盘一个 PM agent 在某段时间内的整体表现。

## 前置

依赖 MCP server **`flowmax-ops`**（`claude mcp add flowmax-ops ...` 已配好）。工具用全限定名 `mcp__flowmax-ops__<tool>` 调用。

## 步骤

1. **确定 PM agent ID**：若用户给的是名字/关键词而非 ID，先调 `mcp__flowmax-ops__list_pm_agents`（传 `q`）找到对应 PM 的 `id`。找不到就如实告知并列出近似结果让用户确认。

2. **拉绩效**：调 `mcp__flowmax-ops__pm_agent_stats`（`account_type`、`period_unit` 按需）。提取该 PM agent 的净盈亏、收益率、胜率、已平仓数。

3. **拉动作流水**（时间窗按用户要求，默认近 7 天）：
   - `mcp__flowmax-ops__list_orders`（`pm_id`）
   - `mcp__flowmax-ops__list_positions`（`pm_id`）
   - `mcp__flowmax-ops__get_equity_curve`（`pm_id`）

4. **拉最近决策**：调 `mcp__flowmax-ops__search_decisions`（`pm_id`）看决策状态分布。

## 输出（Markdown 复盘报告）

- **概览**：ID、名称、交易所、标的、账户类型
- **绩效**：净盈亏 / 收益率 / 胜率 / 已平仓数（数据缺失要说明，别编）
- **决策风格**：决策总数、成功/失败/降级成功分布、最近决策时间
- **权益曲线**：起止与大致走势（从 get_equity_curve 快照判断）
- **风险点**：大额亏损仓位、连续失败轮次、集中持仓标的

数据为空的字段写「无数据」，不要臆造数值。
