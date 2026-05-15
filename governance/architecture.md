# 框架结构设计与长期演进规划

> 记录 `.agents/` 框架的结构设计决策和长期演进方向。
> 与 `migration-log.md` 的分工：本文件记录"为什么这样设计"，migration-log.md 记录"做了什么"。

---

## 设计原则

1. **框架为业务服务，不为自身服务**：skills/knowledge/tools 是核心层，governance/collab/适配是支撑层。支撑层保持最薄可用，核心层逐步做厚
2. **先驻点后完善**：业务流程先沉淀下来（哪怕粗糙），成熟后再调整完善。不在治理层过度设计中等待业务层完美
3. **共享内容 agent 无关**：`skills/`、`knowledge/`、`governance/` 对所有 agent 开放，不绑定任何特定 agent
4. **每个 agent 自包含**：每个 agent 有独立的一级文件夹，包含其所有专属内容，迁移或引用时以文件夹为单位
5. **根层只放通用资产**：`contract.md`、`routing.md`、`templates/`、各 agent 文件夹、共享层
6. **治理文件集中在 governance/**：所有框架自管理规则（沉淀、纠偏、资产登记、架构决策）统一放此层
7. **可分享 / 可移植优先**：`.agents/` 设计为可整体拷贝的框架资产；路径、脚本、模板和配置说明优先使用相对路径或占位符，不把个人本机绝对路径写成团队标准
8. **多宿主兼容优先**：新增或修改架构流程、配置、脚本、入口时，默认同时检查 Codex 桌面 / Codex CLI / Claude Code；不能只让当前使用的宿主可用
9. **跨平台优先，Windows 兜底**：新增脚本和路径解析默认考虑 Windows / macOS / Linux；如果短期难以兼容，必须明确标注“Windows 优先 / Windows-only”以及非 Windows 的替代方式
10. **仓库卫生优先**：`.agents/` 同时是框架真源和本地运行空间，必须把真源、运行产物、缓存、凭证、云同步临时文件分开；仓库卫生失败时优先修机制，再继续沉淀
11. **影响评估先于治理调整**：框架级治理不是普通文案修改。凡触及入口、路由、流程主链路、配置真源、脚本执行路径、仓库边界，必须先评估影响面，再决定是否调整

---

## 治理哲学：Harness Engineering（2026-04-16）

> **Harness = 收紧，防止脱离目的。** 不是最大约束，而是恰好足够的结构性约束。

### policy 与 mechanism 的区别

| | 规则（Policy） | 约束（Mechanism） |
|--|---------|------------|
| 本质 | 写下来的要求，靠执行者自觉 | 结构上让偏离变难，靠系统强制 |
| 失效条件 | 执行者忽略规则 | 需要改变结构本身才能绕过 |
| 例子 | "必须先读 routing.md"（旧 CLAUDE.md） | ROUTING GATE 放在 CLAUDE.md 最顶部（新） |

写规则不等于建约束。规则可以被跳过，约束需要主动破坏才能绕过。

### 两层设计

框架的约束分两层，是独立问题，解决方式不同：

**Layer 1：入口门（Entry Gate）**
- 目标：确保 agent 进入框架，不走旁路
- 手段：结构性约束（ROUTING GATE 在 CLAUDE.md 最顶部；steering `inclusion: always`）
- 判断标准：agent 是否在执行任务前读了 routing.md

**Layer 2：内部精度（Internal Accuracy）**
- 目标：agent 进了框架后能找到正确的 skill/knowledge 文件
- 手段：routing.md 文件级触发词 + 跨领域场景路由
- 判断标准：routing.md 中是否有对应触发词能命中目标文件

两层失效的根因不同——Layer 1 失效是入口问题，Layer 2 失效是路由精度问题，不能混为一谈。

### 入口文件薄指针原则

Claude Code 的 `MEMORY.md`，以及 Codex 桌面 / Codex CLI 的 `AGENTS.md` + steering 薄指针，构成各自的会话入口层，应遵守：

- **只存指针和本机特有事实**，不存可以查文档的内容
- **内容本体全在 `.agents/`**，入口文件只做导航
- **多宿主对称**：结构相同，内容来自同一个 `.agents/` 源

对 Codex 桌面与 Codex CLI 进一步约束：

- `<user-home>\.codex\AGENTS.md` 只保留全局偏好，不写项目知识
- 宿主项目 `AGENTS.md` 只保留项目入口约束，不写知识正文
- `.kiro/steering/core/agent-framework.md` 只保留薄指针，不承担候选池职责

违反此原则的症状：MEMORY.md / AGENTS / steering 开始堆内容 → 和 `.agents/` 产生重复 → 两者不同步 → 维护成本翻倍。

---

## 可分享与跨平台兼容原则

`.agents/` 不只是当前机器的个人工作目录，也是一份可以拷贝到其他设备、交给其他 agent 读取的框架资产。

### 路径书写规则

| 场景 | 推荐写法 | 避免写法 |
|------|----------|----------|
| `.agents` 内部文档互相引用 | `.agents/skills/...`、`skills/...`、`<agents-root>/skills/...` | `<workspace-root>\.agents\skills\...` |
| 项目根入口 | `<workspace-root>/AGENTS.md`、`<workspace-root>/CLAUDE.md` | 当前机器固定盘符路径 |
| 用户级配置 | `<user-home>/.codex/config.toml`、`<user-home>/.claude/...` | `<user-home>\...` |
| 脚本内定位自身依赖 | `$PSScriptRoot`、`__dirname`、`Path(__file__).resolve()`、从 cwd 向上查找 `.agents/` | 写死 `<workspace-root>` |
| 分享包示例 | `~/team-assets/agents-share/`、`D:\team-assets\agents-share\` 作为示例 | 把示例路径写成唯一标准 |

### 宿主固定入口边界

有些文件路径由宿主固定发现，不能完全移进 `.agents/`，例如根目录 `AGENTS.md`、`CLAUDE.md`、`.mcp.json`、`.kiro/settings/mcp.json`、用户级 `~/.codex/config.toml`。这些文件的治理口径是：

- `.agents/` 内保存模板、真源说明、安装 / 同步脚本
- 宿主固定路径上的文件是安装产物、镜像或本机配置
- 迁移到新设备时，优先从 `.agents/host-entries/`、`.agents/runtime/mcp/`、`.agents/runtime/local-secrets/` 重建本机入口
- 不把宿主固定路径里的本机绝对路径反向写成 `.agents/` 框架标准

### 脚本兼容规则

1. 新增脚本优先用跨平台运行时：Node.js / Python / PowerShell 7。
2. Windows PowerShell 脚本可以作为主入口，但如果用于团队分享或跨设备迁移，索引卡必须标明平台要求。
3. 脚本调用其他文件时优先相对脚本自身定位，不依赖调用者当前目录。
4. 必须依赖本机路径、系统定时任务、浏览器配置、MCP 宿主路径时，在 `runtime-configs.md` 或工具索引卡中明确写成“本机安装项 / 可重建项”。
5. 如果短期只能支持 Windows，允许先 Windows 优先，但必须留下 macOS / Linux 的降级说明或待办，不假装已经跨平台。

---

## 当前结构（v2，2026-04-15）

```
.agents/
├── README.md                        # 框架导航，任何 agent 必读
├── contract.md                      # 通用行为准则（所有 agent 共用）
├── routing.md                       # 统一任务路由表（唯一来源）
├── templates/                       # 新 agent 接入模板
│   └── agent-config.template.md
│
├── claude-code/                     # Claude Code 专属
│   ├── adapter.md                   # 能力清单、框架读取方式、局限与补偿、执行规范
│   └── runtime-configs.md           # Claude Code 运行时配置资产索引
│
├── codex/                           # Codex 专属
│   ├── adapter.md                   # 能力清单、框架读取方式、局限与补偿
│   └── runtime-configs.md           # Codex 运行时配置资产索引
│
├── skills/                          # 操作技能层（唯一权威来源，2026-04-16 起）
├── knowledge/                       # 项目知识层（按需积累）
│   ├── testing/
│   ├── business/
│   └── infrastructure/
├── governance/                      # 治理层
│   ├── architecture.md              # 本文件：结构设计与演进规划
│   ├── sedimentation.md             # 沉淀规则
│   ├── self-correction.md           # 纠偏规则
│   ├── asset-registry.md            # 资产目录
│   └── migration-log.md             # 迁移进度记录
│
├── upstream/                        # 部门共享 skill 仓库（只读）
│   └── epean-skills/
├── collab/                          # 多 agent 协作协议
└── archive/                         # 废弃/待验证内容暂存
```

---

## Agent 文件夹规范

新增 agent 时，在 `.agents/` 根层创建以 agent 名称命名的文件夹，包含：

| 文件 | 必须 | 说明 |
|------|------|------|
| `adapter.md` | ✅ | 能力清单、框架读取方式、执行规范、已知局限与补偿 |
| `runtime-configs.md` | ✅ | 该 agent 的运行时配置资产索引（路径、敏感级别、维护规则） |
| 其他专属文件 | 按需 | 如脚本、内存快照、专属模板等 |

`adapter.md` 必须包含的内容：
- 能力清单（跨会话记忆、工具调用、MCP、沉淀候选池）
- 框架文件读取方式（如何加载 contract.md 和 routing.md）
- 任务执行规范（工具链调用顺序、环境确认等）
- 已知局限与补偿措施
- 版本记录

---

## 历史结构（v1，2026-04-14，已废弃）

v1 使用 `adapters/` 层管理 agent 适配：
```
adapters/
  active/claude-code.md     # agent 适配说明（各 agent 混放）
  active/codex.md
  assets/runtime-configs.md # 所有 agent 配置资产合并在一张表
  templates/                # 接入模板
```

**废弃原因**：
- 每个 agent 内容分散在 `active/` 和 `assets/` 两处，无法作为独立单元引用或迁移
- `runtime-configs.md` 混合了所有 agent 的配置，增加新 agent 时维护成本高

`adapters/` 目录保留但标记为 deprecated，内容已迁移到各 agent 专属文件夹。

---

## 结构变更操作规范

**结构变更（新增/删除/移动文件或目录）时，必须执行以下检查清单，不得在关闭任务前跳过：**

| 步骤 | 动作 |
|------|------|
| 1. 引用路径审计 | 在 `.agents/` 全目录 grep 旧路径，确认所有引用已更新 |
| 2. 资产目录同步 | 更新 `governance/asset-registry.md` 反映新增/删除/迁移条目 |
| 3. 路由表同步 | 如路由路径变更，同步更新 `routing.md` |
| 4. README 依赖索引同步 | `README.md` 的文件依赖索引反映最新结构 |
| 5. deprecated 标记 | 被替代的文件加 deprecated 标记，不直接删除 |
| 6. 多宿主兼容性审计 | 检查 Codex 桌面 / Codex CLI / Claude Code 的 adapter、runtime-configs、host entries 与宿主镜像是否同步 |
| 7. 仓库卫生审计 | 确认运行产物、缓存、导出文件、`node_modules/`、`*.baiduyun.uploading.cfg` 没有进入活跃树或 `.git/objects` |
| 8. 影响评估 | 判断本次结构变更影响上层入口、中层路由、流程主链路、脚本执行路径、MCP/凭证、团队分享包或历史案例复用中的哪些部分 |
| 9. 可分享 / 跨平台审计 | 确认新增活跃文档、模板和脚本没有把当前机器绝对路径写成标准；必要时用 `<workspace-root>`、`<agents-root>`、`<user-home>` 占位 |

> **触发时机**：任何涉及文件创建、移动、重命名、删除的操作完成后，在关闭任务前必须执行。
>
> **本规范来源**：2026-04-15 adapter 层 v2 重组后，发现遗漏引用路径审计步骤，事后才被用户发现。将此缺口补入架构规范，防止再次发生。

### 多宿主兼容性检查清单

当新增或修改以下内容时，必须执行本清单：架构流程、治理规则、运行时配置、MCP 配置、工具脚本、宿主入口、agent 适配说明、团队分享包接入规则。

**概念边界**：

- Codex 桌面 / 本会话与 Codex CLI 通常共享 OpenAI Codex 服务能力和 `~/.codex` 用户级配置，但不是同一个运行宿主。
- Codex 桌面、Codex CLI、Claude Code 的模型服务可以相同或不同；兼容性检查关注的是入口发现、工作目录、权限、MCP 配置、脚本调用和输出路径是否一致。
- 因此“Codex 兼容”不能自动代表“Codex CLI 兼容”。涉及命令行调用、环境变量、终端 cwd、配置文件、MCP、脚本路径时，必须把 Codex CLI 单独列入检查。

| 检查项 | 必查文件 / 路径 | 通过标准 |
|------|----------------|---------|
| Codex 桌面适配 | `.agents/codex/adapter.md`、`.agents/codex/runtime-configs.md` | 能从 Codex 桌面入口读到同一 `.agents/` 真源；新增配置资产已登记 |
| Codex CLI 适配 | `AGENTS.md`、`~/.codex/config.toml`、`.agents/codex/runtime-configs.md`、相关脚本调用方式 | 在命令行 cwd 为项目根时，能读取同一 `.agents/` 真源；脚本、MCP、输出路径不依赖桌面专属能力 |
| Claude Code 适配 | `.agents/claude-code/adapter.md`、`.agents/claude-code/runtime-configs.md` | 能从 Claude Code 入口读到同一 `.agents/` 真源；新增配置资产已登记 |
| 宿主入口模板 | `.agents/host-entries/AGENTS.md`、`.agents/host-entries/CLAUDE.md` | 根目录入口只做薄指针；模板与生成结果一致 |
| 宿主固定镜像 | 根目录 `AGENTS.md`、`CLAUDE.md`、`.kiro/settings/mcp.json`、`.mcp.json` | 只承担发现入口或镜像职责，不承载新真源 |
| MCP / runtime | `.agents/runtime/`、`.agents/runtime/mcp/codex.mcp.json` | 真源在 `.agents/runtime/`；宿主路径只镜像或引用 |
| 工具脚本 | `.agents/scripts/`、`.agents/tools/` | 脚本本体和索引卡都在 `.agents/`，各宿主说明不指向旧路径；CLI 调用方式不依赖桌面-only 能力 |
| 跨平台迁移 | adapter、runtime-configs、templates、tools 索引卡、share-packages | 活跃说明使用相对路径或 `<workspace-root>` / `<agents-root>` / `<user-home>`；脚本明确 Windows / macOS / Linux 支持范围 |

最小验证建议：

1. 运行 `.\.agents\scripts\setup\install-agent-host-entries.ps1 -Check`
2. 运行 `.\.agents\scripts\setup\test-agents-framework-integrity.ps1`
3. 对新增或修改的入口路径执行一次 grep，确认没有旧 `.kiro/skills/`、根 `scripts/` 等误导性真源引用

### 默认搜索排除

日常路由、结构影响面和治理自检的全文搜索，默认只扫活跃真源，避免把运行态和历史报告反复读入上下文：

- 默认排除：`.agents/runtime/state/`、`.agents/collab/sessions/`、`.agents/archive/`、`.agents/tmp/`、`.agents/outputs/`、历史完整报告。
- 需要证据时可以定点读取上述目录中的具体文件，不做无目标大范围全文读取。
- 新增 `knowledge/` 或 `governance/` 内容前，仍遵守 `routing.md` 的 archive 写入前检查规则；此时只按关键词查 archive，提取摘要或证据，不把整批历史归档放进上下文。
- 自检脚本默认输出摘要，完整 JSON 写入 `.agents/runtime/state/trade-cli/`；日常排查优先看最新摘要或指定失败项。

---

## 框架级治理影响评估

框架级治理变更分三层评估，不能直接把“发现问题”跳到“批量改文档”。

### 影响层级

| 层级 | 典型文件 / 内容 | 风险 |
|------|----------------|------|
| 上层入口 | `AGENTS.md`、`CLAUDE.md`、`.kiro/steering/core/agent-framework.md`、`.mcp.json`、`.kiro/settings/mcp.json` | 入口失效会让 agent 根本进不了框架，或读错真源 |
| 中层路由与治理 | `routing.md`、`contract.md`、`governance/*.md`、`asset-registry.md` | 路由偏差会让任务走错 skill；治理规则偏差会反复扩大影响 |
| 流程主链路 | `knowledge/testing/methods.md`、`skills/workflows/`、关键 runbook / tool index | 主流程被改偏会影响日常功能测试、线上反馈处理、回归、公告等连续工作 |
| 执行与运行时 | `.agents/scripts/`、`.agents/runtime/`、MCP 配置、local-secrets 示例 | 脚本或配置失配会直接导致功能不可用 |

### 调整前必答

1. 本次变更影响的是哪一层，是否跨层？
2. 受影响的宿主有哪些：Codex 桌面、Codex CLI、Claude Code、团队分享包？
3. 是否改变默认入口、默认路由、默认流程顺序、脚本路径、配置真源或仓库边界？
4. 是否影响 `.agents/` 整体拷贝到其他设备后的可用性，是否引入当前机器绝对路径？
5. 是否会让历史案例、runbook、工具索引、MCP 镜像出现旧口径？
6. 是否需要先做小范围验证或分批提交，而不是一次性大改？
7. 是否值得在实施前做一轮 Claude/Codex 交叉复核，并在实施后做结果复核？

### 调整后必验

1. 路由与入口：`install-agent-host-entries.ps1 -Check` + `test-agents-framework-integrity.ps1`
2. 影响面 grep：对旧路径、旧流程名、旧真源口径做活跃目录扫描
3. 多宿主验证：至少确认 Codex 桌面 / Codex CLI / Claude Code 的入口说明不冲突；涉及 CLI 脚本时实际跑最小命令
4. 仓库卫生：确认运行产物、缓存、云同步临时文件、真实凭证没有进入提交边界
5. 可分享 / 跨平台验证：确认活跃模板、adapter、runtime-configs、工具索引卡不把本机绝对路径写成标准；脚本标明平台支持范围
6. 变更记录：如果改变了上层入口、中层路由或流程主链路，必须写 `migration-log.md`，说明背景、影响面、验证结果和回滚口径

### 架构级交叉验证建议

当变更命中以下任一场景时，默认建议增加 Claude/Codex 交叉验证，而不是只靠单侧实现后自证：

- CLI 入口或命令契约调整
- 治理规则、路由规则、默认流程顺序调整
- MCP 路径、宿主镜像、用户级配置读取口径调整
- collab / handoff / session 相关机制调整

推荐顺序：

1. 实施前：用 Claude/Codex 复核最小改动集、影响宿主、验证清单
2. 实施中：优先保留命令输出、自检结果、本地草稿等证据
3. 实施后：用回归命令、自检脚本和必要的第二模型复核结果做交叉确认

外部 reviewer 不可用时：

- 如果 Claude CLI、Codex CLI 或其他外部 reviewer 超时、登录受阻、交互失败，不把主任务卡死
- 可改用 2 个边界清晰的子 agent 并行复核：一个看实现 / 安全边界，一个看测试 / 文档 / 运行态
- 子 agent 只读优先，输出必须包含结论、证据、风险和建议；主 agent 不直接照单全收，负责整合、取舍、执行和最终验证
- 这种替代复核要在最终结论里说明，避免把“未完成 Claude 复核”误写成“Claude 已通过”

边界：

- 这是治理建议，不是 CLI 强制阻断逻辑
- 简单查询、单点文案修正、一次性本地草稿不要求走完整交叉验证

---

## 仓库卫生与云同步边界

`.agents/` 可以整包同步到 OneDrive / 百度云等工具，但 `.agents/.git` 与主仓库 `.git` 不应被会写入临时文件的云同步进程实时同步。

### 分层规则

| 层 | 允许入库 | 禁止入库 |
|----|----------|----------|
| 真源层 | `skills/`、`knowledge/`、`governance/`、`tools/`、`templates/`、`runtime/mcp/*.json` | 真实凭证、运行缓存、导出文件 |
| 运行层 | `runtime/state/README.md`、`.gitignore`、示例配置 | 真实 state、报告、截图、xlsx、临时 JSON |
| 依赖层 | `package.json`、`package-lock.json`、脚本源码 | `node_modules/` |
| 清理层 | `_cleanup/README.md` 可按需留痕 | 大量备份、云同步残留、Git object 备份 |

### 强制规则

1. `.agents/.git` 与主仓库 `.git` 下不得出现 `*.baiduyun.uploading.cfg`；出现即视为 P1 仓库卫生问题
2. `reports/`、`outputs/`、`tmp/`、`**/__pycache__/`、`runtime/mcp-servers/**/node_modules/`、`*.baiduyun.uploading.cfg` 必须被 `.agents/.gitignore` 忽略
3. 自检报告、调试 JSON、脚本输出默认写入 `.agents/runtime/state/` 或外部临时目录，不参与活跃文档扫描；`runtime/state` 中的云同步临时文件属于运行态噪音，不应提交，但不等同于真源层污染
4. 大范围治理变更应拆分提交：入口/规则、skill 修复、运行时配置、清理动作尽量分开，便于回溯问题来源
5. 如果云同步进程持续写入 `.agents` 或主仓库，不能只反复删除临时文件；应调整同步策略，至少排除 `.agents/.git` 与主仓库 `.git`，必要时改为同步无 `.git` 的镜像包

---

## 内部治理基线与外置候选方案（2026-05-13）

### 背景

2026-05-13 盘点显示，`.agents/` 约 738 MB，其中 `runtime/` 约 603 MB；`runtime/state/` 约 417 MB，主要来自任务截图 / 报告 / GitHub 安全镜像临时工作树；`runtime/mcp-servers/` 约 173 MB，主要来自可重建的 `node_modules/`。宿主项目根层还存在 `.agents-worktrees/` 约 318 MB、`_cleanup/` 约 264 MB、`archive/` 约 97 MB、`outputs/` 约 21 MB。

结论：主目录膨胀的核心不是框架真源，而是运行现场、一次性调试数据、可重建依赖、临时 worktree、纯数据资源和历史归档混放。后续治理目标先按“内部治理优先”处理：让 `.agents/` 内部形成真源、热状态、冷归档、可清理候选的清晰边界；只有当内部 TTL、ignore、索引和清理机制仍不能控制体积、同步风险或敏感边界时，再把外置工作区作为候选方案，并在实施前单独确认。

### 标准分层

| 层 | 内部基线 / 候选位置 | 放什么 | 不放什么 |
|----|----------|--------|----------|
| 框架真源层 | `.agents/` | `README.md`、`contract.md`、`routing.md`、`skills/`、`knowledge/`、`governance/`、`templates/`、脚本源码和工具索引 | 任务截图、批量导出、临时 clone、真实凭证、大型 raw 数据 |
| 本机配置层 | `.agents/runtime/local-secrets/`、`.agents/runtime/mcp/` | 本机可重建配置、私有凭证、MCP 模板与本机镜像 | 纯数据资源、任务证据包、长期历史报告 |
| 热运行层 | `.agents/runtime/state/` | 最近一次或少量必要缓存、摘要 JSON、handoff 草稿、工具自检报告 | 大批截图、重复 Git 工作树、长期证据包、可重建导出目录 |
| 内部冷运行 / 清理候选层 | `.agents/runtime/state/artifacts/`、`.agents/temp/`、根 `_cleanup/` | 短期证据包、待确认删除内容、兼容旧脚本的历史产物 | 无期限堆放的大文件、可重建 clone、未登记的敏感数据 |
| 项目外工作区候选 | `<agent-external-root>/` | 体积或风险超过内部治理能力后的任务产物、临时 worktree、GitHub 安全镜像中间目录、纯数据资源库、冷归档暂存 | 框架真源、必须随 `.agents/` 迁移的文档正文；未确认前不作为默认落点 |
| 安全镜像层 | GitHub Private sanitized mirror | 脱敏后的框架可版本化子集 | `runtime/state/`、`archive/`、`case-studies/`、真实凭证、外部工作区 |

`<agent-external-root>` 是候选占位符，不写死当前机器路径。只有决定采用外置方案时，才在不属于宿主 Git 工作区、不会实时同步 `.git` 的本机目录下创建，例如 `<non-repo-root>/agent-workspace/trade_cloud`。脚本读取顺序建议为：显式参数 > `AGENTS_EXTERNAL_ROOT` 环境变量 > `.agents/runtime/local-secrets/paths.local.json` > 兼容旧路径。

### 若选择外置，建议目录结构

```text
<agent-external-root>/
├── README.md
├── registry/                 # 外部资产索引和 manifest 汇总
├── runtime-state/            # 可丢弃缓存；用于替代过大的 runtime/state 子树
├── task-artifacts/           # 任务截图、日志、抓包、xlsx/json/html 导出
│   └── <yyyy>/<task-id>/<run-id>/
├── worktrees/                # 临时 Git worktree / smoke 现场
├── safe-mirror/              # GitHub 安全镜像 fresh export / shallow clone
├── data-resources/           # 纯数据资源资料
│   ├── raw/                  # 原始资料，只读保存
│   ├── normalized/           # 清洗后的可复用数据
│   ├── references/           # 外部资料、接口文档、样例包
│   └── indexes/              # 轻量索引；正文仍在外部资源目录
├── cleanup-hold/             # 待人工确认删除的大文件和历史现场
└── logs/                     # 本机自动化 / 后台任务日志
```

### 内部优先与外置触发规则

| 内容类型 | 内部治理基线 | 外置触发条件 |
|----------|--------------|----------------|
| 一次性调试截图、抓包、导出 Excel、完整 JSON | `.agents/runtime/state/artifacts/` 或根 `archive/agent/` 兼容保留，登记摘要和清理期限 | 单任务证据包持续膨胀、需要跨多轮保留原始大文件、或云同步 / 搜索成本明显失控 |
| 纯数据资源资料、样例数据、平台文档包、大型接口导出 | `.agents/knowledge/` 只留解释和索引；原始包可先放 `.agents/temp/` 或 `_cleanup/` 候选区 | 资料本身长期有用但体积大、更新频率低、无需随 `.agents/` 迁移 |
| GitHub safe mirror 中间工作树、shallow clone、sanitized export | `.agents/runtime/state/github-*` 兼容保留，增加只保留最近成功和当前运行的清理策略 | 多个临时 clone 反复出现且可重建，内部清理仍不能控制体积 |
| 临时 worktree、smoke 现场、提交验证副本 | `.agents-worktrees/` 兼容保留，任务结束后默认清理或登记保留原因 | worktree 数量或体积长期增长，且不应压在项目根目录 |
| 可删除但暂需人工确认的大文件 | 根 `_cleanup/` 或 `.agents/temp/`，README 登记原路径、原因和保留期限 | `_cleanup/` 自身长期过大，且需要独立冷清理缓冲区 |
| 小型最新缓存、handoff 草稿、工具自检 JSON | `.agents/runtime/state/` 原地保留，按 TTL 或覆盖策略控制 | 通常不外置 |
| 可重建依赖 `node_modules/` | 原位置兼容保留，靠 `.gitignore` 和安装说明治理 | 只有脚本支持外部依赖缓存且收益明确时再迁移 |

### 外置资产 manifest

如果选择外置，并且资产需要后续复查，放入 `<agent-external-root>/task-artifacts/`、`data-resources/`、`safe-mirror/` 的目录应保存一个轻量 `manifest.json` 或在 `registry/` 登记：

```json
{
  "id": "task-xxxxx-20260513-150000",
  "category": "task-artifact",
  "source": "script-or-manual",
  "environment": "test|staging|uat|prod|local",
  "createdAt": "2026-05-13T15:00:00+08:00",
  "owner": "agent-or-user",
  "sensitive": "none|internal|production-data|secret-adjacent",
  "retention": "7d|30d|90d|keep-index-only",
  "summaryPath": ".agents/runtime/state/...",
  "externalPath": "<agent-external-root>/task-artifacts/..."
}
```

原则：无论内部还是外置，`.agents/` 都应沉淀可复用理解、摘要和索引；若外置，则外部资产必须能通过 manifest 找到来源、敏感级别、保留期限和摘要。

### 保留期限建议

| 类型 | 默认期限 | 到期动作 |
|------|----------|----------|
| smoke / tmp / 一次性验证现场 | 3-7 天 | 删除或移入 `cleanup-hold/` 等人工确认 |
| 任务证据包、截图、抓包、导出数据 | 30 天 | 保留摘要，删除原始大文件；重大问题可延长到 90 天 |
| UAT / staging 真实数据验证证据 | 90 天 | 脱敏摘要进 `case-studies/` 或 `knowledge/`，原始数据外部冷归档或删除 |
| GitHub safe mirror 中间目录 | 保留最近 1 次成功 + 当前运行 | 成功后清理旧 export / clone |
| 纯数据资源资料 | 按索引有效期 | 资源仍被引用则保留；失效后只留索引和淘汰原因 |
| `_cleanup/` / `cleanup-hold/` | 30-90 天 | 人工确认后删除；不作为永久 archive |

### 决策顺序

1. 先内部治理：补 ignore、分类、TTL、摘要索引、dry-run 清理清单，不移动目录。
2. 再评估效果：重点看 `runtime/state/github-*`、`runtime/state/artifacts/agent/*`、根 `outputs/`、`.agents-worktrees/` 是否能靠内部清理收住。
3. 如果内部治理足够，就继续保留内部结构，不启用 `<agent-external-root>`。
4. 如果内部治理不足，再按体积、敏感性、复用价值和脚本兼容性列外置候选清单，确认后迁移。
5. 自动清理或外置迁移再工具化：如后续新增计划任务、watchdog 或定时清理脚本，必须静默运行、失败写日志，不弹出控制台窗口。

### 风险边界

- 不把真实凭证、cookie、storage-state、生产数据明细放入可同步的资源区；这类内容必须标记敏感，默认短期保留。
- 不用符号链接 / junction 伪装真源迁移，除非脚本已明确支持并在 `runtime-configs.md` 或工具索引卡登记。
- 不直接删除旧目录；先做引用扫描和脚本兼容检查，必要时保留只读 fallback。
- 不把当前机器绝对路径写成团队标准；文档使用 `<agent-external-root>`，本机路径只放 `local-secrets` 或本机说明。
- 若选择外部工作区，它不参与 GitHub 安全镜像，不作为团队共享真源。

---

## GitHub 安全导出与多设备同步方案

本方案用于把 `.agents/` 的可版本化框架内容提交到个人 GitHub Private 仓库，作为个人备份和 Windows / macOS 新设备启动入口。GitHub 远端是**脱敏安全镜像**，不是本机运行态真源。

### 目标

1. 保留可复用的框架能力：入口、路由、治理、skills、knowledge、模板、工具索引和脱敏运行时模板。
2. 支持不同设备快速恢复：新设备 clone 后，按模板重建宿主入口、MCP 配置和本机凭证。
3. 阻断敏感内容外传：不把真实 token、密码、MCP 私有配置、运行缓存、会话、内部案例和临时脚本产物推到 GitHub。

### 仓库分层

| 层 | 位置 | 职责 | 是否进入 GitHub 安全导出 |
|----|------|------|---------------------------|
| 本地真源层 | `<workspace-root>/.agents/` | 日常维护和 agent 读取的完整框架 | 只导出可版本化子集 |
| 本地运行层 | `.agents/runtime/local-secrets/`、`.agents/runtime/state/`、`.agents/collab/sessions/` | 本机凭证、缓存、会话和临时产物 | 否，仅保留 README / example / template |
| 安全导出层 | `.agents/runtime/state/github-safe-export/` 或同类临时目录 | 生成待提交的脱敏镜像 | 否，属于运行态临时目录 |
| GitHub 私有镜像 | `https://github.com/<owner>/<private-agents-backup>` | 个人备份、跨设备 bootstrap、远端校验 | 是，仅保存脱敏内容 |

### 安全导出边界

| 类型 | 默认处理 | 说明 |
|------|----------|------|
| `README.md`、`routing.md`、`contract.md` | 允许导出 | 框架入口和通用规则 |
| `skills/`、`knowledge/`、`governance/`、`templates/`、`prompts/`、`host-entries/`、`tools/` | 允许导出 | 需要先确认不含真实凭证、内网敏感拓扑和未脱敏案例数据 |
| `codex/`、`claude-code/` | 允许导出 | 仅保存适配说明和配置索引，不保存用户级真实配置 |
| `runtime/mcp/mcp.template.json`、`runtime/README.md`、`runtime/local-secrets/*.example` | 允许导出 | 只导出模板和说明 |
| `runtime/local-secrets/*.env`、`runtime/local-secrets/*.toml` | 禁止导出 | 本机凭证真源，只能留在本机 |
| `runtime/mcp/codex.mcp.json`、`runtime/mcp/claude.mcp.json`、真实 `.mcp.json` 镜像 | 禁止导出 | 可能包含本机路径、服务端点或私有参数 |
| `runtime/state/`、`runtime/tmp/`、`collab/sessions/`、`tmp/`、`outputs/`、`reports/` | 禁止导出 | 本地运行态、缓存和任务输出 |
| `archive/`、`case-studies/` | 默认禁止导出 | 可能含历史现场、截图、业务数据；确需共享时必须单独脱敏评审 |
| `scripts/**/node_modules/`、`runtime/mcp-servers/**/node_modules/`、`**/__pycache__/`、`*.pyc`、`*.pyo` | 禁止导出 | 可重建依赖和运行缓存，不进入框架镜像 |

补充口径：

- 即使文件位于 `skills/`、`knowledge/`、`scripts/`、`upstream*` 等默认允许导出目录，只要包含真实密码、token、私钥、cookie、临时 worktree 或其他本机运行态痕迹，仍必须按文件级或子目录级排除，或先修正本地真源再导出。
- `scripts/runtime/`、站点登录探针、一次性复测脚本、带固定测试账号/密码的浏览器脚本，默认按“运行态/临时验证资产”处理，不进入 GitHub 安全镜像；只有完成脱敏、确认具备长期复用价值后，才允许单独纳入。
- `upstream/`、`upstream-skills/` 下的外部仓库、生成产物或其测试样例，不因“位于允许导出目录”就自动进入安全镜像；若无明确 bootstrap 价值或含 token / webhook / callback 示例，默认按外部依赖或高噪音资产排除。
- 个人备份库默认不导出整个 `upstream/` 外部依赖区；需要共享贡献规则时，应维护脱敏后的 `knowledge/development/` 镜像文档。
- `daily-recommend/` 下的 digest、html 快照、log、translation cache 等日常运行产物默认不进入 GitHub 安全镜像；仅保留脚本、说明和可复用静态模板。

### 标准提交方案

1. 从当前 `.agents/` 生成安全导出目录，导出目录必须位于被忽略的运行态路径或系统临时路径。
2. 按安全导出边界复制文件，不直接推送本地 `.agents/.git` 历史。
3. 日常默认走增量快路径：基于 GitHub 远端 `main` 的最新工作树比较当前 sanitized export，只复制 / 删除 changed paths，并只对增量文件执行敏感扫描。
4. 周期性或高风险场景走全量校验路径：默认周日执行全量；边界规则变更、缺少成功基线、超过全量兜底周期或人工强制时，也对完整 sanitized export 执行敏感扫描，再与远端工作树做全量 overlay。
5. 推送时使用临时认证 header 或安全 credential helper；不要把 token 写入 remote URL、文档或脚本。
6. 推送后通过 GitHub API 或 shallow clone 校验远端：仓库仍为 Private、目标提交 SHA 正确、预期文件存在、禁止目录缺席、敏感扫描通过。

### 默认分支与合并模式

- 默认只维护远端 `main` 作为安全镜像主线，不在镜像仓库保留本机导出历史。
- 默认线性提交仍只写 `main`，且只能 fast-forward；不在旧临时导出历史上补推。
- 日常默认走增量模式：fresh export → fresh shallow clone `origin/main` → diff sanitized tree vs remote worktree → changed-path scan → apply delta → single clean commit → fast-forward push。
- 默认保留周期性全量模式作为兜底：周日自动全量；边界规则变更、缺少成功基线、到达全量周期（脚本默认 7 天）或人工显式 `-ForceFull` 时，也切回 fresh export → fresh shallow clone → full scan → full overlay → fast-forward push。
- 若 `push` 被拒绝为 non-fast-forward，说明远端已前进或存在并发写入，必须停止并报告；不要 force-push，不要在旧临时导出仓库上补历史。
- 只有在需要人工复核高风险差异时，才允许额外创建一次性的本地 review 分支或临时目录；默认不自动推送 review branch。

### Windows / macOS 新设备使用流程

1. 在目标设备 clone GitHub 私有镜像到工作区的 `.agents/` 位置，或先 clone 到临时目录再复制脱敏框架内容。
2. 从 `.agents/host-entries/` 恢复宿主入口：`AGENTS.md`、`CLAUDE.md` 等宿主固定文件只作为薄指针。
3. 按 `runtime/local-secrets/` 的 README / example 在本机新建真实凭证文件；不要从旧设备或 GitHub 复制明文凭证。
4. 按 `runtime/mcp/mcp.template.json` 和各 agent `runtime-configs.md` 生成本机 MCP 配置，所有 `<workspace-root>`、`<agents-root>`、`<user-home>` 必须替换为当前设备路径。
5. Windows 优先使用 PowerShell 7 或 Windows PowerShell 执行现有 setup / integrity 脚本；macOS 优先安装 PowerShell 7，短期内没有跨平台脚本时按模板手工恢复入口和配置。
6. 最后运行最小自检：宿主入口检查、框架完整性检查、MCP 路径检查、一次只读工具调用验证。

### 维护规则

1. GitHub 私有镜像只能作为个人备份和跨设备 bootstrap；团队共享仍走 `share-packages/`、`upstream/` 或单独脱敏仓库。
2. 每次调整导出边界、提交流程、凭证落点或跨平台恢复流程，都要同步更新 `governance/asset-registry.md`。
3. 如果发现远端误入敏感内容，立即停止继续推送，先轮换相关凭证，再用干净远端历史重建；不要在原历史上追加“删除敏感文件”的普通提交作为唯一处理。

---

## GitHub 公开分享架构仓库方案

本方案用于把 `.agents/` 中可分享给同事的架构设计、治理思想、工具使用方式和模板沉淀为单独仓库。它和个人备份库是两条线，不能混用。

分享仓库采用“同事可读安全版”口径：保留对理解框架有价值的项目背景、工具名和流程名，不追求完全匿名；但必须移除或占位替换个人身份、本机路径、内网 IP、真实系统 URL、公司私有域名、服务器日志路径、账号密码 token 和具体业务数据。

### 双仓库定位

| 仓库 | 可见性 | 职责 | 访问对象 | 写入方式 |
|------|--------|------|----------|----------|
| `trade-cloud-agents` | Private | 个人备份与多设备 bootstrap | 仅本人 | 自动同步脚本写入 `main` |
| `trade-cloud-agents-share` | Public 或 Private-read | 同事可读的安全架构分享与协作改进 | 同事、外部朋友、协作者 | 白名单导出写入 `main`；外部修改走 branch / PR |

原则：

- 个人备份库不再承担分享职责，不向任何人发 deploy key、token 或 collaborator 权限。
- 分享仓库不从个人备份库复制历史，只从当前 `.agents/` 真源重新生成干净工作树。
- 分享仓库允许别人 fork / branch / PR；你只在 review 后合并到分享仓库 `main`。
- 外部 PR 若有通用价值，再由你人工挑选回流到 `.agents/` 真源；不自动反向同步。

### 分享仓库允许内容

| 类型 | 默认处理 | 示例 |
|------|----------|------|
| 架构与治理设计 | 允许 | `README.md`、`contract.md`、`routing.md`、`governance/architecture.md` 的安全分享版 |
| 通用工具说明 | 允许 | `tools/` 中不含真实 URL、IP、账号、服务拓扑的索引卡 |
| 通用模板 | 允许 | `templates/`、`host-entries/` 中使用占位符的模板 |
| 分享包 | 允许 | `share-packages/agents-share/` |
| agent 适配思想 | 允许 | `codex/adapter.md`、`claude-code/adapter.md` 的脱敏版 |
| 脚本骨架 | 谨慎允许 | 只保留无真实 endpoint、无账号、无内网 IP、无业务数据的 setup / sync / validation 骨架 |

### 分享仓库禁止内容

| 类型 | 禁止原因 |
|------|----------|
| 任何账号、密码、token、cookie、私钥、连接串 | 凭证风险 |
| 个人姓名、个人邮箱、本机用户名路径 | 个人信息风险 |
| 内网 IP、真实域名、真实系统 URL、服务器日志路径、服务拓扑、服务器列表 | 内部拓扑风险 |
| `runtime/` 真实配置、MCP server 实现、local-secrets、state、session | 运行态与凭证相邻风险 |
| `archive/`、`case-studies/`、事故复盘、任务现场、日志片段 | 业务数据和内部过程风险 |
| `scripts/images/`、`skills/**/images/`、截图、附件、Excel/CSV/PDF/DOCX/PPTX | 页面、业务字段和数据泄露风险 |
| 禅道、钉钉、Seafile、Jenkins、Grafana、Nacos、RabbitMQ、Redis、MongoDB 等公司系统的真实接入细节 | 内部系统暴露风险 |
| 旧脚本中的默认密码、测试账号、真实环境名和真实 host | 历史残留风险 |

### 分享同步流程

1. 从 `.agents/` 生成 `share export`，只复制白名单路径。
2. 对导出树执行安全分享转换：替换个人身份、本机路径、内网 IP、真实系统 URL、公司私有域名、服务器日志路径和私人备份库 URL。
3. 对导出树执行阻断扫描：凭证、连接串、内网 IP、真实系统 URL、个人身份、二进制附件、禁止目录。
4. fresh clone `trade-cloud-agents-share` 的 `main`。
5. overlay 导出树，生成单个同步提交。
6. push 到分享仓库 `main`；如果远端有别人提交，先停下，人工 review / merge，不自动覆盖。
7. 每次推送后校验远端：关键文件存在、禁止路径缺席、敏感扫描为 0。

### 协作提交流程

1. 协作者从 `trade-cloud-agents-share` 创建分支或 fork。
2. 协作者只改分享仓库中的通用文档、模板或脱敏脚本骨架。
3. PR 必须通过分享仓库扫描：无 secret、无内网 IP、无真实系统 URL、无二进制附件、无业务案例。
4. 你 review 后合并 PR。
5. 对值得吸收的通用改动，人工 cherry-pick / 复制回 `.agents/` 真源，再走个人备份库同步。

### 初始仓库建议

- 仓库名：`trade-cloud-agents-share`
- 可见性：如果目标是“可以分享给任何人”，设为 Public；如果仍希望先灰度，先设 Private，再逐步开放。
- 权限：你保留 Admin；其他人通过 fork / PR 或只给 triage/read；不要发共享写 token。
- 分支保护：`main` 开启 PR review；禁止直接 push；删除历史和强推只由你人工处理。

---

## Agent 相关内容的范围定义

**"agent 相关"的判断标准：凡是为 agent 工作流创建的、或在 agent 辅助任务中使用的内容，均属于 agent 相关，必须在 `.agents/` 中有索引或归档。**

| 类型 | 判断方式 | 归属 |
|------|---------|------|
| 知识文档、规则文档 | 是否由 agent 读取或参考 | `.agents/knowledge/` 或 `governance/` |
| 可执行工具脚本 | 是否在 agent 辅助的测试/运维/同步流程中被调用 | 脚本留 `.agents/scripts/`，在 `.agents/tools/` 建索引卡 |
| 任务产出物 | 是否是某个 agent 任务的输出结果 | `.agents/case-studies/` 或 `.codex/outputs/` |
| 配置文件 | 是否是 agent 运行时读取的配置 | 原地保留（`.codex/config.toml`、`.mcp.json` 等），在 `<agent>/runtime-configs.md` 登记 |
| 设计文档、过程记录 | 是否记录 agent 框架的设计思路或历史过程 | `.agents/archive/` |

**边界**：纯业务代码（Java/SQL/生产配置）不属于 agent 相关，不迁移。

> **本规范来源**：2026-04-15 发现 `.agents/scripts/dws/`（测试工作流写 bug 工具）未被 `.agents/` 索引，根因是"agent 相关"从未被明确定义，导致判断时将工具脚本误归为业务代码。

---

## 迁移扫描目录清单

**每次执行迁移或检查"是否有 agent 内容散落在外"时，必须扫描以下目录：**

| 目录 | 扫描内容 |
|------|---------|
| `docs/codex/` | 框架文档、知识文档、操作记录 |
| `docs/context/` | 项目上下文知识 |
| `.kiro/docs/` | 框架指南、架构文档 |
| `.kiro/steering/` | Codex steering 配置（不迁移，但检查是否引用了旧路径） |
| `.kiro/specs/` | 任务规格（不迁移，按需建案例） |
| `.claude/` | Claude Code 配置（检查是否有散落的 .md 文档） |
| `.codex/` | Codex 工作区（检查 output/、outputs/、notes/ 是否有未归档内容） |
| `.agents/scripts/` | 工具脚本（检查是否在 `.agents/tools/` 有对应索引卡） |
| `scripts/` 根层 | 运维脚本（检查是否有 agent 相关文档混入） |

> **本规范来源**：2026-04-15 全量迁移时漏扫 `.claude/`、`scripts/`、`.codex/output/`，导致多处文档和工具遗漏。将固定扫描清单写入规范，后续迁移按此清单执行，不依赖经验判断。

---

## 工具脚本索引规则

**凡是在 agent 工作流中被调用的可执行脚本，创建时必须同步在 `.agents/tools/` 建立索引卡。**

索引卡格式（最简）：

```markdown
# 工具名称

