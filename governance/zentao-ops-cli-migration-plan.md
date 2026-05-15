# zentao-ops CLI 化迁移方案

## 1. 目的

这份文档解决：

- 为 `zentao-ops` 从“skill + 脚本 + MCP 混合实现”迁移到“CLI-first + 单一真源”提供一份完整、可执行、可回滚的实施方案
- 在**不立即动旧结构**的前提下，设计一套新的 CLI 体系，先并行建设、并行验证，再统一切换
- 明确哪些能力必须收进新 CLI，哪些能力应拆成 `zentao-ops` 依赖的小工具脚本，哪些旧配置在切换后应彻底废弃
- 为后续让 Claude / Codex / 人工终端共享同一套禅道操作契约提供治理真源

## 2. 适用场景

适用于：

- 当前项目准备彻底放弃禅道 MCP，实现统一入口、统一脚本真源、统一命令契约
- 需要先保留旧配置稳定可用，再平行搭建新 CLI 结构并做完整验证
- 需要让 Claude、Codex 或人工终端都能通过同一套命令执行禅道任务 / Bug 操作
- 需要把截图、附件、剪贴板等宿主相关能力保留，但从 MCP 剥离出来

不适用于：

- 只做单个禅道命令的小修小补
- 只想修某个字段、某个接口、不涉及整体架构收口的需求
- 仍计划长期维护 `zentao-server/index.js` 作为主实现层的方案

## 3. 所属层级

- 层级：治理 / 架构迁移方案
- 放置位置：`governance/`
- 创建原因：
  - 本次不是单个 skill 文案更新，而是禅道能力的**架构级收口与迁移规划**
  - 需要同时约束 `skills/`、`scripts/`、`runtime/`、`routing.md`、旧 MCP 配置和后续废弃流程
  - 文档需作为切换前的统一讨论底稿，供 Claude / Codex / 用户共同复核

## 4. 背景

### 4.1 现状背景

本方案创建时，项目内禅道相关能力名义上已经统一口径为：

- 项目内禅道操作默认走 `zentao-ops`
- skill 默认推荐入口为：
  - `<workspace-root>\.agents\scripts\zentao\run_zentao_api.ps1`
  - `<workspace-root>\.agents\scripts\zentao\zentao_api.py`

截至 2026-05-08，Phase 7 已切换：

- 项目内禅道操作默认走 `zentao-ops-cli`
- 默认 CLI 入口为：
  - `<workspace-root>\.agents\scripts\zentao_cli\run_zentao_cli.ps1`
  - `<workspace-root>\.agents\scripts\zentao_cli\zentao_cli.py`
- 旧 `zentao-ops`、旧脚本、旧 MCP 仅作为灰度期 fallback，稳定后再单独确认归档

迁移前实际实现并未完全收口，仍存在三层并行：

1. `skills/zentao-ops/SKILL.md`
   - 负责入口说明、命令说明、使用建议
2. `scripts/zentao/zentao_api.py`
   - 承担了一部分禅道业务真源
3. `runtime/mcp-servers/zentao-server/index.js`
   - 同时承担：
     - MCP tool 暴露
     - 参数适配
     - Node 侧认证 / 请求
     - 多个 fallback 业务实现
     - 附件 / 截图 / 富文本辅助

### 4.2 已暴露的问题

本轮问题分析与复核中，已经明确暴露出以下结构性风险：

1. **多套实现并存**
   - 相同能力同时存在于 `zentao_api.py` 与 `zentao-server/index.js`
   - 一处迭代后，另一处容易不同步

2. **业务真源不唯一**
   - 对外说统一走 `zentao-ops`
   - 实际仍有能力落在 MCP fallback 中

3. **命令声明与实现不完全对齐**
   - MCP 中存在 `runZentaoOps("zentao_xxx")` 调用
   - 但 `zentao_api.py` 命令表里并不一定存在对应命令
   - 导致表面转发、实际仍走第二套实现

