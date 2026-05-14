# agents-framework-integrity — .agents 框架完整性自检

**用途**：对 `.agents/` 当前活跃框架做本地可重复执行的完整性检查，重点覆盖路由、Codex 桌面 / Codex CLI / Claude Code 入口、MCP 口径、skill 引用完整性和仓库卫生。

**默认检查范围**：

1. `routing.md` 中的活跃路径是否存在
2. `.agents/codex/adapter.md` 与 `.kiro/steering/core/agent-framework.md` 是否一致
3. `knowledge/infrastructure/mcp-services.md` 与 `.agents/runtime/mcp/codex.mcp.json` / `.agents/runtime/mcp/claude.mcp.json` 的业务 MySQL MCP 口径是否一致，默认覆盖 `test-db`、`prod-db-epean`、`prod-db-mengxuan`
4. `routing.md` 引用到的 skill 根目录是否都存在 `SKILL.md`
5. Codex adapter / runtime-configs 是否显式区分 Codex 桌面与 Codex CLI，根 `AGENTS.md` 是否仍能让 CLI 在项目 cwd 进入 `.agents/` 真源
6. Codex CLI 用户级 `~/.codex/config.toml` 中的 `test-db` / `prod-db-epean` / `prod-db-mengxuan` 是否仍指向 `.agents/runtime/mcp-servers/mysql-server/index.js`，而不是旧 `.kiro/settings/mcp-servers`
7. 活跃 skill / knowledge / tools / runtime / case-studies 文档中是否还残留旧 `ops/`、`.kiro/templates/`、`.kiro/docs/`、非薄指针 `.kiro/steering/`、`~/.codex/*.toml`、`scripts/agent-tools/dws`、`docs/codex/guides/testing-requirements` 真源口径或死链
8. 活跃脚本是否仍通过旧 `ops/` 目录解析共享 Node 依赖；当前统一使用 `.agents/runtime/node-deps/`
9. `.agents/.git` 是否健康，且运行产物、缓存、云同步临时文件等不会进入提交边界
10. `trade.ps1 preflight --json`、`trade.ps1 verify mcp-paths --json` 成功路径，以及隔离副本中 `trade.ps1 verify mcp-paths --json` 失败路径，是否都只刷新 `.agents/runtime/state/trade-cli/` 本地缓存，不改现有 collab session `status.json`，且不会遗留新的 `readiness-*` smoke session 目录

**设计原则**：

- 默认只检查活跃真源，不把 `archive/` / deprecated 文档混入失败项
- `.agents/.git` 对云同步临时文件零容忍；活跃树和 `runtime/state/` 中的临时文件必须被 `.gitignore` 隔离
- `adapters/active/codex.md` 只做 tombstone 辅助检查，不再作为主真源
- 宿主镜像 `.kiro/settings/mcp.json` / `.mcp.json` 默认一起校验；如只想看 Codex 侧，可显式跳过
- Codex CLI 命令是否安装只做可见性提示，不作为框架失败项；但 adapter、runtime-configs 和根 `AGENTS.md` 必须保持 CLI 可进入同一 `.agents/` 真源
- 用户级 `~/.codex/config.toml` 含敏感连接参数，自检只检查 MCP server 路径字符串，不输出、不保存密码值
- `verify framework-integrity` 的别名包装仍通过现有 deep check 链路单独验证；为避免递归，边界回归只覆盖 `preflight` 与 `verify mcp-paths`
- `verify mcp-paths` 的失败路径边界回归只在 `.agents/runtime/state/tmp/` 隔离副本里注入 target script 缺失，要求失败时仍输出可解析 JSON，并刷新本地 cache 而不污染真实工作区

**脚本路径**：

- `.agents/scripts/setup/test-agents-framework-integrity.ps1`

**输出策略**：

- 默认终端只输出结论、计数、失败项和完整报告路径。
- 完整 JSON 默认写入 `.agents/runtime/state/trade-cli/agents-integrity-*.json`。
- 需要机器消费时使用 `-Json`；需要指定归档位置时使用 `-OutputPath`。

**常用命令**：

```powershell
.\.agents\scripts\setup\test-agents-framework-integrity.ps1

.\.agents\scripts\setup\test-agents-framework-integrity.ps1 -Json

.\.agents\scripts\setup\test-agents-framework-integrity.ps1 `
  -OutputPath '.\.agents\archive\session-records\agents-framework-integrity-latest.json'

.\.agents\scripts\setup\test-agents-framework-integrity.ps1 `
  -IncludeDeprecated

.\.agents\scripts\setup\test-agents-framework-integrity.ps1 `
  -SkipClaudeMcp
```

**退出码**：

- `0`：全部通过
- `1`：存在失败项

**失败项输出格式示例**：

```json
{
  "item": "routing.path_exists::knowledge/infrastructure/mcp-services.md",
  "expected": "path exists",
  "actual": "missing: <agents-root>/knowledge/infrastructure/mcp-services.md",
  "pass": false,
  "severity": "P1",
  "repair": "补齐目标文件/目录，或修正 .agents/routing.md 中的路径。",
  "source": ".agents/routing.md"
}
```

**维护状态**：活跃
