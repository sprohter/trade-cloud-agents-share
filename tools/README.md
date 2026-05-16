# 工具索引

> agent 工作流中使用的可执行脚本的发现层。脚本本身留在 `.agents/scripts/`，本目录只维护索引卡。
>
> **强制规则**：新建 agent 工作流脚本时，必须同步在此建索引卡。

## 索引

| 工具 | 用途 | 索引卡 |
|------|------|--------|
| dws | 钉钉产品能力工具：AI 表格、单聊/群聊、机器人消息、DING、待办等 | `dws.md` |
| ops-runtime | MongoDB / Redis / SSH / RabbitMQ / Nacos / Grafana 共享脚本入口 | `ops-runtime.md` |
| elk-discover-url | 根据日志 ID、关键字或 KQL 生成 Kibana Discover 查询链接 | `elk-discover-url.md` |
| ep-log-search | 通过 applog SSH 中转机查询生产应用原始日志上下文 | `ep-log-search.md` |
| ops-ci | Jenkins / XXL-Job 共享脚本入口 | `ops-ci.md` |
| ops-docs | Seafile / 共享文档共享脚本入口 | `ops-docs.md` |
| runtime-probes | Playwright UI 探针脚本（毛利率、调价等页面） | `runtime-probes.md` |
| apifox-docs | Apifox 接口契约读取：优先 MCP，兜底本地 OpenAPI 缓存 | `apifox-docs.md` |
| mercadolibre-playwright | MercadoLibre 站点中文化 Playwright 回归脚本 | `mercadolibre-playwright.md` |
| autopull | 自动 git pull，保持本地代码最新 | `autopull.md` |
| setup | CCH 配置、模型切换、环境初始化 | `setup.md` |
| codex-cli-mcp-paths | Codex CLI 用户级 DB MCP 路径检查与修复 | `codex-cli-mcp-paths.md` |
| agents-framework-integrity | `.agents/` 框架完整性自检，校验路由、入口、MCP 与 skill 引用 | `agents-framework-integrity.md` |
| collab | Claude / Codex 协作会话管理与 readiness 冒烟 | `collab.md` |
| context-audit | Codex token/context 与本地运行态产物审计 | `context-audit.md` |
| dingtalk-bridge | 钉钉只读请求桥接到 Codex 的 Phase 1 PoC 骨架 | `dingtalk-bridge.md` |
| codex-feishu-helper | 飞书控制 Codex app-server 任务的新入口，上游 helper 独立运行 | `codex-feishu-helper.md` |
| codex-feishu-helper-user-guide | codex-feishu-helper 日常使用手册 | `codex-feishu-helper-user-guide.md` |
| feishu-bridge | 旧方案：Python 飞书桥，保留为回退方案 | `feishu-bridge.md` |
| sync | Skill 同步、MCP 配置同步 | `sync.md` |
| oa-prod-announcement | 生产 OA 发版公告维护：钉钉表格对齐、草稿创建/更新、样式验收、状态回写 | `oa-prod-announcement.md` |
| oa-prod-announcement-from-git | 生产 OA 发版公告维护：从 master 合入记录生成草稿，复核后进入 OA 维护链路 | `oa-prod-announcement-from-git.md` |
| data-export | 业务数据导出脚本索引卡 | `data-export.md` |
| scripts-index | `.agents/scripts/` 根层管理脚本索引卡 | `scripts-index.md` |
| third-party-warehouse-replenish-log-stats | 三方仓补货操作日志统计脚本索引卡 | `third-party-warehouse-replenish-log-stats.md` |
