# .agents/scripts/ 管理脚本索引

> `.agents/scripts/` 根层管理脚本的索引卡。脚本本体在 `.agents/scripts/`，此处只记录用途和调用方式。
> `scripts/`（项目根目录）已废弃，仅保留历史副本待清理。

---

## create-kiro-skill.ps1

**用途**：根据模板快速创建新的 skill 目录结构到 `.agents/skills/`（生成 SKILL.md 框架）。

**调用方式**：
```powershell
.\.agents\scripts\create-kiro-skill.ps1 -Name "skill-name" -Description "说明"
```

**维护状态**：活跃

---

## trade.ps1

**用途**：Trade Cloud Agent 工具的轻量统一 CLI 入口。当前提供 `preflight` 聚合预检、`doctor` 只读自检、`framework check` / `verify` 核验包装、`runtime` 只读诊断白名单、`collab` 协作可见性、`evidence init/add/list/summary` 本地测试证据目录管理、`audit context` token/context 审计、`business find` 业务上下文轻量定位，以及 `handoff create` 本地交接草稿。默认人类输出只给摘要和失败项，完整 JSON 写入 `.agents/runtime/state/`；涉及 `preflight` / `verify` / `audit` / `business find` 的实现允许刷新本地缓存，但不改业务、共享状态或外部系统。

**调用方式**：
```powershell
.\.agents\scripts\trade.ps1 preflight
.\.agents\scripts\trade.ps1 doctor
.\.agents\scripts\trade.ps1 doctor --json
.\.agents\scripts\trade.ps1 framework check
.\.agents\scripts\trade.ps1 verify framework-integrity
.\.agents\scripts\trade.ps1 verify mcp-paths
.\.agents\scripts\trade.ps1 runtime list
.\.agents\scripts\trade.ps1 runtime elk-url --trace-id e65842af8a809006709e
.\.agents\scripts\trade.ps1 collab list
.\.agents\scripts\trade.ps1 collab readiness
.\.agents\scripts\trade.ps1 evidence init --task 16035
.\.agents\scripts\trade.ps1 evidence add --task 16035 --type note --file D:\path\note.txt --source codex --environment test --object task-16035 --conclusion "verified"
.\.agents\scripts\trade.ps1 evidence list --task 16035
.\.agents\scripts\trade.ps1 evidence summary --task 16035 --json
.\.agents\scripts\trade.ps1 audit context --days 2
.\.agents\scripts\trade.ps1 business find --query "毛利率"
.\.agents\scripts\trade.ps1 business find --key gross-profit-rate
.\.agents\scripts\trade.ps1 business find --query "Shopee刊登" --search-code
.\.agents\scripts\trade.ps1 handoff create --task 16035
```

**维护状态**：试运行

---

## codebase/find-business-context.ps1

**用途**：业务测试或回归前的低 token 上下文定位。优先读取 `.agents/knowledge/business/context-map/` 的轻量业务卡片，命中卡片时输出卡片路径和新鲜度；未命中或显式 `-SearchCode` 时才用 `rg` 输出紧凑代码候选行。完整 JSON 写入 `.agents/runtime/state/business-context/`。脚本只读，不自动创建卡片，不修改业务代码，不读取 `.agents/runtime/state`、`.agents/collab/sessions`、archive 或历史报告正文。

**调用方式**：
```powershell
.\.agents\scripts\codebase\find-business-context.ps1 -Query "毛利率"
.\.agents\scripts\codebase\find-business-context.ps1 -Key gross-profit-rate
.\.agents\scripts\codebase\find-business-context.ps1 -Query "Shopee刊登" -SearchCode -Json
```

**维护状态**：试运行

---

## install-git-hooks.ps1

**用途**：将 `.githooks/` 目录下的 hook 脚本安装到本地 `.git/hooks/`，使 git 提交前校验生效。

**调用方式**：
```powershell
.\.agents\scripts\install-git-hooks.ps1
```

