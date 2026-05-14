# Trade Cloud Codex 工作入口

> 本文件由 `.agents/host-entries/AGENTS.md` 维护。
> agent 规则、路由、知识、脚本和运行时资产的真源统一在 `.agents/`。

## 路由入口

当任务需要调用工具时，第一次工具调用必须先读取 `.agents/routing.md`，再遵循 `.agents/contract.md`，并在执行任务前读取匹配到的 skill / knowledge / governance 文件。

轻量读取默认策略：
- 先读取 `.agents/README.md` 的导航说明、`.agents/routing.md` 的命中路由行、`.agents/contract.md` 的相关小节。
- 再读取命中的 skill / knowledge / governance 文件。
- 只有在路由不明确、规则冲突、治理变更、或需要原文证据时，才把 `.agents/routing.md` / `.agents/contract.md` 整篇纳入上下文。

例外：
- 纯对话或纯解释，不需要调用工具。
- 用户已经明确给出具体 skill / knowledge 文件路径。

## Agent 框架入口

- 框架根目录：`.agents/`
- 行为规则：`.agents/contract.md`
- 路由表：`.agents/routing.md`
- Codex 适配说明：`.agents/codex/adapter.md`
- 运行时配置索引：`.agents/codex/runtime-configs.md`
- MCP 真源配置：`.agents/runtime/mcp/codex.mcp.json`
- Codex / Kiro 宿主镜像：`.kiro/settings/mcp.json`

## 文档放置规则

- 新增 agent 相关内容默认放到 `.agents/`。
- Agent 工具脚本放到 `.agents/scripts/`；索引卡放到 `.agents/tools/`。
- 默认不要在仓库根目录创建 agent 相关文档。
- 如果临时分析文件误建在根目录，任务结束前移回 `.agents/`。

## 长会话连续性

- 多步骤任务按阶段推进，并给出简短检查点。
- 如果上下文过长或容易丢失，使用 `.agents/templates/session-handoff.md` 创建精简交接。

## 默认语言

- 默认使用简体中文。
- 技术术语、命令、路径、API 名、表名、字段名、产品名可保留英文。
