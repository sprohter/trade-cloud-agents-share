# oa-prod-announcement-from-git — 生产 OA 发版公告维护（Git 生成版）

**用途**：从 `origin/master` 当天合入记录提炼公告内容，结合生产 OA 历史公告样本和公告编写规范做基本复核，作为“生成 + 复核 + OA 维护”链路的上游草稿入口。

**适用场景**：每日发版公告草稿生成、人工写公告前的自动初稿、逐步替代“看 git 记录后手工整理公告”的流程。

**脚本路径**：
- `.agents/scripts/browser/run-oa-prod-announcement-from-git.ps1`
- `.agents/scripts/browser/export-oa-prod-announcement-history.js`
- `.agents/scripts/announcement/build_oa_prod_announcement_from_git.py`
- `.agents/scripts/announcement/reconcile_oa_prod_announcement_with_table.py`
- `.agents/scripts/browser/create-oa-prod-announcement.js`

**默认行为**：
- 默认读取 `origin/master --first-parent` 的当天合入记录
- 默认先导出历史公告样本，再做样式校准
- 默认过滤明显技术型和低信号描述
- 默认优先生成草稿，不把“自动写 OA”当成唯一出口
- 默认在 git 草稿生成后自动执行一次“表格参考纠偏”
- 复核通过后才允许进入 OA 写入阶段
- 启用表格参考纠偏时，正式写入优先使用 `table-correction.json.recommendedContentFile`
- `authoritative-content.txt` 仅作表格参考稿，不作为 git 链路默认最终写入稿
- 若该脚本被其他自动化作为子步骤调用，结构化结果优先读取产物目录 `workflow-summary.json`
- 不要直接把 stdout 里的多段 JSON 串当成唯一机器可解析结果
- 正式写入后必须实时回查 OA 当前记录；首页截图仅作辅助证据
- 生产 OA 凭证解析优先级（高 → 低）：显式参数 `-Username/-Password` → 环境变量 `$env:TRADE_PROD_OA_USERNAME/PASSWORD` → 本地私有 TOML `.agents/runtime/local-secrets/oa-prod.toml`（不入库）；由 `Resolve-OaCredential.ps1` 统一处理

**当前推荐链路**：
1. 先用本工具 `-DryRun` 生成草稿
2. 脚本会自动产出 `announcement-draft.json` 和 `table-correction.json`
3. 复核 `announcement-content.txt`、`table-correction.json`、`authoritative-content.txt` 与 `supplemented-content.txt`
4. 若启用表格参考纠偏，以 `recommendedContentFile` 作为本轮唯一推荐稿入口
5. 再决定是否继续写入 OA 草稿

**当前核心规则**：
- 按业务域归类，顺序尽量贴近 OA 菜单
- 模块归类优先使用真实菜单页面/资源菜单证据，其次看页面路由和前端文件，最后才允许关键词兜底
- 同主题多次提交尽量合并
- 新页面优先写“新增页面”，不重复展开页面内天然附带细节
- 授权类页面/按钮统一补 `（需授权）`
- git 是当前 OA 草稿的复核参考来源，不是 OA 写入主体（2026-04-22 已收口）；OA 最终写入一律取表格权威内容，git 统计稿不直接写 OA
- `tableOnlyItems` 中未命中近似项的内容会补入推荐稿
- 启用表格参考纠偏时，`workflow-summary.json.contentFile` 必须等于 `table-correction.json.recommendedContentFile`
- `summary.json.saveSuccess=true` 不是最终验收结论，还必须看 OA 实时回查结果
- 最终 OA 正文按“模块标题一段 + 每条公告一段”落库，不额外写模块间空白段

**调用方式**：
```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-from-git.ps1 `
  -Username $env:TRADE_PROD_OA_USERNAME `
  -Password $env:TRADE_PROD_OA_PASSWORD `
  -TargetDate '2026-04-14' `
  -DryRun
```

如需仅生成 git 候选稿、暂不读取表格：

```powershell
.\.agents\scripts\browser\run-oa-prod-announcement-from-git.ps1 `
  -TargetDate '2026-04-14' `
  -DryRun `
  -SkipTableCorrection
```

**配套 runbook**：
- `.agents/knowledge/testing/oa-prod-announcement-from-git-runbook.md`

**配套复核标准**：
- `.agents/knowledge/testing/oa-prod-announcement-review-standard.md`

**产物目录**：
- `archive/agent/oa-prod-announcement-from-git/<timestamp>/`

典型产物：
- `history-samples.json`
- `announcement-content.txt`
- `announcement-draft.json`
- `table-record.json`
- `authoritative-content.txt`
- `supplemented-content.txt`
- `table-correction.json`
- `workflow-summary.json`
- `summary.json`（非 dry-run）

`announcement-draft.json.items[*]` 当前会额外输出 `classificationEvidence`，用于说明每条公告项是被哪类菜单/页面证据归到当前模块的。

**凭证注入**：
- OA 登录凭证由 `shared\Resolve-OaCredential.ps1` 统一解析，优先级：显式参数 > 进程环境变量 > 用户/机器级环境变量 > 本地私有 TOML（`.agents\runtime\local-secrets\oa-prod.toml`）
- precheck 仅输出来源标签，不泄漏密码明文
- 菜单库凭证另行注入，详见主 Runbook §4.2

**维护状态**：试运行（2026-04-20 建立 Git 生成链路；2026-04-22 收口为”git 仅复核、表格权威写 OA”；2026-04-23 补齐凭证注入说明）