4. **真实故障已发生**
   - `zentao_bug_comment` 曾因旧 fallback 实现错误，出现“返回成功但没有真正新增 comment”的情况
   - 这不是理论风险，而是已落地故障

5. **职责混杂**
   - MCP 里同时混合了：
     - 业务规则
     - 宿主能力
     - 认证逻辑
     - 输出包装
   - 导致后续维护边界不清

### 4.3 本次迁移的核心判断

后续如果继续保留当前结构，即使暂时可用，也会持续存在以下问题：

- 功能迭代时需要双处甚至多处同步
- 故障排查时很难快速判断“当前到底走了哪套实现”
- skill、脚本、MCP 三者的文档与行为容易再次漂移
- Claude / Codex / 人工终端之间不一定共享同一条命令契约

因此，本次迁移的方向应明确为：

**彻底放弃禅道 MCP 的业务实现层，把所有禅道业务能力统一收口到新的 CLI 真源；skill 只负责路由与说明，宿主特性拆成 CLI 依赖的小工具脚本。**

## 5. 迁移目标

### 5.1 总目标

构建一套新的 `zentao-ops CLI` 体系，满足以下原则：

1. **CLI-first**
   - 所有禅道业务能力以 CLI 为第一入口
2. **单一真源**
   - 所有禅道业务逻辑只保留一套实现
3. **旧结构先保留**
   - 在新 CLI 测试完成前，不改旧 skill / 旧脚本 / 旧 MCP 运行现状
4. **并行验证**
   - 新旧链路并行存在，先验证再切换
5. **可回滚**
   - 切换前后都能明确回退到旧结构
6. **宿主能力保留**
   - 不能自然收进主 CLI 的能力，拆成 helper，不因废弃 MCP 而丢失

### 5.2 迁移完成后的目标结构

```text
zentao-ops-cli skill
    ↓
run_zentao_cli.ps1
    ↓
zentao_cli.py          ← 唯一业务真源 / 正式 CLI
    ↓
helpers/               ← 截图、剪贴板、附件等辅助能力
```

旧结构在切换后灰度期仍保留：

```text
skills/zentao-ops/
scripts/zentao/
runtime/mcp-servers/zentao-server/
```

Phase 7 灰度稳定并经用户单独确认进入 Phase 8 后，旧 MCP 及其相关配置再废弃。

## 6. 新旧并行策略

### 6.1 旧结构保持不变

Phase 7 灰度期以下内容全部保留，不做破坏性修改：

- `skills/zentao-ops/SKILL.md`
- `scripts/zentao/run_zentao_api.ps1`
- `scripts/zentao/zentao_api.py`
- `runtime/mcp-servers/zentao-server/index.js`
- 旧的 MCP 注册与引用
- `routing.md` 已指向 `zentao-ops-cli`；旧 `zentao-ops` 不再作为默认路由

### 6.2 新结构全新创建

建议新增以下目录与文件：

- `skills/zentao-ops-cli/SKILL.md`
- `scripts/zentao_cli/run_zentao_cli.ps1`
- `scripts/zentao_cli/zentao_cli.py`
- `scripts/zentao_cli/helpers/`
- `scripts/zentao_cli/tests/`
- `scripts/zentao_cli/README.md`

可选：

- `scripts/zentao_cli/schemas/`
- `scripts/zentao_cli/samples/`

### 6.3 切换原则

本节为切换前原则。Phase 7 切换后当前口径为：

- `routing.md` 默认路由已切到新 CLI
- 不删除旧 MCP
- 旧 skill 标记为 fallback
- 新 CLI 作为默认入口灰度试用

只有在通过迁移验收后，才执行统一切换与废弃动作。

## 7. 新 CLI 的职责边界

### 7.1 必须纳入主 CLI 的能力

以下属于**禅道业务能力**，必须统一到主 CLI：

#### Bug 能力

- `bug get`
- `bug list`
- `bug create`
- `bug edit`
- `bug comment`
- `bug assign`
- `bug resolve`
- `bug close`
- `bug activate`

#### Task 能力

