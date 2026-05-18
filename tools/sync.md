# sync — Skill 与配置同步工具

**用途**：从 `upstream/epean-skills/` 拉取 skill 更新，或将 `.agents/runtime/mcp/` 中的 MCP 真源配置同步到其他客户端。（旧 Kiro skill / docs 目录已废弃，不再是同步来源）

**使用场景**：skill 有重要更新后手动触发同步；新机器初始化时同步配置。

**脚本路径**：`.agents/scripts/sync/`

| 脚本 | 作用 |
|------|------|
| `pull-epean-skills.ps1` | 从 epean upstream 拉取 skill 更新 |
| `sync-github-agent-repos.ps1` | 每日双仓统一同步入口：先同步个人 Private 备份库，再同步脱敏架构分享仓库 |
| `sync-github-safe-mirror.ps1` | 将 `.agents/` 可版本化子集以安全线性流程同步到 GitHub Private 镜像 |
| `sync-github-share-architecture.ps1` | 将 `.agents/` 白名单架构/治理/模板内容同步到脱敏架构分享仓库 |
| `sync-agents-to-onedrive.ps1` | 将 `.agents/` 框架资产备份到 OneDrive；默认排除凭证、运行缓存和依赖目录 |
| `sync-mcp-to-cursor.ps1` | 将 `.agents/runtime/mcp/<agent>.mcp.json` 同步到 Cursor |
| `sync-kiro-to-onedrive.ps1` | 已废弃，立即报错，防止继续把 `.kiro` 当真源 |

**维护状态**：活跃

## GitHub 双仓每日同步

统一入口：

```powershell
.\.agents\scripts\sync\sync-github-agent-repos.ps1 -SkipAnonymousPrivacyCheck
```

默认顺序：

1. `trade-cloud-agents`：个人 Private 备份库，沿用安全镜像边界；日常增量，周日全量。
2. `trade-cloud-agents-share`：脱敏架构分享库，只导出白名单内容并阻断敏感命中。

自动化 `daily-agents-safe-mirror-sync` 每天 05:00 调用该统一入口。脚本不写入 token，不把 token 拼进 remote URL；所有内部 Git 调用默认先清空继承到的 `credential.helper`，Windows 下再显式使用 Git Credential Manager，避免项目本地 `.git/config` 的 `credential.helper store` 把 PAT 写回 `%USERPROFILE%\.git-credentials`。若遇到疑似敏感内容、推送冲突、远端不可达或需要强推 / 删历史 / 轮换凭证，会停止并报告。

## OneDrive 备份用法

默认安全备份，不移动当前 `.agents/` 路径：

```powershell
.\.agents\scripts\sync\sync-agents-to-onedrive.ps1
```

默认目标：

```text
<user-home>\OneDrive\agents-framework
```

默认会排除：

- `runtime/local-secrets/*.env`
- `runtime/local-secrets/*.toml`
- `runtime/mcp/codex.mcp.json`
- `runtime/mcp/claude.mcp.json`
- `runtime/mcp/mcp.local.json`
- `runtime/state/`
- `runtime/tmp/`
- `runtime/node-deps/`
- `**/__pycache__/`、`*.pyc`、`*.pyo`
- `upstream/epean-skills/`
- `upstream/`
- `_cleanup/`

如确实要把私有配置也备份到 OneDrive，需显式加 `-IncludeSecrets`。不建议用于团队共享目录。

全量镜像整个 `.agents/`，不排除凭证、运行缓存、依赖和 upstream：

```powershell
.\.agents\scripts\sync\sync-agents-to-onedrive.ps1 -Full
```

## GitHub Private 安全镜像

默认一键同步：

```powershell
.\.agents\scripts\sync\sync-github-safe-mirror.ps1
```

默认执行流程：

