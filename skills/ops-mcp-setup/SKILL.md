---
name: ops-mcp-setup
description: 安装、配置、更新或排查 flowmax-ops MCP server（flowmax-ops-mcp）。当用户要求「装/更新 ops-mcp」「配置 MCP」「MCP 工具调不到」「flowmax-ops 连不上」时使用。
---

# 安装 / 更新 ops-mcp

Flowmax 复盘与经营驾驶舱的 skills 都依赖 MCP server **`flowmax-ops`**（仓库 `FlowmaxAITrade/flowmax-ops-mcp`）。本 skill 覆盖它的安装、配置、更新与排障。

## 关键约定

- **server 名固定为 `flowmax-ops`**：所有 skill 用全限定名 `mcp__flowmax-ops__<tool>` 引用工具，名字不一致会导致工具调不到。
- **`OPS_API_KEY` 填界面生成的 operator key**（`fmx_` 开头），不是全局静态 key。

## 安装

二进制两种获取方式任选：

1. **go install**（需 Go 环境）：
   ```bash
   GOPROXY=https://goproxy.cn,direct go install github.com/FlowmaxAITrade/flowmax-ops-mcp/cmd/flowmax-ops-mcp@latest
   ```

2. **下载预编译二进制**：到 [Releases](https://github.com/FlowmaxAITrade/flowmax-ops-mcp/releases) 选对应平台（darwin/linux/windows × amd64/arm64）解压。

然后接入 Claude Code：

```bash
claude mcp add flowmax-ops --scope user \
  --env OPS_BE_BASE_URL=<ops-be 服务地址> \
  --env OPS_API_KEY=<fmx_* operator key> \
  -- /path/to/flowmax-ops-mcp
```

## 生成 API Key

`OPS_API_KEY` 用的是运营后台「API Keys」页生成的 `fmx_*` operator key：

1. 登录运营后台 → API Keys → Generate Key。
2. 复制 `fmx_...` 明文（只显示一次，存好）。
3. 填入 `OPS_API_KEY`。

## 更新

1. 更新二进制到最新版：
   - go install：重跑上面的 `go install ...@latest`。
   - 预编译：下载最新 Release 覆盖旧二进制路径。
2. `claude mcp list` 确认 `flowmax-ops` 指向新二进制；路径变了就 `claude mcp remove flowmax-ops` 后重新 `add`。
3. 重启 Claude Code 生效。

## 排障

- 「MCP server `flowmax-ops` not found」→ 未安装或名字不对，`claude mcp list` 核对。
- 返回 401 → `OPS_API_KEY` 无效（key 不存在、已被删、或对应 operator 被停用）；回后台重新生成或确认 operator 状态。
