# DWS Skills MR Draft

适用分支：

- `feature/chunxing-fu/dws-skills`

当前提交：

- `e4f642cb9ad52c092d60f236ed2e7f3e6f10f7a8`
- `feat: add dws docs and todo handoff skills`

创建地址：

- <https://<internal-domain>/root/epean-skills/-/merge_requests/new?merge_request%5Bsource_branch%5D=feature%2Fchunxing-fu%2Fdws-skills>

---

## MR 标题

新增 DWS 文档读写与待办转交共享技能

## MR 描述（正式评审版）

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
- `python skills/dws-todo-handoff/scripts/dws_todo_handoff.py --title "可用性验证" --recipient-keyword "<maintainer>" --dry-run --json`
- `python skills/dws-todo-handoff/scripts/dws_todo_handoff.py --title "可用性验证" --recipient-user-id "noop-user" --dry-run --json`
- 结果：帮助信息正常；关键词路径 dry-run 成功；非法 userId 返回清晰业务错误，无 traceback

## 边界说明
- 本次以 DWS 通用读写能力和 Todo 转交能力为主，不包含项目内机器人编排流程
- 如后续 DWS 返回字段结构变化，仍需同步更新字段映射

## 备注
- 本 MR 用于替代此前分散的 DWS 尝试分支
- 旧的 `feature/chunxing-fu/dws-dingtalk-docs` 和 `feature/chunxing-fu/dws-todo-handoff` 远端分支已删除
```

## MR 描述（团队分享版）

```md
## 这次想解决什么
- 团队里已经有同事在用 DWS 做钉钉能力接入，但缺少一套可以直接复用的共享 skill
- 这次把目前已经跑通、边界也比较清晰的两块能力先沉淀出来，后面大家再遇到类似场景就不用从 0 搭一遍

## 这次交付了什么
- 新增 `dws-dingtalk-docs`：提供 DWS 鉴权说明、表格/文档基础读写入口和安全写入规范
- 新增 `dws-todo-handoff`：支持按关键词或 userId 找人并创建钉钉待办转交
- 同步更新 `README.md` 和 `CHANGELOG.md`，把入口补齐

## 为什么这样拆
- `dws-dingtalk-docs` 负责沉淀通用读写能力，方便后续扩展表格、文档等场景
- `dws-todo-handoff` 聚焦一个明确闭环：找人 -> 预演 -> 创建待办
- 这样拆分后，别人可以按需安装和复用，不会被项目内流程耦住

## 我做了哪些验证
- 两个脚本都做了 `py_compile`
- 两个脚本都验证了 `--help`
- `dws-todo-handoff` 验证了关键词找人 dry-run 成功
- `dws-todo-handoff` 验证了非法 userId 时返回清晰业务错误，不再抛 traceback

## 这次先不做什么
- 暂不把机器人编排流程一起带进共享仓
- 暂不引入项目内固定表配置或个人使用流程

## 备注
- 这次 MR 已经替代此前分散的 DWS 尝试分支
- 旧的 `feature/chunxing-fu/dws-dingtalk-docs` 和 `feature/chunxing-fu/dws-todo-handoff` 远端分支已删除
```

## GitLab 页面填写建议

### 1. Source branch

- 选择：`feature/chunxing-fu/dws-skills`

### 2. Target branch

- 选择：`master`

说明：

- `epean-skills` 当前贡献目标分支是 `master`
- 不要套用业务仓常见的 `dev -> master` 双 MR 流程

### 3. Title

- 填：`新增 DWS 文档读写与待办转交共享技能`

### 4. Description

- 如果 reviewer 更关注规范和可验证性：用上面的“正式评审版”
- 如果你想让描述更自然、便于团队理解：用上面的“团队分享版”

建议：

- 默认优先用“正式评审版”
- 如果你觉得页面看起来太硬，可以把“正式评审版”作为主体，再把“这次想解决什么”那两句替换进去

### 5. Assignee / 指派人

- 默认先指派自己即可
- 如果团队有固定 reviewer，再按你们当前协作习惯补充

### 6. Reviewer

- 没有明确 reviewer 规则时可以先不填
- 如果你准备点名某位同事看 DWS 方案，再单独补一个 reviewer

### 7. Labels / 标记

- 当前没有硬性要求时可以不填
- 如果你们团队习惯按 `skill`、`docs`、`tooling` 分类，再补对应标签

### 8. Milestone / 里程碑

- 没有明确版本计划时留空即可

### 9. Squash commits

- 如果 GitLab 页面有该选项，建议保持默认
- 当前整合分支已经是单个干净 commit，不需要额外纠结

### 10. Delete source branch

- 当前不建议勾“合并后删除源分支”也没关系
- 这条整合分支后面是否删除，可以等合入完成后再看

## 推荐提交方式

如果你现在直接去 GitLab 页面创建，建议这样填：

- 标题：`新增 DWS 文档读写与待办转交共享技能`
- 描述：优先使用“正式评审版”
- 源分支：`feature/chunxing-fu/dws-skills`
- 目标分支：`master`
- Assignee：先填自己
- Reviewer：按需要补，不强制
