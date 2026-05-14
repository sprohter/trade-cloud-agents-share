# 业务上下文卡片模板

```yaml
---
key: example-business-key
name: 业务名称
status: draft
lastVerifiedAt: YYYY-MM-DD
gitCommit: <git commit hash>
evidenceLevel: A
referencedFiles:
  - trade-module/path/to/Controller.java
  - trade-module/path/to/Service.java
---
```

## Applies When

- 这张卡片适用于哪些业务问题、测试场景或用户表述。

## Keywords

- 中文业务词：
- 英文标识：
- URL / 菜单：
- 表名 / 字段：
- 常见禅道 / 反馈表述：

## Business Entry Points

- 菜单 / 页面：
- API / Controller：
- Job / MQ：
- 外部平台入口：

## Frontend Entry

- `path/to/file.js:line` - 作用说明

## Backend Entry

- `path/to/Controller.java:line` - 入口说明
- `path/to/Service.java:line` - 核心处理
- `path/to/Mapper.xml:line` - 数据访问

## Data Model / Tables

- 表：
- 关键字段：
- 字段来源：

## Core Rules Summary

- 已确认规则 1（证据：`path:line`）
- 已确认规则 2（证据：`path:line`）

## Regression Focus

- 保存 / 更新：
- 删除 / 状态流转：
- 权限 / 可见性：
- 平台差异：
- 异步 / 定时 / MQ：

## Evidence Paths

- `path/to/file:line`
- SQL/schema：
- 接口文档：
- 日志 / 页面验证：

## Open Questions

- 未确认问题写在这里，不要写成规则。
