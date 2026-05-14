# Epean Skills MR Template

适用场景：

- 向 `epean-skills` 提交新的共享 skill
- 更新现有 skill 的共享能力
- 需要快速整理 reviewer 友好的 MR 描述

使用原则：

- 标题优先写“能力名”，不要只写技术动作
- 正文优先写“解决什么问题”，不要先堆文件名
- 验证必须写真实命令和真实结果

---

## 标题模板

### 新增单个 skill

`新增 <skill-name 对应能力> 共享技能`

示例：

- `新增 DWS 待办转交共享技能`
- `新增 DWS 表格读写共享技能`

### 新增同主题多个 skill

`新增 <主题> 共享技能`

示例：

- `新增 DWS 文档读写与待办转交共享技能`

### 修复现有 skill

`修复 <skill-name> <问题描述>`

示例：

- `修复 dws-todo-handoff 接收人解析异常`

---

## 描述模板

```md
## 变更目的
- 解决什么共享问题
- 为什么这次值得沉淀到 epean-skills

## 变更内容
- 新增 `skills/<skill-a>/`
- 新增 `skills/<skill-b>/`
- 更新 `README.md` 和 `CHANGELOG.md`

## 验证情况
- `python -m py_compile ...`
- `python ... --help`
- `python ... --dry-run --json`
- 结果：帮助信息正常 / dry-run 成功 / 异常路径返回单行业务错误

## 边界说明
- 本次不包含什么
- 仍有哪些后续可增强点

## 备注
- 本 MR 用于替代旧分支 `<old-branch>`
- 建议在本 MR 合并后删除旧尝试分支，避免继续误用
```

---

## DWS 主题可直接复用版本

### 标题

`新增 DWS 文档读写与待办转交共享技能`

### 描述

```md
## 变更目的
- 将当前项目内已验证可用的 DWS 能力收口为团队可复用的共享 skill
- 统一 DWS 文档读写和待办转交的最小可用工作流，降低团队重复搭建成本

## 变更内容
- 新增 `skills/dws-dingtalk-docs/`，沉淀 DWS 鉴权、表格/文档基础读写入口和安全写入规范
- 新增 `skills/dws-todo-handoff/`，支持通过关键词或 userId 解析接收人并创建钉钉待办转交
- 更新 `README.md` 和 `CHANGELOG.md`

## 验证情况
- `python -m py_compile skills/dws-dingtalk-docs/scripts/dws_json_runner.py skills/dws-todo-handoff/scripts/dws_todo_handoff.py`
- `python skills/dws-dingtalk-docs/scripts/dws_json_runner.py --help`
- `python skills/dws-todo-handoff/scripts/dws_todo_handoff.py --help`
- `python skills/dws-todo-handoff/scripts/dws_todo_handoff.py --title "可用性验证" --recipient-keyword "付春幸" --dry-run --json`
- `python skills/dws-todo-handoff/scripts/dws_todo_handoff.py --title "可用性验证" --recipient-user-id "noop-user" --dry-run --json`
- 结果：帮助信息正常；关键词路径 dry-run 成功；非法 userId 返回清晰业务错误，无 traceback

## 边界说明
- 本次以 DWS 通用读写能力和 Todo 转交能力为主，不包含项目内机器人编排流程
- 如后续 DWS 返回字段结构变化，仍需同步更新字段映射

## 备注
- 本 MR 用于替代此前分散的 DWS 尝试分支
- 建议在本 MR 合并后删除旧的 `feature/chunxing-fu/dws-dingtalk-docs` 和 `feature/chunxing-fu/dws-todo-handoff` 分支
```
