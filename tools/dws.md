# dws — 钉钉产品能力工具

**用途**：通过 DWS CLI 调用钉钉产品能力，包括 AI 表格缺陷写入、单聊/群聊收发、机器人消息、DING、待办等。

**使用场景**：功能测试执行阶段写入钉钉 AI 表格缺陷，或需要由 agent 操作钉钉消息、待办、DING 等协同能力时。

**脚本路径**：`.agents/scripts/dws/`

**默认运行体**：`.agents/runtime/tools/dws/current/dws.exe`

说明：

1. `run-dws.ps1` / `run-dws.js` 是统一入口
2. 默认能力随 `.agents/` 架构一并交付，不依赖 `_cleanup/` 等临时目录
3. 外部安装路径（如 `DWS_BIN`、`C:\Tools\dws\current\dws.exe`）仅作覆盖或回退

| 脚本 | 作用 |
|------|------|
| `run-dws.ps1` / `run-dws.js` | 主入口，执行写入 |
| `new-dingtalk-bug-payload.ps1` | 构造 bug payload |
| `add-dingtalk-aitable-profile.ps1` | 添加新表格 profile |
| `configs/aitable-bug-profiles.json` | 表格配置（字段映射） |
| `templates/bug-description-example.md` | Bug 描述模板 |
| `templates/bug-record-payload-example.json` | Payload 结构示例 |

**调用方式**：
```powershell
cd .agents/scripts/dws
.\run-dws.ps1 <dws args...>

# 示例：查看版本 / 路由 dry-run
.\run-dws.ps1 --version
.\run-dws.ps1 chat message send --user <userId> --title "测试" --text "内容" --dry-run --format json
```

**目录职责**：

| 路径 | 作用 |
|------|------|
| `.agents/skills/dws/` | DWS 通用 skill 和产品参考，是 agent 使用钉钉能力的主要说明入口 |
| `.agents/skills/dws/scripts/` | 随 skill 分发的通用辅助脚本 |
| `.agents/scripts/dws/` | 项目级 wrapper、AI 表格视图脚本和 payload 工具 |
| `.agents/runtime/tools/dws/current/` | 随架构交付的默认 DWS 运行体 |

**配套知识**：`.agents/knowledge/testing/钉钉AI表格缺陷录入规范.md`

**维护状态**：活跃