**维护状态**：活跃

---

## diag-startup.ps1

**用途**：启动失败快速诊断脚本，检查常见启动问题（端口、配置、依赖）。

**调用方式**：
```powershell
.\.agents\scripts\diag-startup.ps1 -EnvName test -Service trade-module
```

**维护状态**：活跃

---

## ops-runtime/runtime-ops.ps1

**用途**：MongoDB / Redis / SSH / RabbitMQ / Nacos / Grafana 的统一共享脚本入口。

**调用方式**：
```powershell
'{}' | & '.\.agents\scripts\ops-runtime\runtime-ops.ps1' ssh-list-profiles
```

**维护状态**：活跃

---

## ops-ci/ci-ops.ps1

**用途**：Jenkins / XXL-Job 的统一共享脚本入口。

**调用方式**：
```powershell
'{}' | & '.\.agents\scripts\ops-ci\ci-ops.ps1' jenkins-list-jobs
```

**维护状态**：活跃

---

## ops-docs/docs-ops.ps1

**用途**：Seafile / 共享文档的统一共享脚本入口。

**调用方式**：
```powershell
'{}' | & '.\.agents\scripts\ops-docs\docs-ops.ps1' seafile-list-repos
```

**维护状态**：活跃

---

## setup/test-agents-framework-integrity.ps1

**用途**：对 `.agents/` 当前活跃框架做可重复执行的完整性自检，覆盖 `routing.md` 路径存在性、`codex/adapter.md` 与 steering 入口一致性、MySQL MCP 配置口径、`skills/*/SKILL.md` 完整性，以及活跃文档中的旧入口残留。

默认终端只输出结论、计数、失败项和完整报告路径；完整 JSON 写入 `.agents/runtime/state/trade-cli/`。需要机器消费时使用 `-Json`。

**调用方式**：
```powershell
.\.agents\scripts\setup\test-agents-framework-integrity.ps1

.\.agents\scripts\setup\test-agents-framework-integrity.ps1 -Json

.\.agents\scripts\setup\test-agents-framework-integrity.ps1 `
  -OutputPath '.\.agents\archive\session-records\agents-framework-integrity-latest.json'
