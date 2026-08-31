---
name: review-incident
description: 排查失败或异常的决策轮次，归类失败原因。当用户要求「查失败决策」「为什么失败」「排查异常轮次」「出问题的有哪些」时使用。
---

# 失败决策排查

找出失败/异常的决策轮次并归类根因。

## 前置

依赖 MCP server **`flowmax-ops`**。工具用全限定名 `mcp__flowmax-ops__<tool>`。

## 步骤

1. **拉异常轮次**：调 `mcp__flowmax-ops__search_decisions`，`status` 传 `execution_failed`（执行失败）；跳过（HOLD/风控兜底）用 `status` 传 `skipped` 单独查。时间窗按用户要求。

2. **逐个深入**：对每个异常 round，调 `mcp__flowmax-ops__get_round`（`pm_id` + `round_id`），看 `message` 字段定位原因：`PM Decision skipped:*`（读 message 里的跳过原因）、`Decision execution failed: ...`（读报错）。

3. **归类**：把失败原因分组（如「研究超时」「执行异常」「上游不可用」）。

## 大响应处理

`get_round` 单轮返回可能较大，超过 token 上限时结果会存到文件（提示里会给路径）。定位原因只需 `message` 字段，用 `jq` 取即可，不要整读文件：

```bash
F=<提示里给出的文件路径>
jq -r '.status, .message' "$F"
```

## 输出（Markdown 排查报告）

- **异常概况**：异常轮次总数、涉及交易员、时间分布
- **逐条清单**：每条含交易员、round_id、状态（skipped/execution_failed）、message 摘要
- **根因归类**：按原因聚合（跳过原因 / 执行错误），标出高发类型
- **建议**：指向最值得深入复盘的几轮（给 round_id，方便用 `review-round`）

只陈述有据可查的事实，原因不明确就写「未知/需进一步排查」，不要归因。
