# timon-skills

Flowmax 内部「经营驾驶舱 + 复盘」的 Claude Code 插件，给老板/管理员用自然语言调取交易员决策数据做复盘、查看平台经营数据。

本插件只是**编排层**，真正的数据来自 MCP server **[`timon-mcp`](https://github.com/FlowmaxAITrade/timon-mcp)**。

> **说明**：底层实体是 PM agent（非 trader），skill 已改名 `review-pm-agent`。后续底层路径将迁移到 `/api/v1/reporting/*`，权威约定见 timon-be 仓库的 `docs/api-conventions.md`。

## 技能列表

| 技能 | 用法 | 作用 |
|---|---|---|
| `review-pm-agent` | `/review-pm-agent <id或名字>` | 复盘单个 PM agent（收益/胜率/决策风格/最近动作） |
| `review-round` | `/review-round <id> <round_id>` | 复盘单轮决策完整链路 |
| `review-period` | `/review-period <时间窗>` | 全局周期复盘（谁最活跃、成功率、标的分布、交易额/手续费/回撤、异常） |
| `review-incident` | `/review-incident` | 排查失败/异常决策并归类根因 |
| `ops-overview` | `/ops-overview` | 经营大盘（用户/Agent/Credit/邀请码） |
| `ops-mcp-setup` | `/ops-mcp-setup` | 安装/配置/更新 timon-mcp |

## 前置

先安装并配置 MCP server，**server 名必须是 `timon`**（技能里按这个名字引用工具）。完整步骤（含更新与排障）见 `/ops-mcp-setup`：

```bash
# 安装二进制（go install 或下载 Releases），然后：
claude mcp add timon --scope user \
  --env OPS_BE_BASE_URL=<ops-be 地址> \
  --env OPS_API_KEY=<fmx_* operator key> \
  -- /path/to/timon-mcp
```

> `OPS_API_KEY` 填运营后台「API Keys」页生成的 `fmx_*` key（只显示一次），不是全局静态 key。

## 更新 MCP

更新 timon-mcp 到最新版（新功能/修复需更新二进制后重启 Claude Code）：

- **go install**：`GOPROXY=https://goproxy.cn,direct go install github.com/FlowmaxAITrade/timon-mcp/cmd/timon-mcp@latest`
- **预编译二进制**：下载最新 [Release](https://github.com/FlowmaxAITrade/timon-mcp/releases) 覆盖旧路径

完整步骤与排障见 `/ops-mcp-setup`。

## 安装

在 Claude Code 里执行：

```
/plugin marketplace add FlowmaxAITrade/timon-skills
/plugin install timon-skills
```

安装后即可在任意目录用上面 6 个斜杠命令。

> 经 marketplace 安装后，插件的**完整名**是 `timon-skills@timon-skills`（`插件名@marketplace名`）。下面「更新」和 `uninstall` 都要用这个完整名。

## 开发

本地验证插件结构：

```bash
claude plugin validate --strict .
```

## 版本发布

版本号**手动**管理（无构建产物，不需要 release-please）：

1. 把 `plugin.json` 的 `version` 和 `.claude-plugin/marketplace.json` 的 `plugins[].version` **同步**改成新版本（语义化版本，如 `0.1.1`）。
2. 用 CLI 打 tag（会自动校验两处版本一致；tag 格式固定为 `timon-skills--v<version>`）：

```bash
claude plugin tag --push          # 或先 --dry-run 预览
```

3. 用户侧更新到最新版本（`update` 是 `claude plugin` 的 CLI 命令，不是斜杠命令；用完整名）：

```bash
claude plugin update timon-skills@timon-skills
```

更新后需重启 Claude Code 生效。

