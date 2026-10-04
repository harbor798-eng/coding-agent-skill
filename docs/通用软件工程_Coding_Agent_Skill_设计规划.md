# 通用软件工程 Coding Agent Skill —— 设计规划与未来发展

> 用途：在新的独立对话中继续设计和实现一个通用的软件工程 / Coding Agent Skill。  
> 当前阶段：只完成设计规划，不实现具体 Workflow。  
> 核心目标：打造一个类似 Cursor / Codex / Claude Code 优秀实践的、宿主与模型无关的软件工程 Skill。

## 1. 项目定位

这是一个**面向 Coding Agent 的通用软件工程工作规范与工作方法 Skill**。

它不是 OpenClaw、豆包、Cursor、Claude Code、Codex 或某个模型专属的 Skill，也不是某种语言的开发教程。

未来可以被：

- Cursor
- Claude Code
- Codex
- 豆包 Coding Agent
- OpenClaw
- 其他 Coding Agent

使用。

核心思想：

> **不规定 Agent 使用什么模型，而是规定 Agent 应该如何完成一项软件工程任务。**

---

## 2. 当前工程骨架

```text
coding-agent-skill/
├── SKILL.md
├── README.md
├── docs/
│   └── README.md
├── workflows/
│   └── README.md
├── templates/
│   └── README.md
├── references/
│   └── README.md
└── examples/
    └── README.md
```

目录职责：

| 目录 | 职责 |
|---|---|
| `SKILL.md` | Skill 核心入口与核心行为规范 |
| `README.md` | 面向维护者的项目说明 |
| `docs/` | 设计文档、架构说明、ADR、宿主接入说明 |
| `workflows/` | 标准软件工程工作流程 |
| `templates/` | Prompt、任务、计划、报告等标准模板 |
| `references/` | 外部规范、最佳实践、API 备忘、知识参考 |
| `examples/` | 端到端真实使用案例 |

目前只建立骨架，没有提前填充具体工作流。

---

## 3. 核心设计理念

这个 Skill 不是告诉 AI 每一行代码怎么写，而是：

> **规定 AI 应该如何完成一次完整的软件工程任务。**

核心生命周期：

```text
用户需求
   ↓
理解项目
   ↓
分析现状
   ↓
制定方案
   ↓
执行修改
   ↓
验证
   ↓
发现问题
   ↓
修复
   ↓
再次验证
   ↓
交付
```

而不是：

```text
看到需求 → 直接写代码
```

---

## 4. 核心目标

### 4.1 防止没理解项目就开始写代码

应该：

```text
理解项目结构
↓
寻找相关代码
↓
理解现有实现
↓
确认依赖关系
↓
再开始修改
```

### 4.2 防止需求被擅自缩水

不能因为实现困难、代码混乱、测试麻烦等原因自行降低需求。

正确方式：

```text
发现困难
↓
分析原因
↓
寻找方案
↓
确实需要改变需求时，明确向用户说明
```

而不是偷偷把需求做小。

### 4.3 最小必要修改

优先解决当前问题所需的最小范围修改，避免无关重构、无意义文件移动、无关依赖升级。

### 4.4 不猜测

如果关键事实未知：

```text
搜索 / 读取 / 检查 / 运行 / 验证
```

而不是猜。

### 4.5 修改必须验证

不能“写完代码就宣布完成”。

应该根据任务进行：

```text
编译
测试
运行
API 验证
GUI 验证
数据验证
```

### 4.6 错误必须分析

禁止无限盲目重试：

```text
失败 → 重试 → 失败 → 再重试
```

应该：

```text
失败
↓
分析错误
↓
定位原因
↓
修改
↓
重新验证
```

---

## 5. Agent 的自主程度

Skill 不应该把 Coding Agent 限制成机械执行器。

允许 Agent：

- 自主理解目标
- 自主调查项目
- 自主定位问题
- 自主选择工具
- 自主制定局部方案
- 自主执行
- 自主验证
- 自主修复明显问题

但以下情况应升级给用户：

- 产品需求变化
- 重大架构变化
- 破坏性操作
- 无法确定的关键决策

核心原则：

> **允许自主执行，但不允许自主改变目标。**

---

## 6. 任务级自主，而不是动作级思考

不要：

```text
点击一次 → 模型重新思考
读取一个文件 → 模型重新思考
运行一次命令 → 模型重新思考
```

而应该：

```text
理解任务
↓
制定一段执行计划
↓
连续执行
↓
验证
↓
遇到异常再重新判断
```

例如：

```text
目标：
检查 JEVE 启动后为什么仍然显示旧界面

计划：
1. 定位启动入口
2. 检查 Scene/FXML 加载关系
3. 搜索旧界面引用
4. 启动程序
5. 验证实际加载页面
6. 如发现问题则修复
7. 再次启动验证
```

