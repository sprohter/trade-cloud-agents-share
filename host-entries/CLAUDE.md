# CLAUDE.md

> 本文件由 `.agents/host-entries/CLAUDE.md` 维护。
> agent 规则、路由、知识、脚本和运行时资产的真源统一在 `.agents/`。

## Agent 框架入口

Claude Code 只把仓库根目录的本文件作为固定宿主启动入口。

任务开始时：

1. 读取 `.agents/routing.md`。
2. 读取匹配到的 skill / knowledge / governance 文件。
3. 遵循 `.agents/contract.md`。
4. 如需 Claude Code 专属规则，读取 `.agents/claude-code/adapter.md`。

## 运行时来源

- MCP 真源配置：`.agents/runtime/mcp/claude.mcp.json`
- MCP 宿主镜像：`.mcp.json`
- 本地私有配置来源：`.agents/runtime/local-secrets/`
- Skill 真源：`.agents/skills/`
- 工具脚本真源：`.agents/scripts/`

## 知识边界

本文件不承载项目知识正文。需要项目知识时读取 `.agents/`：

- 项目概览：`.agents/knowledge/infrastructure/project-overview.md`
- 技术栈：`.agents/knowledge/infrastructure/tech-stack-analysis.md`
- 基础设施：`.agents/knowledge/infrastructure/project-infrastructure.md`
- 代码库定位：`.agents/skills/codebase-map/SKILL.md`
- 测试流程：`.agents/knowledge/testing/methods.md`
- 开发卫生：`.agents/knowledge/development/temporary-artifact-hygiene.md`

## 默认语言

- 默认使用简体中文。
- 技术术语、命令、路径、API 名、表名、字段名、产品名可保留英文。
