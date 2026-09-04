# flowmax-ops-skills

Flowmax 内部「经营驾驶舱 + 复盘」的 Claude Code 插件，给老板/管理员用自然语言调取交易员决策数据做复盘、查看平台经营数据。

本插件只是**编排层**，真正的数据来自 MCP server **[`flowmax-ops-mcp`](https://github.com/FlowmaxAITrade/flowmax-ops-mcp)**。

> **说明**：底层实体是 PM agent（非 trader），skill 已改名 `review-pm-agent`。后续底层路径将迁移到 `/api/v1/reporting/*`，权威约定见 ai-trading-ops-be 仓库的 `docs/api-conventions.md`。

## 技能列表

| 技能 | 用法 | 作用 |
|---|---|---|
| `review-pm-agent` | `/review-pm-agent <id或名字>` | 复盘单个 PM agent（收益/胜率/决策风格/最近动作） |
| `review-round` | `/review-round <id> <round_id>` | 复盘单轮决策完整链路 |
| `review-period` | `/review-period <时间窗>` | 全局周期复盘（谁最活跃、成功率、标的分布、异常） |
| `review-incident` | `/review-incident` | 排查失败/异常决策并归类根因 |
| `ops-overview` | `/ops-overview` | 经营大盘（用户/Agent/Credit/邀请码） |

## 前置

先安装并配置 MCP server，**server 名必须是 `flowmax-ops`**（技能里按这个名字引用工具）：

```bash
# 下载 flowmax-ops-mcp 二进制（见其 Releases），然后：
claude mcp add flowmax-ops --scope user \
  --env OPS_BE_BASE_URL=<占位> \
  --env OPS_API_KEY=<占位> \
  -- /path/to/flowmax-ops-mcp
```

## 安装

在 Claude Code 里执行：

```
/plugin marketplace add FlowmaxAITrade/flowmax-ops-skills
/plugin install flowmax-ops-skills
```

安装后即可在任意目录用上面 5 个斜杠命令。

> 经 marketplace 安装后，插件的**完整名**是 `flowmax-ops-skills@flowmax-ops-skills`（`插件名@marketplace名`）。下面「更新」和 `uninstall` 都要用这个完整名。

## 开发

本地验证插件结构：

```bash
claude plugin validate --strict .
```

## 版本发布

版本号**手动**管理（无构建产物，不需要 release-please）：

1. 把 `plugin.json` 的 `version` 和 `.claude-plugin/marketplace.json` 的 `plugins[].version` **同步**改成新版本（语义化版本，如 `0.1.1`）。
2. 用 CLI 打 tag（会自动校验两处版本一致；tag 格式固定为 `flowmax-ops-skills--v<version>`）：

```bash
claude plugin tag --push          # 或先 --dry-run 预览
```

3. 用户侧更新到最新版本（`update` 是 `claude plugin` 的 CLI 命令，不是斜杠命令；用完整名）：

```bash
claude plugin update flowmax-ops-skills@flowmax-ops-skills
```

更新后需重启 Claude Code 生效。

