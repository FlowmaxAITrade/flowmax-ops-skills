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
   - `message`：决策日志 message（`PM Decision created` / `PM Decision skipped:*` / `Decision execution failed:*`），据此判定结果
   - `orders` / `positions` / `equity`：该轮实际执行的订单、仓位变化、权益快照

## 大响应处理

`get_round` 单轮可能返回较大（完整 decision + orders + positions + equity）。若结果超过 token 上限，会提示 `exceeds maximum allowed tokens` 并存到文件（路径在提示里）。此时**不要整读文件**，用 `jq` 定向取字段：

```bash
F=<提示里给出的文件路径>
jq -r '.status, .message' "$F"                        # 状态 + 判定 message
jq -c '.decision.attrs.structuredDecision' "$F"       # 下单指令 / 仓位动作
jq -c '.decision.attrs.overallReasoning' "$F"         # 决策理由摘要
jq -c '[.orders[] | {symbol,side,status,action_type}]' "$F"
jq -c '[.positions[] | {symbol,side,status,size,net_realized_pnl}]' "$F"
jq -r '.equity[0].equity, .equity[-1].equity' "$F"    # 权益起止
```

只取分析需要的字段，不要整段输出，避免撑爆上下文。

## 输出（Markdown 复盘报告）

- **基本信息**：交易员、round_id、最终状态（created/skipped/execution_failed）
- **决策内容**：打算做什么（标的、方向、仓位、杠杆），为什么（reasoning 摘要）
- **执行过程**：按时间线列出关键事件
- **实际结果**：订单是否成交、仓位变化、权益变化
- **复盘结论**：决策与结果是否一致；若是 skipped / execution_failed，定位原因

涉及金额/杠杆等数值要准确引用原文，不要估算。