```

**维护状态**：活跃

---

## audit/measure-codex-context.ps1

**用途**：统计最近 N 天 Codex session token、最大会话、重复治理检查、runtime/collab 产物和历史报告体积。只读取 `token_count` 事件和文件元数据，不读取消息正文或敏感配置。

**调用方式**：
```powershell
.\.agents\scripts\audit\measure-codex-context.ps1 -Days 2
.\.agents\scripts\trade.ps1 audit context --days 2 --json
```

**维护状态**：活跃

---

## admin-codex-optimize.ps1

**用途**：Codex 运行环境优化配置（需管理员权限），优化 Node/Playwright 在 Windows 下的运行效率。

**调用方式**：以管理员身份运行
```powershell
.\.agents\scripts\admin-codex-optimize.ps1
```

**维护状态**：活跃

---

## zentao-bug-view.js

**用途**：命令行查看禅道 Bug 详情，与 MCP `zentao_get_bug_detail` 功能一致，适合无 MCP 时快速查看。

**调用方式**：
```bash
node .agents/scripts/zentao-bug-view.js <bugId>
```

**维护状态**：活跃

---

## zentao-my-bug-count.js

**用途**：查询当前账号名下未关闭 Bug 数量，与 MCP `zentao_get_my_bugs` 功能一致。

**调用方式**：
```bash
node .agents/scripts/zentao-my-bug-count.js
```

**维护状态**：活跃

---

## zentao_cli/run_zentao_cli.ps1

**用途**：Trade Cloud 禅道操作默认入口。负责装配本地认证、代理和 Python 运行环境，再转调 `.agents/scripts/zentao_cli/zentao_cli.py`。

**调用方式**：
```powershell
.\.agents\scripts\zentao_cli\run_zentao_cli.ps1 --output json auth status
```

**维护状态**：活跃

---

## zentao_cli/zentao_cli.py

**用途**：禅道 CLI 核心实现（认证、任务/缺陷查询、创建、评论、指派和状态流转）。日常禅道操作默认不要直接调用本文件，应走上面的 `run_zentao_cli.ps1` wrapper；旧 `.agents/scripts/zentao/zentao_api.py` 仅作为灰度 fallback。

**调用方式（仅底层调试）**：
```bash
python .agents/scripts/zentao_cli/zentao_cli.py --output json <resource> <action> [options]
```

**维护状态**：活跃

---

## browser/test-oa-login-smoke.js

**用途**：验证测试环境 OA 登录是否可用，输出截图、页面 HTML 和 storage state 作为回归证据。

**调用方式**：
```powershell
$env:TRADE_TEST_OA_USERNAME = '<测试OA用户名>'
$env:TRADE_TEST_OA_PASSWORD = '<测试OA密码>'
.\.agents\scripts\setup\invoke-node-tool.ps1 node .\.agents\scripts\browser\test-oa-login-smoke.js
```

**维护状态**：活跃

---

## browser/create-oa-prod-announcement.js

**用途**：登录生产 OA 后调用系统公告接口，按“当天查重、必要时覆盖更新”的规则创建或更新公告草稿，并做富文本渲染校验。

**调用方式**：
```powershell
.\.agents\scripts\setup\invoke-node-tool.ps1 node .\.agents\scripts\browser\create-oa-prod-announcement.js `
  --username $env:TRADE_PROD_OA_USERNAME `
  --password $env:TRADE_PROD_OA_PASSWORD `
  --date '2026-04-17' `
  --title 'OA系统更新日志20260417' `
  --content-file 'D:\path\to\content.txt'
```

**维护状态**：活跃

---

## browser/run-oa-prod-announcement-maintain.ps1

**用途**：端到端执行“钉钉表格读取 -> 生产 OA 公告草稿创建/更新 -> 表格状态回写待发布”。

**调用方式**：
```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-maintain.ps1 `
  -Username $env:TRADE_PROD_OA_USERNAME `
  -Password $env:TRADE_PROD_OA_PASSWORD
```

**维护状态**：活跃

---

## browser/run-oa-prod-announcement-daily.ps1

**用途**：工作日公告日常闭环入口，按顺序执行“确保当天表格记录存在 -> 更新 `AI统计公告内容（仅供参考）` -> 用表格权威正文维护生产 OA 草稿并回写 `待发布` -> 核验上一条记录是否已在 OA 发布并必要时回写 `已发布`”。

**默认行为**：
- 对应当前默认调度：工作日 `10:00 / 13:00 / 17:00 / 19:00`
- 若当天表格记录不存在，则按规范自动新增一条最小记录：`标题 = OA系统更新日志YYYYMMDD`、`计划上线日期 = 当天`、`OA状态 = 待创建`
- 参考稿优先读取 git 产物目录中的 `workflow-summary.json.contentFile`
- `AI统计公告内容（仅供参考）` 按 `richText` 类型回写
- 若表格权威字段为空，则 OA 维护记为正常跳过分支，不算执行失败
- 若表格权威字段后来更新，后续触发应继续命中当天同一条 OA 草稿执行幂等更新
- 同日重复执行时，OA 侧只允许 `updated` 或 `noop`

**调用方式**：
```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-daily.ps1 `
  -Username $env:TRADE_PROD_OA_USERNAME `
  -Password $env:TRADE_PROD_OA_PASSWORD
```

仅做凭证预检：
```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-daily.ps1 -Precheck
```

**维护状态**：活跃

---

## browser/export-oa-prod-announcement-history.js

**用途**：登录生产 OA，导出指定时间范围内的历史公告样本，供公告风格校准和复核使用。

