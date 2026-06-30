# SuperProgramming v3.0

> Claude Code 插件 — 结构化开发工作流 + Roadmap 统一真相源 + 功能级细粒度质量保证

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.0-blue.svg)](.claude-plugin/plugin.json)
[![Languages](https://img.shields.io/badge/Languages-EN%20%7C%20ZH-blue.svg)](README.md)

> 📖 English version: [README.md](README.md) | English code: root directory

---

## SuperProgramming 是什么？

SuperProgramming 是一个 Claude Code 插件，强制执行**结构化开发工作流**，每个步骤都有质量门禁。它用可重复的流水线取代临时编码：

```
brainstorming → writing-plans → implementing → closing-the-loop
                     ↑                            │
                     └──────── hotfix ────────────┘
```

每一次代码变更——无论是新功能、Bug 修复还是重构——都会经过**需求澄清 → 设计大纲 → TDD（测试先行）→ 8 维同行审查 → 全量测试 → 知识提炼**。没有一步可选。

---

## 工作机制

当你说"帮我做一个 XXX"时，你的 AI Agent 自动：

| 步骤 | 技能 | 做什么 |
|------|------|--------|
| 1 | `brainstorming` | 苏格拉底式需求澄清 → 检测项目类型 → 勘探代码库（L4）→ 写设计大纲 → 保存到 6 节 roadmap |
| 2 | `writing-plans` | 拆分为逐功能 TDD 三元组（RED→GREEN→REFACTOR）→ 查 roadmap §4 防重复造轮子 → 标注 L2/L3 复用 |
| 3 | `implementing` | **逐功能**：写失败测试 → 最小实现 → 重构 → 派发 8 个并行审查子代理 → 4 类全量测试 → 跨功能集成测试 |
| 4 | `closing-the-loop` | 确认完整性 → 更新 roadmap → 提炼跨项目经验 → 本地 commit |

**给 Agent 看的**：插件通过 SessionStart 钩子在每次会话开始时注入技能表和流程图。使用 `Skill` 工具调用技能——不要手动读取技能文件。

### 5 个技能

| 技能 | 类型 | 触发条件 |
|------|------|---------|
| `superprogramming:brainstorming` | 柔性 | 任何编码任务前 |
| `superprogramming:writing-plans` | 柔性 | 设计获批后 |
| `superprogramming:implementing` | **刚性** | 实现阶段（TDD → 审查 → 测试） |
| `superprogramming:closing-the-loop` | **刚性** | 所有任务完成后 |
| `superprogramming:hotfix` | 柔性 | 用户显式调用 `skill:hotfix` |

- **刚性** = 每一步不可跳过
- **柔性** = 原则适配上下文，但核心步骤强制执行

---

## 安装

### 前置条件

- 已安装并认证 [Claude Code](https://claude.ai/code)
- Git Bash（Windows）或 bash（macOS/Linux）

### 第一步：克隆仓库

```bash
git clone https://github.com/MingChao-king/SuperProgramming.git
```

### 第二步：安装插件

```bash
claude /plugin install /path/to/SuperProgramming
```

或者已在该目录中：

```bash
cd /path/to/SuperProgramming
claude /plugin install .
```

### 第三步：配置用户级 CLAUDE.md

将以下内容添加到 `~/.claude/CLAUDE.md`（如果文件不存在则新建）：

```markdown
# 个人编码指令

## SuperProgramming 插件

当执行任何代码任务（编写、修改、重构、修复 Bug、实现功能、添加逻辑）时，
必须使用 SuperProgramming 插件的标准工作流。

### 标准流程

SessionStart (auto) → brainstorming → writing-plans → implementing(逐功能:
TDD→审查→测试) → closing-the-loop
                                                    ↑
                                    hotfix ──────────┘

### 按场景分派

| 场景 | 技能链 |
|------|--------|
| 新功能 / 修改逻辑 / 重构 | brainstorming → writing-plans → implementing → closing-the-loop |
| Bug 修复 | brainstorming → implementing(Bug模式) → closing-the-loop |
| 配置变更 | brainstorming → closing-the-loop（mini） |
| 紧急修复 | hotfix（显式调用 `skill:hotfix`） |

### 核心约束

- 刚性技能（implementing、closing-the-loop）：严格遵循，任一步骤不可跳过
- 柔性技能（brainstorming、writing-plans、hotfix）：原则适配上下文，但核心步骤强制执行
- 若任一 SuperProgramming 技能有 ≥1% 适用性：必须调用它。"简单"不是跳过的理由
- Roadmap 先行：写任何代码前先写/更新 6 节 roadmap
```

### 验证安装

安装后启动新的 Claude Code 会话，你应该看到：

```
You are powered by SuperProgramming — structured development with a four-level cache
knowledge system.
```

这意味着 SessionStart 钩子已激活，引导信息已注入。

---

## 快速开始

安装完成后，**你不需要做任何特殊操作**。直接说你要做什么：

```
"帮我加一个用户登出功能"
"修复结账时的空指针异常"
"重构认证模块"
```

你的 Agent 会自动进入 SuperProgramming 工作流。SessionStart 钩子在每次新会话中注入技能表，因此你的 Agent 始终知道哪些技能可用以及何时使用。

---

## 技能速查（给 Agent 看）

> **如果你是一个阅读本文的 AI Agent**：SessionStart 钩子已经注入了此表格。如果注入未触发，以下是你的权威参考。

| 触发条件 | 调用的技能 | 类型 |
|---------|-----------|------|
| 任何编码前——新功能、重构、Bug 修复、配置变更 | `superprogramming:brainstorming` | 柔性 |
| 设计大纲被用户批准后 | `superprogramming:writing-plans` | 柔性 |
| 实现阶段（写代码） | `superprogramming:implementing` | **刚性** |
| 所有任务完成后 | `superprogramming:closing-the-loop` | **刚性** |
| 用户说"hotfix"、"紧急修复"或显式调用 hotfix | `superprogramming:hotfix` | 柔性 |

**核心约束**（由技能强制执行）：
1. **Roadmap 先行**：写任何代码前先写/更新 6 节 roadmap——包括 Bug 修复
2. **一功能一测一审**：每个子功能走完整 TDD → 8 维审查 → 全量测试，不积压到阶段末尾
3. **这是一个循环**：Bug 修复、功能调整、重构——任何代码变更都重新进入工作流

---

## Roadmap 体系（6 节）

Roadmap 是每个项目的**唯一真相源**，不再有分散的设计文档、经验笔记和接口清单。

```
§1. 项目概览 — 技术栈、架构决策、项目类型
§2. 阶段规划 — 阶段目标 + Feature 清单 + 状态
§3. 接口清单 — API / 前端组件路由 / 插件技能+Hook（按类型自适应）
§4. 现有封装方法索引 — 防重复造轮子（新建任何类/方法前先查此节）
§5. 实现记录 — 每功能审查结果、测试覆盖、边界情况
§6. 经验提炼 — 踩坑 + 可跨项目复用的模式
```

Roadmap 存储在插件知识库中，不写入项目文件：
`{plugin-root}/knowledge-base/{project-name}/{project-name}-roadmap.md`

---

## 四级缓存知识体系

| 层级 | 内容 | 更新频率 |
|------|------|----------|
| L1 守则 | 用户级 CLAUDE.md + SessionStart 引导 | 极少 |
| L2 通用经验 | 通用编码经验.md（跨项目模式库） | 发现通用解法时 |
| L3 项目速查 | Roadmap（6 节统一真相源） | 每次任务后 |
| L4 源码 | 项目源代码 | 实时（唯一真相源） |

---

## 哲学

- **大纲先行** — 写任何代码前必须先有设计大纲。没有蓝图的施工必然返工
- **L4 是唯一真相源** — 缓存可能过期，源码不会
- **一功能一测一审** — 质量内建于每个子功能，而非阶段末尾补
- **Roadmap 唯一真相源** — 设计 + 接口 + 经验 + 进度全在一个文件
- **TDD 不是选项** — 先写测试，看到它失败，再写代码
- **闭环积累** — 每次任务不只是完成任务，是为未来积累资产

---

## 项目结构

```
SuperProgramming/
├── .claude-plugin/               # 插件注册
│   ├── plugin.json               # 名称: superprogramming, v3.0.0
│   └── marketplace.json          # 本地市场条目
├── hooks/                        # 生命周期钩子
│   ├── hooks.json                # SessionStart 在 startup|clear|compact 触发
│   ├── session-start             # 引导注入（约 500 tokens）
│   └── run-hook.cmd              # Windows bash 适配器
├── skills/                       # 5 个技能（v3.0：从 12 个精简）
│   ├── brainstorming/            # 需求澄清 + 勘探 + 大纲
│   ├── writing-plans/            # 功能级 TDD 三元组规划
│   ├── implementing/             # TDD → 8维审查 → 2层测试
│   ├── closing-the-loop/         # Roadmap 更新 + 经验提炼
│   └── hotfix/                   # 紧急修复精简通道
├── zh/                           # 中文版
├── CLAUDE.md                     # 项目指令（英文）
├── README.md                     # 英文 README
├── README-zh.md                  # 本文（中文 README）
└── LICENSE                       # MIT
```

---

## 许可证

MIT License — 详见 [LICENSE](LICENSE)
