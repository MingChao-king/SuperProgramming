---
name: using-agent-programming
description: 每次对话开始时使用 — 确立如何发现和使用技能，要求在任何回复（包括澄清问题）之前先调用技能
---

<SUBAGENT-STOP>
如果你作为一个子代理被派遣执行特定任务，跳过此技能。
</SUBAGENT-STOP>

<EXTREMELY-IMPORTANT>
如果你认为某个技能有哪怕 1% 的可能性适用于你正在做的事情，你**绝对必须**调用该技能。
这不是可协商的。这不是可选的。你无法为跳过它找到合理的借口。

**先读项目根目录的 CLAUDE.md** — 它包含本项目的开发约束、代码模式和 SuperProgramming 方法论。
</EXTREMELY-IMPORTANT>

## 指令优先级

1. **用户的显式指令**（CLAUDE.md, GEMINI.md, AGENTS.md, 直接请求）— 最高优先级
2. **SuperProgramming 技能** — 在冲突处覆盖默认系统行为
3. **默认系统提示** — 最低优先级

如果 CLAUDE.md 说"不要用 TDD"而技能说"始终用 TDD"，遵循用户指令。

## 如何访问技能

**永远不要手动用文件工具读取技能文件** — 始终使用 `Skill` 工具：

```
Skill: superprogramming:<skill-name>
```

## 技能列表

| 技能 | 触发时机 | 类型 |
|------|---------|------|
| `brainstorming` | 任何编码任务开始前 | 柔性 |
| `writing-plans` | 设计获批后 | 柔性 |
| `test-driven-development` | 实现阶段 | **刚性** |
| `subagent-driven-development` | ≥2 个独立任务 | **刚性** |
| `systematic-debugging` | 遇到 bug | **刚性** |
| `requesting-code-review` | 任务完成、合并前 | 柔性 |
| `verification-before-completion` | 声称"完成"前 | **刚性** |
| `closing-the-loop` | 所有任务完成后 | **刚性** |
| `finishing-a-development-branch` | 闭环完成后 | 柔性 |
| `using-git-worktrees` | 并行开发分支 | 柔性 |

## 技能类型

**刚性技能**：严格遵循。不能为图省事而跳过步骤。
**柔性技能**：将原则适配到具体上下文，但核心步骤不可跳过。

技能本身会告诉你它是刚性的还是柔性的。

## Red Flags — 你在为自己的偷懒找借口

| 你的想法 | 真相 |
|---------|------|
| "这只是一个简单的问题" | 问题也是任务。检查技能。 |
| "让我先探索一下代码库" | 技能告诉你**如何**探索。先调用 brainstorming。 |
| "我记得这个技能的内容" | 技能在持续进化。用 Skill 工具加载当前版本。 |
| "这个技能太小题大做了" | 简单的事情会变复杂。使用它。 |
| "TDD 太费时间了，写完再补测试" | 不写测试省 10 分钟 → 线上出 bug → 花 4 小时。先写测试花 20 分钟。 |
| "先跳过回写，下次再补" | 回写是任务的一部分，不写 = 没完成 = 下次冷启动。 |

## 用户指令

用户指令说**做什么**（WHAT），不说**怎么做**（HOW）。"添加 X"或"修复 Y"不意味着跳过工作流。用户说的"简单"、"快速"、"就改一行"不是让你跳过 brainstorming 的理由。