- `task get`
- `task list`
- `task create`
- `task edit`
- `task comment`
- `task start`
- `task finish`
- `task close`
- `task activate`

#### 只读辅助能力

- `story get`
- `product list`
- `project list`
- `user list`
- `my bugs`
- `my tasks`
- `my todos`

#### 公共业务能力

- 富文本格式化
- 写后回读校验
- token / 账号密码认证回退
- 图片下载与详情富文本处理
- 通用输出格式

### 7.2 应拆为 helper 的能力

以下能力保留，但不强行塞进主 CLI 业务逻辑：

- 剪贴板截图
- 自动截图
- 附件预处理
- Windows 专属文件探测与临时文件处理
- 本地宿主相关图片转换能力

建议拆分为：

- `helpers/clipboard_image.ps1`
- `helpers/capture_screen.py`
- `helpers/prepare_attachments.py`
- `helpers/windows_paths.ps1`

原则：

- **业务决策在主 CLI**
- **宿主能力在 helper**

helper 契约补充：

- helper 不直接调用禅道 API
- helper 不读取、刷新或判断禅道 token
- helper 不判断 Bug / Task 状态
- helper 不拼接业务 comment / steps
- helper 不执行写后回读校验
- helper 不内置产品、项目、执行、模块等禅道业务默认值
- helper 只负责剪贴板图片、截图、本地路径探测、附件校验、图片转换、临时文件和 manifest 输出

建议 helper manifest 统一为：

```json
{
  "status": "success",
  "files": [
    {
      "path": "D:/xxx/a.png",
      "filename": "a.png",
      "mime": "image/png",
      "source": "clipboard|screenshot|local",
      "temporary": true
    }
  ],
  "warnings": []
}
```

主 CLI 负责读取该 manifest，并统一完成附件上传、业务对象写入和回读校验。

### 7.3 skill 的职责

新 `zentao-ops-cli` skill 只负责：

- 推荐入口
- CLI 命令说明
- 参数示例
- 使用边界
- 回退策略

不再承担：

- 业务逻辑实现
- 宿主能力实现
- 多套入口说明

## 8. CLI 契约设计

### 8.1 设计原则

新 CLI 建议采用：

- **正式 subcommand 模式**
- **旧 JSON 调用兼容模式**

目标是先兼容旧调用，再逐步把文档、脚本和使用习惯迁到新 CLI。

### 8.2 推荐命令面

```text
zentao auth ...
zentao user list
zentao bug get|list|create|edit|comment|assign|resolve|close|activate
zentao task get|list|create|edit|comment|start|finish|close|activate
zentao story get
zentao product list
zentao project list
zentao my bugs|tasks|todos
zentao format bug-steps
zentao legacy <old-command> <json>
```

PowerShell 入口示例：

```bash
run_zentao_cli.ps1 bug get --id 18514 --output json
run_zentao_cli.ps1 bug comment --id 18514 --comment-file note.html --verify
run_zentao_cli.ps1 task start --id 123 --consumed 1 --left 2
run_zentao_cli.ps1 my bugs --status active
```

### 8.3 推荐参数规范

统一建议支持：

- `--output json|text`
- `--verify / --no-verify`
- `--comment`
- `--comment-file`
- `--steps`
- `--steps-file`
- `--attachments <path>`（可重复）
- `--stdin`
- `--raw`

### 8.4 兼容策略

过渡期保留旧模式：

```bash
run_zentao_cli.ps1 legacy zentao_bug_get '{"id":18514}'
```

或在 `zentao_cli.py` 内兼容：

```bash
python zentao_cli.py zentao_bug_get '{"id":18514}'
```

但所有文档和后续新增示例，默认使用新 CLI 写法。

## 9. 分阶段迁移计划

### Phase 0：方案冻结

目标：

- 冻结本方案文档
- 与 Claude / Codex 复核 CLI 契约与分层策略
- 确认新目录结构与命名方案

产出：

- 本文档
- 调整意见清单

完成标志：

- 用户确认迁移方向
- CLI 结构命名不再反复变更

### Phase 1：新 CLI 骨架搭建

