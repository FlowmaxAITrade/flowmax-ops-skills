---
name: review-incident
description: 排查失败或异常的决策轮次，归类失败原因。当用户要求「查失败决策」「为什么失败」「排查异常轮次」「出问题的有哪些」时使用。
---

# 失败决策排查

找出失败/异常的决策轮次并归类根因。

## 前置

依赖 MCP server **`flowmax-ops`**。工具用全限定名 `mcp__flowmax-ops__<tool>`。

## 步骤

1. **拉失败轮次**：调 `mcp__flowmax-ops__search_decisions`，`status` 传 `failed`（如需降级成功也传 `degraded_success` 单独查）。时间窗按用户要求。

2. **逐个深入**：对每个失败 round，调 `mcp__flowmax-ops__get_round`（`pm_id` + `round_id`），重点看 `events` 时间线，定位是卡在研究失败（RESEARCH_FAILED/RESEARCH_TIMEOUT）、处理失败（PROCESS_FAILED）、还是整体失败（ROUND_FAILED）。

3. **归类**：把失败原因分组（如「研究超时」「执行异常」「上游不可用」）。

## 输出（Markdown 排查报告）

- **失败概况**：失败总数、涉及交易员、时间分布
- **逐条清单**：每条含交易员、round_id、失败阶段、失败事件、简短说明
- **根因归类**：按失败阶段/原因聚合，标出高发类型
- **建议**：指向最值得深入复盘的几轮（给 round_id，方便用 `review-round`）

只陈述有据可查的事实，原因不明确就写「未知/需进一步排查」，不要归因。