**用途**：一句话说清楚这个工具做什么。
**使用场景**：什么任务场景下需要调用它。
**脚本路径**：`.agents/scripts/<category>/<script>`
**调用方式**：最简调用示例。
**维护状态**：活跃 / 停用
```

> **本规范来源**：同上，`dws/` 钉钉写 bug 工具创建时没有在 `.agents/` 留下任何记录，导致 agent 在测试工作流中不知道该工具存在，面临重复造轮子风险。

---

## SOP Router Core 借鉴边界（2026-05-15）

用户提供的同事方案截图显示 `codex-sop-router-core/` 采用 `graph/`、`policies/`、`leaf-skills/*/capsule.yml`、`memory/`、`evals/` 和 manifest 工具组织 Codex SOP 路由。该方案的高价值点是把路由、风险、执行、记忆和校验从纯 Markdown 规则中拆出机器可读结构。

具体渐进路线见 `governance/router-core-evolution.md`；本节只保留架构边界。

当前 `.agents/` 不直接切换到该方案，也不新增默认 router core。低风险吸收范围仅限：

1. 新增 `templates/skill-capsule.template.yml`，为后续高频 skill 补轻量 capsule 留模板。
2. 新增 `templates/route-eval-case.template.yml`，为后续典型任务路由回归留模板。
3. 保持 `routing.md`、`contract.md`、`skills/`、`knowledge/`、`tools/`、`runtime/` 的现有职责不变。

暂不执行的事项：

- 不新增 `router-core/` 或 `graph/` 活跃目录。
- 不把 `routing.md` 转换为 YAML 图谱。
- 不引入 `sop-route.mjs`、`manifest.mjs` 等执行脚本。
- 不改变 Codex / Claude Code 的入口、MCP 配置或默认路由。

后续触发条件：等同事分享完整项目后，再对照实际 `graph/`、`policies/`、`tools/` 的实现质量，决定是否进入结构化路由试点。试点也应先覆盖少量高频只读任务，并通过 route eval case 验证，不直接替换现有路由表。

---

## 长期演进规划

### 架构重心回归（2026-04-20 审视结论）

**现状**：治理层（governance 14 文件 + collab 13 文件 + 适配 10 文件）占核心业务层（skills+knowledge+tools = 150 文件）的 25%，支撑层过厚。

**方向**：
1. 治理层瘦身：14 → 8 文件（合并低频独立文件）
2. 重心回归业务：功能测试流程、bug 验证流程、线上问题分析流程、周计划评审流程等高频工作流逐步做厚
3. 支撑层只做最薄可用：治理规则够用就行，不再往里加复杂度

**治理层合并计划**：
- doc-creation-rules.md + knowledge-priority-backlog.md → sedimentation.md
- quarterly-checklist.md → asset-registry.md
- skills-usage-guide.md + skill-creation-checklist.md + skills-lifecycle-tiering.md + skills-frontmatter-audit.md → skills-handbook.md
- skills-feedback-guide.md → 标 deprecated
- 合并后：14 → 8 文件

### 近期（按需执行）

- 执行治理层合并计划
- `knowledge/business/` 首个内容文件：下次遇到字段口径争议时写入
- `knowledge/infrastructure/` 补充环境 URL 和访问地址

### 中期

- 业务工作流逐步沉淀：功能测试、bug 验证、线上问题分析、周计划评审
- 新 agent 接入（如 Cursor、其他 IDE 插件）：按 agent 文件夹规范创建

### 长期

- 持续维护 GitHub 私有安全导出流程，补齐导出 / 扫描 / 远端校验脚本化能力；团队共享仍走单独脱敏包或 upstream 流程
- 评估作为部门共享框架的可行性

---

*创建时间：2026-04-15*
*维护者：<maintainer> + Claude Code*
