# Agents Share Routing

> 本文件是本分享包的任务路由入口。

---

## 主流程

| 场景 | 入口文件 |
|------|---------|
| 功能测试、提测、回归、上线验收、测试结论 | `workflows/functional-testing.md` |
| 线上反馈、问题分析、业务反馈、生产只读核查、排查结论 | `workflows/online-feedback-analysis.md` |

---

## 子流程

| 场景 | 入口文件 |
|------|---------|
| 需求文档、测试点拆分、用例设计、需求有效性判断 | `subflows/requirement-and-test-case-design.md` |
| 提 Bug、禅道、Bug 标题、重现步骤、缺陷流转 | `subflows/bug-handoff-and-zentao.md` |
| 修复验证、回归是否通过、验证结论怎么写 | `subflows/fix-verification.md` |
| Web 页面异常、前后端问题分诊、先抓接口还是先查日志 | `subflows/web-issue-triage.md` |
| 业务回复、群里回复、怎么同步排查结论 | `subflows/business-feedback-response.md` |

---

## 工具与配置

| 场景 | 入口文件 |
|------|---------|
| MCP、数据库、禅道、OA、Seafile、DingTalk AI 表格、工具配置口径 | `runbooks/tooling-and-config-overview.md` |
| 问题台账、AI 表格回填、反馈记录维护 | `runbooks/issue-board-update.md` |
| OA 公告、上线结果同步 | `runbooks/oa-announcement-maintenance.md` |
| Seafile、执行版用例归档 | `runbooks/seafile-testcase-archival.md` |
| 本地 agent 怎么接入本分享包 | `config-templates/agent-entry.template.md` |
| MySQL MCP 配置模板 | `config-templates/mcp.template.jsonc` |

---

## 治理

| 场景 | 入口文件 |
|------|---------|
| 分享包怎么维护、怎么加新内容、什么不该放进来 | `governance/maintenance-rules.md` |

---

## 默认读取顺序

1. 先定位主流程
2. 再补对应子流程
3. 最后按需读 runbook 和配置模板
