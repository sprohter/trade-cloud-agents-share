# context-audit - Codex token/context audit

**用途**：本地只读统计最近 N 天 Codex session token、最大会话、重复治理检查、`.agents` runtime 产物、collab 产物和历史报告体积，用于判断是否需要 handoff 或减少全文读取。

**入口脚本**：

- `.agents/scripts/audit/measure-codex-context.ps1`
- 推荐统一入口：`.agents/scripts/trade.ps1 audit context`

**常用命令**：

```powershell
.\.agents\scripts\trade.ps1 audit context
.\.agents\scripts\trade.ps1 audit context --days 2 --top 8
.\.agents\scripts\trade.ps1 audit context --json
```

**输出策略**：

- 默认终端只输出摘要、最大会话、重复任务和简短分析。
- 完整 JSON 写入 `.agents/runtime/state/trade-cli/context-audit-*.json`。
- `--json` 保持机器可读输出。

**安全边界**：

- 只读取 Codex session JSONL 的 `token_count` 事件和文件元数据。
- 不读取 prompt / response 正文。
- 不读取 `.agents/runtime/local-secrets/`、MCP 凭证、数据库连接密码或用户级配置正文。
- 默认只统计 `.agents/runtime/state/`、`.agents/collab/sessions/`、历史报告的文件元数据；需要证据时再定点打开具体文件。

**维护状态**：活跃