---

## 7. 软件工程任务生命周期

未来统一为：

```text
Requirement
    ↓
Discovery
    ↓
Analysis
    ↓
Planning
    ↓
Implementation
    ↓
Verification
    ↓
Review
    ↓
Delivery
```

即：

```text
需求
↓
项目探索
↓
现状分析
↓
方案设计
↓
代码实现
↓
验证
↓
审查
↓
交付
```

---

## 8. Workflows 的发展方向

`workflows/` 不应一开始堆很多流程。

未来可以逐步增加：

```text
project-discovery.md
requirement-analysis.md
implementation.md
debugging.md
testing.md
code-review.md
refactoring.md
ui-development.md
api-development.md
delivery.md
```

第一阶段建议只验证三个核心流程：

```text
project-discovery
implementation
verification
```

---

## 9. Templates 的发展方向

未来用于标准化任务、计划和结果：

```text
task.md
requirement.md
plan.md
implementation-report.md
test-report.md
bug-report.md
review-report.md
```

目标：

> **让不同 Coding Agent 之间可以使用相对统一的任务、计划和结果格式。**

---

## 10. References 的发展方向

用于保存：

- Coding Agent 最佳实践
- Cursor / Codex / Claude Code 等工具的优秀设计
- 软件工程规范
- Git 工作规范
- 测试规范
- API 设计规范
- 项目结构规范
- Agent 行为规范

必须区分：

```text
references/
= 参考知识

SKILL.md
= 本 Skill 必须遵守的核心规则
```

References 不等于强制规则。

---

## 11. Docs 的发展方向

用于记录 Skill 自身设计：

```text
architecture.md
design-principles.md
workflow-design.md
host-integration.md
decision-records/
evolution.md
```

未来建议使用 ADR（Architecture Decision Record）记录重要设计决策：

```text
为什么选择这个方案？
为什么放弃另一个方案？
```

---

## 12. Examples 的发展方向

最终使用真实案例验证 Skill：

```text
examples/
├── add-feature/
├── fix-bug/
├── refactor/
├── ui-change/
└── complex-task/
```

每个案例可以展示：

```text
需求
↓
Agent 调查
↓
计划
↓
修改
↓
验证
↓
最终结果
```

---

## 13. 与 Cursor 类 Coding Agent 的关系

可以借鉴 Cursor、Codex、Claude Code 等优秀思想，但不复制某个产品的实现。

重点研究：

- 项目规则
- Agent Instructions
- Skills
- Context 管理
- 代码库探索
- 任务规划
- 工具调用
- 自动验证
- 错误恢复
- 长任务执行
- 上下文压缩
- 任务状态管理

目标不是“复制 Cursor”，而是：

> **提取不同 Coding Agent 中具有普适价值的软件工程方法。**

---

## 14. 与 OpenClaw 的关系

二者属于不同层级：

```text
Coding Agent Skill
↓
规定“代码 Agent 应该怎么完成软件工程任务”
```

而：

```text
OpenClaw
↓
负责“怎么调度模型、工具、电脑和任务”
```

所以：

```text
OpenClaw
   ↓
可以加载这个 Skill
```

但：

```text
这个 Skill ≠ OpenClaw Skill
```

同样可以被 Cursor、Claude Code、Codex、豆包等使用。

---

## 15. 与轻度调度器方案的关系

之前设计的 OpenClaw 轻度调度器：

```text
自然语言
↓
模型理解
↓
结构化任务
↓
OpenClaw
↓
工具执行
↓
验证
↓
必要时重新调用模型
```

可以与本 Skill 形成上下层关系：

```text
              Coding Agent Skill
                     │
             软件工程工作规范
                     │
                     ▼
              Coding Agent
                     │
            ┌────────┴────────┐
            ▼                 ▼
         OpenClaw           Cursor
            │                 │
            ▼                 ▼
          Tools             Tools
```

Skill 规定：

> **怎么做软件工程。**

宿主负责：

> **怎么调用工具和模型。**

---

## 16. 最重要的边界

这个 Skill 不应该变成一个“大型万能 Prompt”。

不要把所有规则全部塞进 `SKILL.md`。

最终应该形成：

```text
SKILL.md
    ↓
核心规则
    │
    ├── workflows/
    │      ↓
    │    具体流程
    │
    ├── templates/
    │      ↓
    │    标准格式
    │
    ├── references/
    │      ↓
    │    外部知识
    │
    └── examples/
           ↓
         实际案例
```

这样方便按需加载，也能控制上下文规模。

---

## 17. 未来核心能力模型

成熟后，希望 Coding Agent 具备：

```text
理解
↓
探索
↓
分析
↓
规划
↓
执行
↓
验证
↓
诊断
↓
修复
↓
复验
↓
交付
```

