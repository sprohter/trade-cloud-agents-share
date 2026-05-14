# setup — 环境配置与模型切换工具

**用途**：CCH API 配置、Claude Code / Codex 模型切换、环境初始化。

**使用场景**：新环境初始化、切换 AI 模型、配置 CCH 路由 profile。

**脚本路径**：`.agents/scripts/setup/`

| 脚本 | 作用 |
|------|------|
| `switch-claude-cch-profile.ps1` | 切换 Claude Code 的 CCH profile |
| `switch-codex-cch-profile.ps1` | 切换 Codex 的 CCH profile |
| `repair-codex-cli-mcp-paths.ps1` | 检查 / 修复 Codex CLI 用户级 DB MCP 路径，防止 `~/.codex/config.toml` 保留旧 `.kiro/settings/mcp-servers` |
| `switch-kiro-claude-model.ps1` | 切换 Kiro 使用的 Claude 模型 |
| `setup-cch-api.ps1` | 已废弃：改用 profile 切换脚本 |
| `start-codex-with-fixed-env.ps1` | 以修正环境变量启动 Codex |
| `manage-claude-cch-ext-opus46.ps1` | 已废弃：改用 `switch-claude-cch-profile.ps1` |
| `test-agent-collab-readiness.ps1` | 校验 Claude / Codex 协作骨架资产，并执行会话冒烟验证 |
| `test-agents-framework-integrity.ps1` | 校验 `.agents/` 框架完整性：routing 路径、Codex/steering 入口、MCP 配置、skill 引用 |
| `install-agent-host-entries.ps1` | 从 `.agents/host-entries/` 安装根目录 `AGENTS.md` / `CLAUDE.md` 宿主入口 |
| `claude-cch-routing-profiles.json` | Claude Code CCH 路由 profile 配置 |
| `codex-cch-routing-profiles.json` | Codex CCH 路由 profile 配置 |

真实 CCH token 不写入 profile JSON；切换脚本按 `token_secret_key` 从环境变量或 `.agents/runtime/local-secrets/cch.env` 读取。

切换脚本和 readiness 脚本产生的本地备份 / 报告默认写入 `.agents/runtime/state/artifacts/agent/`，该目录只作本机运行态留痕并被 `.agents/runtime/state/.gitignore` 忽略。旧的根目录 `archive/agent/config-backups/` 只保留历史回滚点，不再作为新产物默认落点。

**当前候选补充**：`claude-cch-routing-profiles.json` 已包含 `glm-5.1` 候选 profile，当前顺序上优先于 `glm-5`，可在其他模型访问超时时作为备选兜底。

**维护状态**：活跃