目标：

- 创建新 skill、脚本目录、入口脚本、helper 目录、tests 目录
- 建立 helper 契约与 manifest 约定
- 不动旧结构

建议新增：

- `skills/zentao-ops-cli/SKILL.md`
- `scripts/zentao_cli/run_zentao_cli.ps1`
- `scripts/zentao_cli/zentao_cli.py`
- `scripts/zentao_cli/helpers/`
- `scripts/zentao_cli/tests/`
- `scripts/zentao_cli/README.md`

完成标志：

- 新 CLI 可以被调用
- 帮助信息、命令骨架、输出结构已成形

### Phase 2：先补齐读能力

目标：

- 优先构建对比验证所需的只读能力

范围：

- `bug get`
- `bug list`
- `task get`
- `task list`
- `story get`
- `product list`
- `project list`
- `user list`
- `my bugs`
- `my tasks`
- `my todos`

完成标志：

- 新 CLI 读操作可与旧链路逐项比对
- 字段输出稳定

### Phase 3A：补齐评论类写能力

目标：

- 优先补齐最容易出现“返回成功但未真正写入”的评论能力

范围：

- `bug comment`
- `task comment`

要求：

- 所有写操作默认具备回读校验能力
- 不能只看接口返回 success

完成标志：

- 评论类操作可在专用测试对象上跑通，且回读能确认真实落地

### Phase 3B：补齐状态流转类写能力

目标：

- 将核心状态流转写操作并入新 CLI

范围：

- `bug resolve`
- `bug close`
- `bug activate`
- `task start`
- `task finish`
- `task close`
- `task activate`

要求：

- 所有写操作默认具备回读校验能力
- 不能只看接口返回 success

完成标志：

- 状态流转类操作均可在专用测试对象上跑通

### Phase 3C：补齐指派 / 修改类能力

目标：

- 把旧链路里容易分散在 edit / assign / fallback 的写能力收口到新 CLI

范围：

- `bug assign`
- `bug edit`
- `task edit`

要求：

- 所有写操作默认具备回读校验能力
- 不能只看接口返回 success

完成标志：

- 指派 / 修改类操作均可在专用测试对象上跑通

### Phase 4：迁移 create 与公共格式能力

目标：

- 完成复杂参数、富文本与表单类能力统一

范围：

- `bug create`
- `task create`
- `format bug-steps`
- 富文本格式与 HTML 归一化能力
- 附件与富文本输入能力

完成标志：

- 复杂输入能力在新 CLI 中达到旧能力可用水平

### Phase 5：拆 helper

目标：

- 保留截图、附件、宿主能力，但从 MCP 中剥离

范围：

- 剪贴板截图
- 自动截图
- 附件准备 / 上传辅助
- Windows 本地辅助能力

完成标志：

- 主 CLI 不再直接堆宿主细节
- helper 能独立调用并被主 CLI 编排

### Phase 6：并行测试

目标：

- 新旧链路并行验证，不影响现网使用

测试方式：

1. 读能力对比
   - 同一对象、新旧输出做字段比对
2. 写能力验证
   - 在专用测试 Bug / Task 上执行
3. 富文本验证
   - comment / steps / desc 回读结构验证
4. 附件与截图能力验证
5. 异常与回退验证
   - token 失效
   - 账号密码回退
   - Web comment 场景

完成标志：

- 新 CLI 通过迁移验收

### Phase 7：切换默认入口

目标：

- 把默认使用口径切到新 CLI

动作：

- 更新 `routing.md`
- 更新旧 `zentao-ops` skill，标记 deprecated
- 将默认 skill 入口改为 `zentao-ops-cli`
- 更新相关文档与示例

完成标志：

- 日常禅道任务不再依赖旧 MCP

### Phase 7.5：灰度观察期

目标：

- 在保留旧链路可回退的前提下，让新 CLI 作为默认推荐入口完成一轮真实任务验证

要求：

