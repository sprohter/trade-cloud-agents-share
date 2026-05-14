---
name: shared-ops-skill
description: "共享运维技能模板。用于三端复用的低频工具能力接入。关键词：共享技能、低频工具、脚本调用、跨端复用"
metadata:
  skill_level: 1
  related_skills: "tools, infrastructure"
  auto_activate: false
  display_name: "共享技能模板"
  category: "工具"
---

# shared-ops-skill

这是一个共享 skill 模板，用于统一接入低频工具能力。

## 入口信息

- 共享脚本目录：`<agents-root>/scripts/<domain>/`
- 参考目录：`<agents-root>/scripts/<domain>/references/`
- 私有配置目录：`<agents-root>/runtime/local-secrets/`

## 使用规则

1. 先检查所需环境变量或本地私有配置是否已准备
2. 先阅读 `references/`
3. 按统一命令格式调用脚本

```powershell
<tool-entry> <command> '<json>'
```

> 脚本如仅支持 Windows / PowerShell，应在 skill 或工具索引卡中显式标注；面向团队分享时优先提供 Node.js / Python / PowerShell 7 等跨平台入口。

## 迁移要求

- 不在 skill 中重复实现业务逻辑
- 不把账号密码写在 skill 中
- 所有执行逻辑统一下沉到 `.agents/scripts/`
