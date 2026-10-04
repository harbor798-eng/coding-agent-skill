# docs/

存放与本 Skill 相关的**说明性、决策性**文档。

## 放什么

- 设计文档：为什么这样划分目录、为什么定这些约定
- 决策记录（ADR）：关键取舍、被否决的方案
- 接入指南：如何在 Cursor / Claude Code / Codex / 豆包 Coding Agent / OpenClaw 等不同宿主里加载本 Skill（按宿主分文件，不要写在同一段里混着说）
- 版本说明、迁移记录

## 不放什么

- 可执行的工作流步骤 → 去 `workflows/`
- 可复用的提示词或文件模板 → 去 `templates/`
- 外部规范的摘抄 / 速查表 → 去 `references/`
- 带具体输入输出的演示案例 → 去 `examples/`