而不是只有：

```text
生成代码
```

---

## 18. 后期可加入的高级能力

### 项目记忆

理解：

```text
项目目标
架构
技术债
历史决策
当前进度
```

### 任务状态

例如：

```text
TODO
IN_PROGRESS
BLOCKED
VERIFYING
DONE
```

### 自动恢复

例如：

```text
编译失败
↓
读取错误
↓
定位代码
↓
修复
↓
重新编译
```

需要设置最大尝试次数和升级条件，防止无限循环。

### 风险分级

未来可对操作定义：

```text
LOW
MEDIUM
HIGH
CRITICAL
```

例如：

```text
读取文件 → LOW
修改代码 → MEDIUM
删除大量代码 → HIGH
删除数据库 → CRITICAL
```

高风险操作采用更严格的确认机制。

### 多 Agent 协作

后期可以考虑：

```text
Planner
Coder
Reviewer
Tester
```

但第一阶段不要复杂化。

---

## 19. 发展路线

```text
Phase 0
工程骨架
    ↓
Phase 1
核心规则
    ↓
Phase 2
基础工作流
    ↓
Phase 3
标准模板
    ↓
Phase 4
知识参考
    ↓
Phase 5
真实案例验证
    ↓
Phase 6
跨 Agent 测试
    ↓
Phase 7
持续迭代
```

### Phase 0：当前

已完成：

- 工程目录
- 基础 README
- 目录职责
- 宿主中立设计

### Phase 1：核心规则

重点设计 `SKILL.md`：

- Skill 定位
- 核心原则
- Agent 行为边界
- 自主程度
- 验证原则
- 需求保护原则

### Phase 2：基础 Workflow

先做：

```text
project-discovery
implementation
verification
```

### Phase 3：Templates

建立：

```text
Task
Plan
Report
Review
```

等模板。

### Phase 4：References

系统研究：

```text
Cursor
Codex
Claude Code
其他 Coding Agent
```

提取优秀实践。

### Phase 5：Examples

使用真实项目验证，例如：

```text
Java Spring Boot
JEVE JavaFX
其他项目
```

### Phase 6：跨宿主验证

分别尝试：

```text
Cursor
Codex
Claude Code
豆包
OpenClaw
```

观察：

- 规则能否被正确理解
- Workflow 是否可执行
- 模板是否通用
- 哪些内容依赖宿主

然后继续抽象。

---

## 20. 长期设计原则

> **先观察不同 Coding Agent 的优秀实践，再抽象共性；不要先假设所有 Agent 都应该按照某一种方式工作。**

即：

```text
Cursor
Codex
Claude Code
豆包
其他 Agent
        ↓
共同能力
        ↓
抽象
        ↓
本 Skill
```

而不是：

```text
先设计一套
↓
强行要求所有 Agent 遵守
```

---

## 21. 最终愿景

最终得到的不是一个：

> “让 AI 更会写代码的 Prompt”。

而是：

> **让不同 Coding Agent 按照比较成熟的软件工程方法完成工作的通用 Skill。**

理想流程：

```text
用户
↓
需求
↓
Coding Agent
↓
加载本 Skill
↓
理解项目
↓
分析需求
↓
制定计划
↓
执行
↓
验证
↓
修复
↓
交付
```

Agent 使用什么：

```text
模型
工具
IDE
Terminal
GUI
```

都不是这个 Skill 的核心关注点。

---

## 22. 当前明确不做

第一阶段暂时不：

- 实现具体代码生成策略
- 实现具体 Debug Workflow
- 实现 Git 自动化
- 实现多 Agent
- 实现模型路由
- 实现 OpenClaw 专属功能
- 实现 Cursor 专属功能
- 实现复杂记忆系统
- 实现复杂风险控制系统
- 收集大量参考资料
- 一次创建大量 Skill 文件

当前最重要的事情：

> **先把 Skill 的核心软件工程理念定义清楚。**

---

## 23. 新对话启动方式

在新的独立对话中提供本文件，并说明：

> “这是我正在设计的通用 Coding Agent Skill 的设计规划。请基于这份文档继续，不要重新定义项目目标。当前已经完成工程骨架，现在从 Phase 1 开始设计 `SKILL.md`。”

然后按照：

```text
Phase 1
↓
Phase 2
↓
Phase 3
...
```

逐步推进。

不要一次把整个 Skill 做完。

---

## 24. 一句话总结

> **这个项目不是要造一个新的 Cursor，而是要提炼 Cursor、Codex、Claude Code 等 Coding Agent 中优秀的软件工程方法，把“如何理解项目、规划任务、执行修改、验证结果、处理错误和保护需求”沉淀成一个独立、通用、宿主无关的 Coding Agent Skill。**