- 旧 MCP、旧 skill、旧脚本全部保留
- 新 CLI 成为默认推荐入口
- 真实日常任务至少跑完一轮
- 灰度期间所有失败案例记录到 `migration-log.md`

完成标志：

- 灰度期间未出现阻塞性回滚触发条件

### Phase 8：废弃旧结构

目标：

- 删除或归档所有旧 MCP 与旧配置

范围：

- `runtime/mcp-servers/zentao-server/index.js`
- 禅道 MCP 相关注册配置
- 旧 MCP 依赖说明
- Node 侧禅道重复业务实现

建议动作：

- 先移入 `_cleanup/` 或 `archive/`
- 验证稳定后再彻底删除

完成标志：

- 禅道链路只剩 CLI 真源

## 10. 测试与验收方案

### 10.1 测试原则

- 旧结构不动，新 CLI 平行验证
- 写操作只在专用测试对象上执行
- 结果必须以回读为准
- 不接受“命令返回 success 但数据未落库”的通过判断

### 10.2 最小验收集

至少覆盖：

1. `bug get`
2. `task get`
3. `bug list`
4. `task list`
5. `user list`
6. `my bugs`
7. `story get`
8. `bug comment`
9. `task comment`
10. `bug assign`
11. `bug resolve`
12. `bug close`
13. `bug create`
14. `task create`
15. `task edit`
16. `task start`
17. `task finish`
18. 富文本 comment 回读
19. 认证失效后的自动回退验证
20. helper manifest 到主 CLI 附件上传的完整链路验证
21. 附件或截图能力至少一条链路可用

### 10.3 验收标准

迁移完成前必须满足：

1. 新 CLI 覆盖当前常用禅道业务能力
2. 所有写操作具备回读校验
3. skill 文档与 CLI 实际命令一致
4. 截图 / 附件能力仍可用
5. 不再依赖 `zentao-server/index.js`
6. Claude / Codex / 人工终端能共享同一套命令契约

所有写操作验收必须同时满足：

1. 接口调用成功
2. 回读对象确认状态、评论、指派人、附件真实落地
3. 输出中能明确看到校验结果，而不是只显示接口 success

## 11. 风险与回滚

### 11.1 主要风险

1. **主 CLI 过度膨胀**
   - 若把宿主能力全塞进主脚本，会导致 CLI 重新变成巨石

2. **并行期间文档混乱**
   - 新旧 skill 同时存在，若说明不清容易误用

3. **写操作测试误打真实对象**
   - 需明确专用测试 Bug / Task

4. **helper 拆分后调用链复杂**
   - 需保证 helper 只承担宿主能力，不再长出业务规则

5. **切换过早**
   - 在未完成读写对比和异常验证前切换，可能导致实际能力倒退

### 11.2 回滚策略

本次迁移采用“新建并行，不碰旧结构”的方式，因此回滚策略简单明确：

#### 切换前

- 新 CLI 验证失败时，直接停止推进
- 旧 skill / 旧脚本 / 旧 MCP 继续使用

#### 切换后短期内

- 若新 CLI 出现阻塞问题：
  1. `routing.md` 回切旧 `zentao-ops`
  2. 旧 `zentao-ops` skill 恢复默认入口，或移除 deprecated 标记
  3. 保留新 `scripts/zentao_cli/`，标记为 experimental，不立即删除
  4. MCP 注册配置保持可用，直到 Phase 8 经单独确认后再处理

明确回滚触发条件：

- 新 CLI 写操作返回成功但回读不一致
- 常用只读命令字段缺失，影响测试判断
- 附件 / 截图链路不可用
- 认证回退失败，而旧链路可用
- skill 文档命令与 CLI 实际行为不一致

#### 旧结构删除前

- 必须确认已通过一轮完整真实任务使用验证
- 旧结构建议先移入 `_cleanup/` 或 `archive/`，不要直接硬删

## 12. 当前建议的实施顺序

推荐严格按以下顺序推进：

1. 冻结方案与命令面
2. 新建 CLI 骨架
3. 先补读能力
4. 再补写能力
5. 再拆 helper
6. 做并行测试
7. 切默认入口
8. 废弃旧配置

