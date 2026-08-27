---
name: ops-overview
description: 输出平台经营大盘——用户数、Agent 数、Credit 消耗/发放、邀请码使用率。当用户要求「看经营数据」「平台有多少用户」「消耗了多少积分」「Credit 情况」时使用。
---

# 经营大盘

输出 Flowmax 平台的经营 KPI 总览。

## 前置

依赖 MCP server **`flowmax-ops`**。工具用全限定名 `mcp__flowmax-ops__<tool>`。

## 步骤

1. 调 `mcp__flowmax-ops__ops_overview`，拿用户/Agent/昨日决策/邀请码使用率。

2. 调 `mcp__flowmax-ops__credit_summary`（可选 `start`/`end` 时间窗），拿 Credit 按类型汇总。

3. 按需补充：`mcp__flowmax-ops__list_users`（最近注册）、`mcp__flowmax-ops__list_invite_codes`（邀请码状态）。

## 输出（Markdown 大盘报告）

- **用户**：总用户、本周新增、上周新增、最近注册（含余额与 Agent 数）
- **Agent**：总数、活跃数、昨日决策量
- **Credit**：充值 / 扣费消耗 / 奖励 / 发放 / 过期 各自总额与笔数（金额按 credit 单位原样呈现，注明是「总额」还是「净额」）
- **邀请码**：发放/使用/使用率
- **一句话洞察**：对比本周 vs 上周新增趋势、Credit 净流入/流出方向

金额和计数直接引用接口数值，不要换算或猜测口径。
