# Host Entries

> 本目录保存各 agent 宿主固定入口文件的模板真源。
> 根目录 `AGENTS.md` / `CLAUDE.md` 是由这里安装出去的宿主门牌，不承载长期知识真源。

## 设计目标

- `.agents/` 是可迁移的 agent 资产包。
- 根目录入口文件只是宿主固定发现路径下的 bootstrap 产物。
- 新设备迁移时，复制 `.agents/` 后运行安装脚本，即可恢复根目录入口。

## 当前模板

| 模板 | 安装目标 | 作用 |
|------|----------|------|
| `AGENTS.md` | `<project-root>/AGENTS.md` | Codex 项目入口 |
| `CLAUDE.md` | `<project-root>/CLAUDE.md` | Claude Code 项目入口 |

## 安装方式

```powershell
.\.agents\scripts\setup\install-agent-host-entries.ps1
```

只检查不写入：

```powershell
.\.agents\scripts\setup\install-agent-host-entries.ps1 -Check
```

## 维护规则

- 修改入口内容时，优先改本目录模板，再运行安装脚本同步根目录。
- 根目录入口不写项目知识正文，只写最小启动规则和 `.agents/` 指针。
- 若宿主未来支持自定义入口路径，再把安装脚本降级为兼容层。