**调用方式**：
```powershell
.\.agents\scripts\setup\invoke-node-tool.ps1 node .\.agents\scripts\browser\export-oa-prod-announcement-history.js `
  --username $env:TRADE_PROD_OA_USERNAME `
  --password $env:TRADE_PROD_OA_PASSWORD `
  --start-date '2026-03-01' `
  --end-date '2026-04-13'
```

**维护状态**：试运行

---

## announcement/build_oa_prod_announcement_from_git.py

**用途**：读取 `origin/master` 当天合入记录，提炼生产 OA 公告草稿，并输出复核结果与来源追溯。

**调用方式**：
```bash
python .agents/scripts/announcement/build_oa_prod_announcement_from_git.py \
  --repo-root <workspace-root> \
  --branch origin/master \
  --date 2026-04-14 \
  --out-dir <workspace-root>/archive/agent/oa-prod-announcement-from-git/dry-run
```

**维护状态**：试运行

---

## announcement/reconcile_oa_prod_announcement_with_table.py

**用途**：读取钉钉 `OA系统公告维护 -> 公告管理` 当天记录，对 git 生成稿做权威纠偏，并输出最终建议稿与差异证据。

**调用方式**：
```bash
python .agents/scripts/announcement/reconcile_oa_prod_announcement_with_table.py \
  --repo-root <workspace-root> \
  --draft-file <workspace-root>/archive/agent/oa-prod-announcement-from-git/dry-run/announcement-draft.json \
  --target-date 2026-04-21 \
  --out-dir <workspace-root>/archive/agent/oa-prod-announcement-from-git/dry-run
```

**维护状态**：试运行

---

## browser/run-oa-prod-announcement-from-git.ps1

**用途**：端到端执行“历史公告导出 -> git 合入记录提炼 -> 表格权威纠偏 -> 可选写入 OA”。

**调用方式**：
```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-from-git.ps1 `
  -Username $env:TRADE_PROD_OA_USERNAME `
  -Password $env:TRADE_PROD_OA_PASSWORD `
  -TargetDate '2026-04-14' `
  -DryRun
```

仅保留 git 候选稿时，可额外加：

```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-from-git.ps1 `
  -TargetDate '2026-04-14' `
  -DryRun `
  -SkipTableCorrection
```

**维护状态**：试运行

---

## browser/start-linuxdo-edge-isolated.ps1

**用途**：为 `linux.do` 启动独立 Edge profile，隔离 Cookie / Cloudflare 验证态 / Local Storage，降低默认 Edge profile 反复触发限流的概率。

**调用方式**：
```powershell
.\.agents\scripts\browser\start-linuxdo-edge-isolated.ps1
```

只清理这个独立 profile 下的 `linux.do` 站点数据：
```powershell
.\.agents\scripts\browser\start-linuxdo-edge-isolated.ps1 -ResetLinuxDoSiteData -HeadlessResetOnly
```

**维护状态**：活跃

---

## dws/update-aitable-record.js

**用途**：通过 Node 侧参数组装调用 `dws` 更新 AI 表格记录，解决 PowerShell 直传复杂 JSON 参数时的转义问题。

**调用方式**：
```powershell
.\.agents\scripts\setup\invoke-node-tool.ps1 node .\.agents\scripts\dws\update-aitable-record.js `
  --base-id '<baseId>' `
  --table-id '<tableId>' `
  --record-id '<recordId>' `
  --field-id '<fieldId>' `
  --value '待发布'
```

多行正文建议走文件：
```powershell
.\.agents\scripts\setup\invoke-node-tool.ps1 node .\.agents\scripts\dws\update-aitable-record.js `
  --base-id '<baseId>' `
  --table-id '<tableId>' `
  --record-id '<recordId>' `
  --field-id '<fieldId>' `
  --value-file 'D:\path\to\content.txt'
```

**维护状态**：活跃