不建议：

- 一开始就直接删 MCP
- 一开始就改 `routing.md`
- 边设计命令边删旧实现
- 不做并行测试就直接切换

## 13. 相关文档

- [zentao-ops Skill](../skills/zentao-ops/SKILL.md)
- [旧禅道脚本 zentao_api.py](../scripts/zentao/zentao_api.py)
- [旧禅道 MCP server](../runtime/mcp-servers/zentao-server/index.js)
- [路由表 routing.md](../routing.md)
- [治理日志 migration-log.md](./migration-log.md)
- [资产目录 asset-registry.md](./asset-registry.md)

## 14. 后续处理

- 是否需要继续沉淀为 skill：
  - 否，本文件是治理方案，不是 skill
- 是否只是临时文档：
  - 否，这是一份迁移期间的正式治理真源
- 后续是否应归档：
  - 是，迁移完成并稳定后，可归档为历史迁移方案，并把稳定规则收口回 `skills/`、`routing.md`、`asset-registry.md`

## 15. 发布前检查

- 如果这是 agent 相关文档，是否已放在 `.agents/` 框架内，或已说明例外理由：
  - 是
- 本文新增链接是否都可解析：
  - 是，均指向当前已存在稳定路径
- 如果本文承担索引作用，已有条目是否都可点击：
  - 是
- 是否引用了已有真源而不是重复定义：
  - 是，本文只做迁移方案，不重写现有 skill / script 内容

## 16. 复核追加意见（Claude Code，2026-05-06）

本节为方案冻结前的追加复核意见，重点用于约束 Codex 后续确认与实施。

### 16.1 背景判断复核结论

当前方案的核心背景判断成立：

- 旧 `zentao-ops` skill 名义上推荐统一走 `scripts/zentao/zentao_api.py`。
- 实际 MCP server 中仍存在大量 `runZentaoOps(...)` 失败后的 Node fallback。
- MCP 调用的部分命令并不在旧 `zentao_api.py` 命令表中，导致“表面转发、实际 fallback”的风险持续存在。
- MCP 同时承载业务操作、认证、附件、截图、剪贴板、富文本与输出包装，职责确实混杂。
- `zentao_bug_comment` 曾经暴露过“接口返回成功但评论未真实落地”的真实故障，因此写后回读校验必须作为新 CLI 的强制标准。

结论：迁移方向不需要推翻，仍应坚持 **CLI-first + 单一业务真源 + MCP 业务层废弃**。

### 16.2 CLI 命令面补齐建议

第 7.1、第 8.2、第 9 阶段计划和第 10 验收清单中，应补齐以下能力，避免新 CLI 覆盖不完整：

- `user list`
  - 用于真实姓名到 `account` 的映射，是创建、指派、流转前置能力。
- `bug list`
- `task list`
- `bug assign`
  - 旧 MCP 已暴露该能力，但旧 `zentao_api.py` 未形成稳定命令，应作为迁移补齐重点。
- `task comment`
  - 旧脚本已有 `zentao_task_comment`，新 CLI 不应遗漏。

建议最终命令面至少包含：

```text
zentao user list
zentao bug get|list|create|edit|comment|assign|resolve|close|activate
zentao task get|list|create|edit|comment|start|finish|close|activate
zentao story get
zentao product list
zentao project list
zentao my bugs|tasks|todos
zentao format bug-steps
zentao legacy <old-command> <json>
```

### 16.3 helper 边界补充

现有“业务决策在主 CLI，宿主能力在 helper”原则正确，但实施前需要补成明确契约。

helper 不应承担：

- 直接调用禅道 API。
- 读取、刷新或判断禅道 token。
- 判断 Bug / Task 状态。
- 拼接业务 comment / steps。
- 执行写后回读校验。
- 内置产品、项目、执行、模块等禅道业务默认值。

helper 只应承担：

- 剪贴板图片读取。
- 屏幕截图。
- 本地路径探测。
- 附件存在性、大小、扩展名、MIME 检查。
- 图片格式转换。
- 临时文件创建与清理。
- 输出标准 attachment manifest。

