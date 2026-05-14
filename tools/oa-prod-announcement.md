# oa-prod-announcement — 生产 OA 发版公告维护

**用途**：维护生产 OA 发版公告的完整执行链路，包括：
- 从当天 `master` 合入记录生成公告草稿
- 将确认后的标题/正文同步到钉钉 `OA系统公告维护 -> 公告管理`
- 登录生产 OA 创建或更新当天公告草稿，并在成功后将表格 `OA状态` 回写为 `待发布`

**适用场景**：每日发版公告维护、补录当天公告、发现当天草稿已存在时用最新表格内容覆盖更新、仅整理 `AI统计公告内容（仅供参考）` 的人工复核稿。

**脚本路径**：
- `.agents/scripts/announcement/build_oa_prod_announcement_from_git.py`
- `.agents/scripts/announcement/reconcile_oa_prod_announcement_with_table.py`
- `.agents/scripts/browser/run-oa-prod-announcement-closed-loop.ps1`
- `.agents/scripts/browser/run-oa-prod-announcement-daily.ps1`
- `.agents/scripts/browser/run-oa-prod-announcement-maintain.ps1`
- `.agents/scripts/browser/create-oa-prod-announcement.js`
- `.agents/scripts/dws/update-aitable-record.js`

**默认行为**：
- git 生成阶段优先固化“归类与编排规则”，不是机械按提交数逐条输出
- 若当前任务只是“公告整理 / 参考稿整理 / AI统计公告内容润色”，优先走人工复核链路，不自动推进到 `公告内容` 或 OA 写入
- git 生成阶段默认每次实时查询生产菜单库（`sys_resource + sys_resource_new`）做页面归类
- 模块归类链路固定为：实时菜单库页面证据 > 页面路由/前端文件证据 > 控制层与目录语义证据 > 文案关键词兜底
- 同主题多次提交会尽量合并，避免一页功能拆成多条重复公告
- 新页面优先保留“新增页面”本身；页面内按钮/字段细节默认不重复展开
- 资源权限相关改动在文案末尾补 `（需授权）`
- git 统计稿仅用于复核，不直接作为 OA 写入来源
- git 与表格公共内容冲突时，默认按表格文案覆盖对应 git 项；表格独有项补入推荐稿
- `run-oa-prod-announcement-from-git.ps1` 默认禁止直写 OA；未显式传 `-AllowDirectOaWrite` 会直接阻断
- 表格读取与状态回写统一走 `dws aitable`
- 日常自动化入口对应当前默认调度：工作日 `10:00 / 13:00 / 17:00 / 19:00`
- 日常自动化入口会先确保当天表格记录存在；若缺失，则自动补建一条最小记录：`标题 + 计划上线日期 + OA状态=待创建`
- 每次触发都会先更新 `AI统计公告内容（仅供参考）`，再用表格权威正文维护 OA，最后补做上一条记录 `已发布` 状态核验
- 日常自动化读取 git 预检结果时，优先使用产物目录 `workflow-summary.json`，不直接依赖子脚本 stdout
- `AI统计公告内容（仅供参考）` 当前按 `richText` 回写，调用 DWS 更新 helper 时必须透传字段类型
- 默认接受当天 `待创建` 记录；若已是 `待发布`，允许幂等重跑
- 若当天记录已是 `已发布`，日常自动化不会再新建第二条同日记录
- 生产 OA 登录后直接调用系统自带公告接口，不手搓外部接口
- 当天若不存在草稿：创建 `未发布` 公告
- 当天若已存在唯一草稿：比较标题/日期/富文本内容，不一致则覆盖更新
- 若表格权威字段 `发布内容/公告内容` 为空：跳过 OA 创建/更新，视为日常自动化正常分支
- 若表格权威字段后来更新：后续触发继续命中当天同一条 OA 草稿执行幂等更新
- 同一天重复执行只能命中 `updated` 或 `noop`，不应重复新增第二条同日草稿
- 保存时会按历史公告样式归一化正文：
  源文本空行不写入空白段，正文按“模块标题一段 + 每条公告一段”生成
- 保存后做一次富文本渲染校验，检查换行、空段和特殊字符是否按 `richTextContent` 正常渲染
- 生产 OA 凭证解析优先级（高 → 低）：显式参数 `-Username/-Password` → 环境变量 `$env:TRADE_PROD_OA_USERNAME/PASSWORD` → 本地私有 TOML `.agents/runtime/local-secrets/oa-prod.toml`（不入库）
- 凭证 precheck 由 `Resolve-OaCredential.ps1` 统一处理，输出来源标签，不泄露明文
- 手动 shell 报 `Missing OA credential` 时，先查最近 `archive/agent/oa-prod-announcement-daily/<timestamp>/workflow-summary.json`；若自动化归档已成功，优先按“当前 shell 未继承 runner 凭证”排查
- 菜单库查询凭证优先通过 `$env:OA_MENU_DB_USER` / `$env:OA_MENU_DB_PASSWORD` 注入（支持回退 `TRADE_MENU_DB_*`、`MYSQL_*`）
- 调度入口真源默认位于：`$CODEX_HOME/automations/oa/automation.toml`

