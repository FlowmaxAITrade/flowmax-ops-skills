# Timon 改名迁移指南：清理旧版 flowmax-ops 工具

运营后台工具已从「flowmax-ops」改名为「Timon」（MCP 仓库 `flowmax-ops-mcp` → `timon-mcp`，Skills 仓库 `flowmax-ops-skills` → `timon-skills`）。

如果你之前装过旧版，请按下面步骤清理旧版并换到新版。

## 一、卸载旧 Skills 插件

在终端执行（注意用完整名 `插件名@marketplace名`，裸名会报 not found）：

```bash
claude plugin uninstall flowmax-ops-skills@flowmax-ops-skills
```

## 二、移除旧 MCP 服务器

```bash
claude mcp remove flowmax-ops
```

## 三、删除旧 MCP 二进制（可选，之前用 go install 装的）

```bash
rm "$(go env GOPATH)/bin/flowmax-ops-mcp"
```

> 如果你之前是手动下载的预编译二进制，直接删掉那个文件即可。

## 四、安装新版

```bash
# 1. 安装新版 MCP 二进制
go install github.com/FlowmaxAITrade/timon-mcp/cmd/timon-mcp@latest

# 2. 注册新版 MCP 服务器（名字改为 timon）
claude mcp add timon --scope user \
  --env OPS_BE_BASE_URL=<ops-be 地址> \
  --env OPS_API_KEY=<fmx_* operator key> \
  -- "$(go env GOPATH)/bin/timon-mcp"

# 3. 安装新版 Skills 插件（在 Claude Code 里执行）
/plugin marketplace add FlowmaxAITrade/timon-skills
/plugin install timon-skills
```

改完后**重启 Claude Code** 生效。

> `OPS_API_KEY` 填运营后台「API Keys」页生成的 `fmx_*` key（只显示一次），不是全局静态 key。
