# collab — CC协作会话工具

**用途**：用结构化 session 文件管理 Claude / Codex 的 request、response、status 和 failover 记录。

**别名**：

- `CC协作`
- `CC协作会话`
- `Claude/Codex 协作会话`

**默认理解**：当前默认指向 `.agents/collab/` 这套文件化协作 session，不是自动双向消息桥。

**推荐组合**：

- `Claude 主控 + Codex MCP 执行 + CC协作 session 留痕`
- `Codex 主控 + Claude CLI 生成测试方案 + CC协作 session 留痕`

**使用场景**：需要显式记录两端协作过程、切换 owner、做降级切换、或在本地跑一遍协作 readiness 验证。

**脚本路径**：

- `.agents/scripts/collab/manage-collab-session.ps1`
- `.agents/scripts/collab/invoke-claude-fast.ps1`
- `.agents/scripts/collab/invoke-claude-test-plan.ps1`
- `.agents/scripts/setup/test-agent-collab-readiness.ps1`

| 脚本 | 作用 |
|------|------|
| `manage-collab-session.ps1` | 管理 `init`、`request`、`respond`、`status`、`list`、`switch-mode`、`complete`、`fail`；新建 session 时会提醒未完成 session |
| `invoke-claude-fast.ps1` | Codex 快速一次性调用 Claude Code CLI；不写 session，默认严格空 MCP，适合短问题、交叉核对和 smoke test |
| `invoke-claude-test-plan.ps1` | Codex 一次性调用 Claude Code CLI 生成测试方案；request/response JSON 和 Markdown 原文写入 `.agents/collab/sessions/` |
| `test-agent-collab-readiness.ps1` | 检查协作资产是否齐全，并跑一遍本地冒烟链路；默认只输出摘要，完整 JSON 写入 `.agents/runtime/state/trade-cli/` |

**配套文档**：

- `.agents/collab/README.md`
- `.agents/collab/bridge-overview.md`
- `.agents/collab/PROTOCOL.md`
- `.agents/collab/claude-cli-test-plan-collaboration-design.md`

**常用命令**：

```powershell
.\.agents\scripts\collab\manage-collab-session.ps1 -Action init -SessionId "demo-collab" -Title "Demo" -Goal "Validate collab flow"
.\.agents\scripts\collab\manage-collab-session.ps1 -Action list
.\.agents\scripts\collab\invoke-claude-fast.ps1 -Prompt "只回复 OK"
.\.agents\scripts\collab\invoke-claude-fast.ps1 -Prompt "只回复 OK" -Json
.\.agents\scripts\collab\invoke-claude-test-plan.ps1 -SessionId "demo-test-plan" -Title "Demo test plan" -Goal "Design a minimal executable test plan" -InputText "Smoke input"
.\.agents\scripts\setup\test-agent-collab-readiness.ps1
.\.agents\scripts\setup\test-agent-collab-readiness.ps1 -Json
```

**Claude CLI 测试方案入口说明**：

- 默认纯文本顾问模式：`--tools ""`、`--no-session-persistence`、`--setting-sources user`
- 默认显式传空 MCP 配置并启用 `--strict-mcp-config`，避免项目根 `.mcp.json` 自动拉起 DB / Codex MCP 导致慢启动或卡死
- 需要 Claude 读取外部工具时，再显式传 `-McpConfig` 和 `-EnableTools`
- Claude CLI 失败时，脚本会记录失败 response，并把 session 切到 `codex-solo`

**入口选择**：

- 只需要 Claude 回答 / 复核一句话：用 `invoke-claude-fast.ps1`
- 需要 CC 协作留痕、测试方案 request/response 和 failover：用 `invoke-claude-test-plan.ps1`

**维护状态**：活跃