**当前推荐链路**：
1. 运行 `build_oa_prod_announcement_from_git.py` 生成当天公告草稿
2. 复核 `announcement-content.txt` 与 `announcement-draft.json`
3. 运行 `reconcile_oa_prod_announcement_with_table.py` 做“表格参考纠偏”
4. 以 `table-correction.json.recommendedContentFile` 作为 OA 写入稿的唯一准入入口（冲突按表格优先，补漏写入 `supplemented-content.txt`）
5. 运行 `run-oa-prod-announcement-maintain.ps1` 或 `run-oa-prod-announcement-closed-loop.ps1`，用表格权威内容维护生产 OA 草稿
6. 检查 `summary.json`、渲染截图和表格状态回查结果

**调用方式**：
```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-maintain.ps1 `
  -Username $env:TRADE_PROD_OA_USERNAME `
  -Password $env:TRADE_PROD_OA_PASSWORD
```

闭环入口示例：

```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-closed-loop.ps1 `
  -Username $env:TRADE_PROD_OA_USERNAME `
  -Password $env:TRADE_PROD_OA_PASSWORD `
  -TargetDate 2026-04-22
```

日常自动化入口示例：

```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-daily.ps1 `
  -Username $env:TRADE_PROD_OA_USERNAME `
  -Password $env:TRADE_PROD_OA_PASSWORD `
  -TargetDate 2026-05-06
```

git 草稿生成示例：

```powershell
python .\.agents\scripts\announcement\build_oa_prod_announcement_from_git.py `
  --repo-root <workspace-root> `
  --branch origin/master `
  --date 2026-04-20 `
  --out-dir <workspace-root>\archive\agent\oa-prod-announcement-from-git\2026-04-20Tdraft
```

表格权威纠偏示例：

```powershell
python .\.agents\scripts\announcement\reconcile_oa_prod_announcement_with_table.py `
  --repo-root <workspace-root> `
  --draft-file <workspace-root>\archive\agent\oa-prod-announcement-from-git\2026-04-20Tdraft\announcement-draft.json `
  --target-date 2026-04-20 `
  --out-dir <workspace-root>\archive\agent\oa-prod-announcement-from-git\2026-04-20Tdraft
```

**配套 runbook**：
- `.agents/knowledge/testing/oa-prod-announcement-runbook.md`

**可选参数**：
- `-TargetDate`：默认当天，格式 `yyyy-MM-dd`
- `-Headful`：前台打开浏览器执行
- `-SkipStatusUpdate`：只创建/更新 OA 草稿，不回写钉钉表格

**产物目录**：
- `archive/agent/oa-prod-announcement/<timestamp>/`
- `archive/agent/oa-prod-announcement-from-git/<timestamp>/`

典型产物：
- `announcement-content.txt`：git 生成的公告正文
- `announcement-draft.json`：git 生成阶段的复核与来源明细
- `announcement-draft.json.menuLookup`：本轮是否命中实时菜单库（含降级原因）
- `table-record.json`：当天表格权威记录
- `authoritative-content.txt`：表格参考稿，仅作对照，不直接覆盖 OA 草稿
- `supplemented-content.txt`：git 主体 + 表格补漏后的推荐稿
- `table-correction.json`：git 与表格的结构化对比结果
- `table-correction.json.tablePreferredItems`：公共冲突中被表格覆盖的条目
- `archive/agent/oa-prod-announcement-closed-loop/<timestamp>/workflow-summary.json`：收口执行摘要（门禁 + 预检 + OA 写入）
- `03-dashboard.png`：登录后的生产 OA 首页截图
- `04-render-preview.png`：保存后富文本渲染预览截图
- `summary.json`：Playwright 执行摘要
- `workflow-summary.json`：整条维护链路摘要

**最小自测清单**：
- 路由应能让“公告整理 / 参考稿整理 / AI统计公告内容润色”命中 runbook 与 review standard
- PowerShell / Python / Node 关键脚本语法检查通过
- `build_oa_prod_announcement_from_git.py` 能对指定日期生成 `announcement-content.txt` 与 `announcement-draft.json`
- `reconcile_oa_prod_announcement_with_table.py` 能基于同一日期输出 `table-correction.json`
- `run-oa-prod-announcement-daily.ps1` 在“当天记录缺失”场景下应先补建最小记录，再继续后续链路
- `run-oa-prod-announcement-daily.ps1` 在“权威字段为空”场景下应把 OA 维护记为正常跳过，而不是异常失败
- 手工修正多行 `richText` 参考字段时，优先 `--value-file`，并对同一 `recordId` 做回查确认

**维护状态**：活跃（2026-04-17 建立 OA 维护链路；2026-04-20 扩展为 git 生成 + 复核 + 样式归一化；2026-04-22 收口为“git仅复核、表格权威写 OA”；2026-05-07 补齐日常自动化参考字段回写、幂等验收口径与凭证上下文排查口径）
