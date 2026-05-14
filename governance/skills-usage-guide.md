---
deprecated: true
migrated_to: governance/skills-handbook.md
migrated_at: 2026-04-20
---

# Skills 使用指南

> ⚠️ 已 deprecated（2026-04-20）。内容已合并至 `governance/skills-handbook.md` §1，本文件保留以防旧引用，不再维护。

## 🎯 什么是 Skills？

Skills 是 Kiro 的**按需激活的专业知识库**，包含具体的工作流程、标准规范和参考文档。

### 核心概念

**Skills = 专业技能模块**
- 每个 skill 专注于一个特定领域（Bug管理、性能分析、工具使用等）
- 只在需要时激活，不会占用常驻内存
- 可以组合使用，灵活应对复杂任务

### 与 Steering 的区别

| 特性 | Steering | Skills |
|:---|:---|:---|
| **用途** | 核心行为配置 | 专业知识库 |
| **加载方式** | 自动加载 | 按需激活 |
| **内容类型** | 个人偏好、沟通风格 | 工作流程、标准规范 |
| **文件数量** | 4-5个（精简） | 20+个（丰富） |
| **更新频率** | 低（稳定） | 中高（持续改进） |
| **适用场景** | 所有对话 | 特定任务 |

**类比理解**：
- Steering = 你的性格和习惯（始终存在）
- Skills = 你的专业技能（需要时调用）

---

## 🚀 如何使用 Skills？

### 方法 1: 自动激活（推荐）

当你讨论相关主题时，Kiro 会自动识别并激活对应的 skill。

**示例**：
```
你: "系统报错了，需要创建禅道Bug"
Kiro: [自动激活 bug-management skill]
      "我来帮你创建Bug，请提供以下信息..."
```

**自动激活的 skills**：
- `bug-management` - 当讨论具体Bug报告时
- `performance` - 当讨论具体性能问题时

### 方法 2: 手动激活

使用 `#skill名称` 手动激活特定的 skill。

**示例**：
```
你: "#tools"
Kiro: [加载 MCP 工具使用知识]
      "MCP 工具使用指南已加载，有什么可以帮你的？"
```

**手动激活的 skills**：
- `#tools` - MCP 工具使用
- `#development` - 开发流程
- `#infrastructure` - 基础设施
- `#ops-runtime` - MongoDB/Redis/SSH/RabbitMQ 等低频运行时排查
- `#ops-ci` - Jenkins/XXL-Job 等低频 CI 排查
- `#ops-docs` - Seafile/共享文档检索
- `#reference` - 参考文档

### 方法 3: 组合使用

可以同时激活多个 skills 来处理复杂任务。

**示例**：
```
你: "#bug-management #performance"
Kiro: [同时加载 Bug管理 和 性能分析 知识]
      "已加载 Bug管理 和 性能分析 skills，准备好了！"
```

### 方法 4: 直接访问文件

如果不确定如何激活，可以直接打开 skill 文件。

**路径格式**：`.agents/skills/{skill名称}/{文档名称}.md`

**示例**：
- `.agents/skills/tools/mcp-usage-guide.md`
- `.agents/skills/bug-management/zentao-bug-creation.md`

---

## 📚 可用的 Skills

### 🐛 bug-management - Bug管理

**用途**: 禅道Bug创建、Bug报告流程、问题分析方法

**激活方式**: 
- 自动激活：讨论具体Bug报告时
- 手动激活：`#bug-management`

**包含文档**:
- `zentao-bug-creation.md` - 禅道Bug创建标准规范
- `bug-reporting.md` - Bug报告工作流程
- `quick-reference.md` - 快速参考指南

**适用场景**:
- 创建禅道Bug
- 报告系统问题
- 分析Bug根因
- 编写重现步骤

---

### ⚡ performance - 性能分析

**用途**: 监控数据分析、性能瓶颈识别、优化方案评估

**激活方式**:
- 自动激活：讨论具体性能问题时
- 手动激活：`#performance`

**包含文档**:
- `monitoring-analysis.md` - 监控数据分析标准流程
- `performance-analysis.md` - 性能分析方法论
- `case-study-*.md` - 实际案例研究

**适用场景**:
- 分析系统性能
- 查看监控数据
- 识别性能瓶颈
- 制定优化方案

---

### 💻 development - 开发流程

**用途**: 代码审查、GitLab MR、禅道任务开发流程

**激活方式**: `#development`

**包含文档**:
- `code-review.md` - 代码审查规范
- `gitlab-merge-request.md` - GitLab MR 提交流程
- `zentao-task-development.md` - 禅道任务开发指南

**适用场景**:
- 提交代码审查
- 创建 GitLab MR
- 开发禅道任务
- 遵循开发规范

---

### 🏗️ infrastructure - 基础设施

**用途**: 环境识别、数据库操作、日志查询指南

**激活方式**: `#infrastructure`

**包含文档**:
- `environment-detection.md` - 环境配置映射
- `database-operations.md` - 数据库操作规范
- `elk-index-mapping.md` - ELK 索引映射
- `operation-log-guide.md` - 操作日志查询指南