1. fresh export `.agents/` 可版本化脱敏子集
2. fresh shallow clone `origin/main`
3. 默认增量比较 sanitized tree 与远端工作树，只应用 changed paths
4. 默认只对 changed paths 做敏感内容扫描；周日、到达全量周期或显式 `-ForceFull` 时改为全量扫描 + 全量 overlay
5. only-if-changed clean commit
6. fast-forward push 到远端 `main`
7. push 后校验远端 SHA、关键入口文件、禁止目录缺席、private 可见性

默认额外排除：

- `upstream-skills/`
- `upstream/codex-feishu-helper/`
- `upstream/epean-skills/`
- `daily-recommend/digests/`
- `daily-recommend/html/`
- `daily-recommend/daily_recommend.log`
- `daily-recommend/translation-cache.json`

默认分支/合并模式：

- 远端只维护 `main` 这条线性安全镜像主线
- 默认基于最新 `origin/main` 新建浅克隆临时集成副本，不复用旧导出仓库历史
- 默认日常走增量快路径；脚本默认每周日自动全量，也保留 7 天全量兜底，并支持手动 `-ForceFull`
- 若 `push` 被拒绝为 non-fast-forward、远端 private 校验不确定、或扫描命中疑似敏感内容，立即停止并报告
- 不自动 force-push、不删除历史、不自动推 review branch

仅做本地集成演练，不推送远端：

```powershell
.\.agents\scripts\sync\sync-github-safe-mirror.ps1 -NoPush
```

参数说明：

- `-StateRoot`：覆盖默认运行目录；默认写入 `<user-home>/.codex/automations/daily-agents-safe-mirror-sync/`
- `-ForceFull`：跳过增量快路径，强制执行一次全量扫描 + 全量 overlay
- `-FullSyncCadenceDays`：自动全量校验周期；默认 `7`，设为 `0` 可关闭周期性全量
- `-WeeklyFullDay`：每周自动全量日；默认 `Sunday`，设为空可关闭按周固定全量
- `-GitHttpSslBackend`：覆盖 git HTTPS TLS backend；Windows 默认使用 `openssl`，传 `default` 可回退 Git 自身配置
- `-GitCredentialHelper`：覆盖内部 Git 调用的 credential helper；Windows 默认使用 `manager`，并且会先清空继承 helper；传 `default` 表示禁用脚本层 helper 注入但仍清空继承 helper
- `-SkipAnonymousPrivacyCheck`：仅在人工确认远端可见性后使用；自动化默认不要跳过

## GitHub 架构分享仓库

个人备份库和分享仓库分开维护：

- `trade-cloud-agents`：个人 Private 备份，只给本人。
- `trade-cloud-agents-share`：同事可读的安全架构分享仓库，可给同事或外部朋友查看 / fork / 提 PR。

分享仓库只从 `.agents/` 真源白名单导出，不从个人备份库复制历史。它保留对同事理解框架有用的项目背景、工具名和流程名，但发布前会替换个人身份、本机路径、内网 IP、真实系统 URL、公司私有域名、服务器日志路径和私人备份库 URL。

同步脚本：

```powershell
.\.agents\scripts\sync\sync-github-share-architecture.ps1
```

默认允许：

- `README.md`
- `contract.md`
- `routing.md`
- `governance/` 中脱敏后的架构 / 治理文档
- `tools/` 中通用工具索引
- `templates/`
- `host-entries/`
- `share-packages/agents-share/`
- `codex/adapter.md`
- `claude-code/adapter.md`

默认禁止：

- `runtime/`
- `archive/`
- `case-studies/`
- `scripts/images/`
- `skills/**/images/`
- 任何截图、附件、Excel/CSV/PDF/DOCX/PPTX
- 任何账号、密码、token、cookie、私钥、连接串
- 个人姓名、个人邮箱、本机用户名路径
- 任何内网 IP、真实系统 URL、公司私有域名、服务器日志路径、服务拓扑、服务器列表

分享仓库协作方式：

- 你保留 `main` 合并权。
- 其他人通过 fork / branch / PR 修改。
- PR 必须通过敏感扫描后才合并。
- 需要回流到本地 `.agents/` 真源时，由你人工挑选，不自动反向同步。
