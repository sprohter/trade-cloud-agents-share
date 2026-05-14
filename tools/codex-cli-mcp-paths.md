# codex-cli-mcp-paths

**用途**：检查并修复 Codex CLI 用户级 `~/.codex/config.toml` 中业务 MySQL MCP server 的脚本路径。

**使用场景**：`trade.ps1 verify mcp-paths` 或完整性检查提示 `test-db` / `prod-db-epean` / `prod-db-mengxuan` 仍指向旧 `.kiro/settings/mcp-servers/mysql-server/index.js`。

**脚本路径**：`.agents/scripts/setup/repair-codex-cli-mcp-paths.ps1`

**调用方式**：

```powershell
.\.agents\scripts\setup\repair-codex-cli-mcp-paths.ps1 -Action Check
.\.agents\scripts\setup\repair-codex-cli-mcp-paths.ps1 -Action Apply
.\.agents\scripts\setup\repair-codex-cli-mcp-paths.ps1 -Action Check -Json
```

**行为边界**：

- 真源读取 `.agents/runtime/mcp/codex.mcp.json`。
- 只处理 `test-db`、`prod-db-epean`、`prod-db-mengxuan` 三个业务 MySQL MCP server 的 `args` 行。
- `Apply` 会先备份用户级 `config.toml` 到 `.agents/runtime/state/artifacts/agent/`。
- 输出只包含 server 名、路径状态、是否变更和备份路径，不输出 env、密码、token 或完整连接信息。
- `switch-codex-cch-profile.ps1` 写回 Codex profile 后会自动调用该脚本，防止模型切换保留旧 MCP 路径。

**维护状态**：活跃
