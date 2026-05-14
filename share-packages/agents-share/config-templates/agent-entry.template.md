# Agent 入口模板

> 用于把本分享包接到本地 agent 入口文件中。
> 将下面的 `<agents-share-root>` 替换为你的实际本地路径。

---

## 1. 通用 AGENTS / CLAUDE 入口模板

```md
## 团队 Agents 分享包入口

第一个读取动作：
1. 读 <agents-share-root>/README.md
2. 读 <agents-share-root>/contract.md
3. 读 <agents-share-root>/routing.md
4. 按路由进入对应 workflow / subflow / runbook

默认适用范围：
- 功能测试
- 线上反馈问题分析
- Bug 闭环
- 工具配置与接入

新建或维护长期 agent 文档时，默认放到本地 `.agents/` 框架或本分享包对应层级，不放仓库根目录。
```

---

## 2. 更强约束版模板

```md
接到测试类任务时必须先执行：

步骤 1 → 读取 <agents-share-root>/README.md
步骤 2 → 读取 <agents-share-root>/contract.md
步骤 3 → 读取 <agents-share-root>/routing.md
步骤 4 → 识别对应 workflow / subflow / runbook
步骤 5 → 再执行任务
```

---

## 3. 推荐补充说明

可在本地入口文件里补一句：

```md
本地凭证、账号密码、token 不在分享包中维护，只能从环境变量或本地私有配置读取。
分享包里的 MCP server 名称是脱敏示例名，项目内真实名称以本地 `.agents/runtime/mcp/` 真源为准。
```

---

## 4. 适用对象

- Claude
- Codex
- 其他支持本地文件读取的 agent
