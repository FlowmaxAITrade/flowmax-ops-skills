---
name: review-round
description: 复盘单轮决策的完整链路——研究→决策→下单→仓位→平仓。当用户要求「复盘某一轮」「看某个 round 的决策细节」「这个 round 为什么这么操作」时使用。
---

# 复盘单轮决策

重放某一轮（round）决策的完整链路并评估结果。

## 前置

依赖 MCP server **`flowmax-ops`**。工具用全限定名 `mcp__flowmax-ops__<tool>`。

## 步骤

1. 调 `mcp__flowmax-ops__get_round`，必填 `pm_id` 和 `round_id`。

2. 从返回里拆解：
   - `decision`：原始决策内容（下单指令、仓位动作、置信度、风控区块）
   - `events`：事件时间线（ROUND_STARTED → RESEARCH_* → DECISION_FINALIZED → ROUND_COMPLETED/FAILED），判断卡在哪一步
   - `orders` / `positions` / `equity`：该轮实际执行的订单、仓位变化、权益快照

## 输出（Markdown 复盘报告）

- **基本信息**：交易员、round_id、最终状态（success/degraded_success/failed/running/unknown）
- **决策内容**：打算做什么（标的、方向、仓位、杠杆），为什么（reasoning 摘要）
- **执行过程**：按时间线列出关键事件
- **实际结果**：订单是否成交、仓位变化、权益变化
- **复盘结论**：决策与结果是否一致；若是失败/降级，定位失败阶段与原因

涉及金额/杠杆等数值要准确引用原文，不要估算。