建议 helper 输出统一为：

```json
{
  "status": "success",
  "files": [
    {
      "path": "D:/xxx/a.png",
      "filename": "a.png",
      "mime": "image/png",
      "source": "clipboard|screenshot|local",
      "temporary": true
    }
  ],
  "warnings": []
}
```

主 CLI 负责读取该 manifest，并统一完成附件上传、业务对象写入和回读校验。

### 16.4 分阶段计划调整建议

原 Phase 0-8 可执行，但建议做以下细化：

1. **Phase 1 提前建立 helper 契约**
   - helper 目录可以在 Phase 1 创建。
   - 具体截图、剪贴板、附件实现可在后续阶段迁移。
   - 这样可避免 Phase 4 处理 create/edit/comment 时临时把宿主逻辑塞回主 CLI。

2. **Phase 2 只读能力补齐**
   - 除原计划外，增加：
     - `bug list`
     - `task list`
     - `user list`

3. **Phase 3 写能力建议拆小验收点**
   - Phase 3A：评论类
     - `bug comment`
     - `task comment`
   - Phase 3B：状态流转类
     - `bug resolve|close|activate`
     - `task start|finish|close|activate`
   - Phase 3C：指派 / 修改类
     - `bug assign`
     - `bug edit`
     - `task edit`
   - Phase 4 再处理复杂创建与富文本：
     - `bug create`
     - `task create`
     - `format bug-steps`
     - 附件与富文本输入

### 16.5 验收清单补充

第 10.2 最小验收集建议增加：

- `user list`
- `bug list`
- `task list`
- `bug assign`
- `task comment`
- `bug create`
- `task edit`
- 认证失效后的自动回退验证
- helper manifest 到主 CLI 附件上传的完整链路验证

所有写操作验收必须同时满足：

1. 接口调用成功。
2. 回读对象确认状态 / 评论 / 指派人 / 附件真实落地。
3. 输出中能明确看到校验结果，而不是只显示接口 success。

### 16.6 切换与回滚补充

Phase 7 切默认入口后，不应立即进入 Phase 8 废弃旧结构，应增加灰度期。

灰度期要求：

- 旧 MCP、旧 skill、旧脚本全部保留。
- 新 CLI 成为默认推荐入口。
- 真实日常任务至少跑完一轮后，再评估是否进入废弃阶段。
- 灰度期间所有失败案例记录到 `governance/migration-log.md`。

建议明确回滚触发条件：

- 新 CLI 写操作返回成功但回读不一致。
- 常用只读命令字段缺失，影响测试判断。
- 附件 / 截图链路不可用。
- 认证回退失败，而旧链路可用。
- skill 文档命令与 CLI 实际行为不一致。

切换后短期回滚动作应具体化为：

1. `routing.md` 回切到旧 `skills/zentao-ops/`。
2. 旧 `skills/zentao-ops/SKILL.md` 恢复默认入口说明，或移除 deprecated 标记。
3. 保留新 `scripts/zentao_cli/`，但标记为 experimental，不立即删除。
4. MCP 注册配置保持可用，直到 Phase 8 经过单独确认后再处理。

### 16.7 文档链接建议

第 13 节长期治理文档链接建议优先使用相对路径，减少本机绝对路径绑定：

- `../skills/zentao-ops/SKILL.md`
- `../scripts/zentao/zentao_api.py`
- `../runtime/mcp-servers/zentao-server/index.js`
- `routing.md` 可写为 `../routing.md`

绝对路径可保留在本机 runtime 说明中，但治理方案正文建议避免依赖 `<workspace-root>`。

### 16.8 最终复核结论

本方案可以作为迁移主方案继续推进。冻结前建议先补齐：

1. 命令覆盖清单。
2. helper 契约。
3. 分阶段验收点。
4. 灰度期与回滚触发条件。
5. 长期文档链接相对化。

补齐后，再由 Codex 进入实施阶段。