**适用场景**:
- 识别环境（测试/UAT/生产）
- 查询数据库
- 搜索日志
- 查看操作记录

---

### 🧰 ops-runtime - 共享运行时运维

**用途**: MongoDB、Redis、SSH、RabbitMQ、Nacos、Grafana 等低频运行时排查

**激活方式**:
- 自动激活：提到 MongoDB、操作日志、Redis、SSH、RabbitMQ、Nacos、Grafana 等具体排查任务时
- 手动激活：`#ops-runtime`

**包含内容**:
- `D:\project\trade_cloud\.agents\scripts\ops-runtime\runtime-ops.ps1`
- `D:\project\trade_cloud\.agents\scripts\ops-runtime\references\commands.md`

**适用场景**:
- 查询生产/测试 MongoDB 操作日志
- Redis key 排查
- SSH 日志查看
- RabbitMQ/Nacos/Grafana 排查

---

### 🛠️ ops-ci - 共享CI运维

**用途**: Jenkins、XXL-Job 等低频 CI / 调度排查

**激活方式**:
- 自动激活：提到 Jenkins、XXL-Job、构建、定时任务等具体排查任务时
- 手动激活：`#ops-ci`

**包含内容**:
- `D:\project\trade_cloud\.agents\scripts\ops-ci\ci-ops.ps1`
- `D:\project\trade_cloud\.agents\scripts\ops-ci\references\commands.md`

**适用场景**:
- 查看 Jenkins job / build
- 查询 XXL-Job 分组、任务、执行日志

---

### 📄 ops-docs - 共享文档运维

**用途**: Seafile、共享文档检索

**激活方式**:
- 自动激活：提到 Seafile、共享文档、文档检索、需求文档等具体任务时
- 手动激活：`#ops-docs`

**包含内容**:
- `D:\project\trade_cloud\.agents\scripts\ops-docs\docs-ops.ps1`
- `D:\project\trade_cloud\.agents\scripts\ops-docs\references\commands.md`

**适用场景**:
- 列出 Seafile 资料库
- 查看目录内容

---

### 🔧 tools - 工具使用 ✅ 已完成

**用途**: MCP工具配置、使用指南、优化方案

**激活方式**: `#tools`

**包含文档**:
- `mcp-usage-guide.md` - MCP 使用指南（超简单版）✅
- `mcp-setup-checklist.md` - MCP 配置检查清单 ✅
- `mcp-optimization-guide.md` - MCP 优化指南（待迁移）
- `mcp-auto-management.md` - MCP 自动管理策略（待迁移）

**适用场景**:
- 配置 MCP 工具
- 查询数据库
- 查看日志
- 管理定时任务
- 排查工具问题

---

### 📚 reference - 参考文档

**用途**: 数据库命名规范、网址导航、API文档等静态参考

**激活方式**: `#reference`

**包含文档**:
- `database-naming-guide.md` - 数据库命名规范
- `website-guide.md` - 网址导航
- `xxljob-tasks.md` - XXL-Job 任务列表
- `api-integration.md` - API 集成文档

**适用场景**:
- 查询命名规范
- 查找系统网址
- 查看定时任务
- 查阅 API 文档

---

## 💡 最佳实践

### 1. 优先使用自动激活

**推荐做法**：
```
✅ 直接描述你的需求
"系统报错了，需要创建Bug"
"入库单审核很慢，需要分析性能"
```

**不推荐做法**：
```
❌ 先手动激活再描述
"#bug-management"
"我要创建Bug"
```

**原因**：自动激活更智能，会根据上下文选择最合适的 skills。

### 2. 组合使用处理复杂任务

**示例**：
```
你: "生产环境订单同步失败，需要排查并创建Bug"
Kiro: [自动激活 infrastructure + bug-management]
      1. 先查询生产环境日志（infrastructure）
      2. 分析失败原因
      3. 创建禅道Bug（bug-management）
```

### 3. 不确定时查看 README

每个 skill 目录都有 README.md，说明包含的文档和用途。

**路径**：`.agents/skills/{skill名称}/README.md`

### 4. 直接访问文件作为备选

如果激活机制不工作，可以直接打开文件查看。

**示例**：
```
打开文件：.agents/skills/tools/mcp-usage-guide.md
查看内容：完整的 MCP 工具使用指南
```

---

## 🔍 常见问题

### Q1: 如何知道哪个 skill 被激活了？

**A**: Kiro 会在回答中提示：
```
Kiro: [已激活 bug-management skill]
      "我来帮你创建Bug..."
```

### Q2: 可以同时激活多个 skills 吗？

**A**: 可以！使用空格分隔：
```
你: "#bug-management #performance"
Kiro: [同时激活两个 skills]
```

### Q3: 自动激活不工作怎么办？

**A**: 使用手动激活或直接访问文件：
```
方法1: "#tools"
方法2: 打开 .agents/skills/tools/mcp-usage-guide.md
```

### Q4: Skills 和 Steering 会冲突吗？

**A**: 不会！它们是互补的：
- Steering 定义你的基本行为
- Skills 提供专业知识
- 一起工作，相得益彰

### Q5: 如何知道有哪些 skills 可用？

**A**: 查看主 README：
```
打开文件：.agents/skills/README.md
查看内容：所有可用 skills 的清单
```

### Q6: Skills 会自动更新吗？

**A**: 会的！当 steering 文档迁移到 skills 后，会自动同步更新。

### Q7: 旧的 steering 文档还能用吗？

**A**: 可以！在迁移完成前，新旧系统并存，向后兼容。

---

## 🎯 使用场景示例

### 场景 1: 创建禅道Bug

**你的操作**：
```
你: "Shopee刊登失败，需要创建Bug"
```

**Kiro 的响应**：
```
Kiro: [自动激活 bug-management skill]
      
      我来帮你创建禅道Bug，请提供以下信息：
      
      1. 环境：测试/UAT/亿品生产/梦旋生产？
      2. 重现步骤：具体操作步骤
      3. 错误信息：报错内容或截图
      4. 期望结果：应该发生什么
```

### 场景 2: 分析性能问题

**你的操作**：
```
你: "入库单审核耗时4秒，需要优化"
```

**Kiro 的响应**：
```
Kiro: [自动激活 performance skill]
      
      我来分析性能问题，按照标准流程：
      
      1. 数据收集：查询日志和数据库
      2. 瓶颈识别：找出耗时最多的操作
      3. 优化建议：提供可执行的方案
      4. 效果评估：预估优化收益
```

### 场景 3: 配置 MCP 工具

**你的操作**：
```
你: "#tools"
你: "怎么配置测试数据库连接？"
```

**Kiro 的响应**：
```
Kiro: [已激活 tools skill]
      
      MCP 测试数据库配置步骤：
      
      1. 打开配置文件：.kiro/settings/mcp.json
      2. 找到 test-db 配置
      3. 确认连接信息：
         - 主机：ob-proxy.dev.epean.cn
         - 端口：2883
         - 数据库：ep_trade_test
      4. 测试连接：查询店铺列表
```

### 场景 4: 查询环境信息

**你的操作**：
```
你: "#infrastructure"
你: "UAT环境用的是哪个数据库？"
```

**Kiro 的响应**：
```
Kiro: [已激活 infrastructure skill]
      
      根据环境映射配置：
      
      UAT环境：
      - 数据库：prod-db-epean（⚠️ 使用生产库）
      - SSH日志：uat-ssh
      - ELK日志：all-uat-log-*
      - 部署分支：master（每天自动部署）
      
      ⚠️ 注意：UAT使用生产数据库，操作需谨慎！
```

---

## 📊 Skills 迁移状态

### 当前进度（Week 1 Day 5）

**已完成**：
- ✅ Week 1 Day 1-2: 创建 6 个 skill 目录（扁平结构）
- ✅ Week 1 Day 3: 创建引用检查工具
- ✅ Week 1 Day 4: 迁移 tools skill（2/4 文档）
- ✅ Week 1 Day 5: 测试验证（100%通过率）

**进行中**：
- 🚧 Week 1 Day 5: 编写使用文档（本文档）

**待完成**：
- ⏳ Week 2: 迁移 bug-management 和 performance skills
- ⏳ Week 3: 迁移 development、infrastructure、tools（剩余）、reference skills
- ⏳ Week 4: 配置切换和清理

### 可用性状态

| Skill | 状态 | 可用文档 | 激活方式 |
|:---|:---|---:|:---|
| tools | ✅ 部分可用 | 2/4 | `#tools` |
| bug-management | ⏳ 待迁移 | 0/3 | 暂不可用 |
| performance | ⏳ 待迁移 | 0/3 | 暂不可用 |
| development | ⏳ 待迁移 | 0/3 | 暂不可用 |
| infrastructure | ⏳ 待迁移 | 0/4 | 暂不可用 |
| reference | ⏳ 待迁移 | 0/4 | 暂不可用 |

---

## 🔗 相关文档

- **Skills 主 README**: `.agents/skills/README.md`
- **实施路线图**: `.kiro/archive/2026-03-kiro-governance/implementation-roadmap.md`
- **设计文档**: `.kiro/archive/2026-02-migration/steering-to-skills-refactor-plan.md`
- **测试报告**: `.kiro/temp/week1-day5-test-report.md`

---

## 💬 需要帮助？

如果你在使用 skills 时遇到问题：

1. **查看 README**: 每个 skill 目录都有详细说明
2. **查看测试报告**: 了解已知问题和解决方案
3. **直接访问文件**: 作为备选方案
4. **反馈问题**: 记录在 `.kiro/temp/week1-day5-feedback.md`

---

**创建时间**: 2026-02-26  
**维护者**: Kiro AI + 付春幸  
**版本**: 1.0  
**状态**: ✅ 完成

