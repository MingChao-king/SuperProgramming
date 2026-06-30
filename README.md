# SuperProgramming v3.0

> Claude Code Plugin — Structured Development Workflow + Roadmap as Single Source of Truth + Per-Function Quality Assurance

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-3.0.0-blue.svg)](.claude-plugin/plugin.json)
[![Languages](https://img.shields.io/badge/Languages-EN%20%7C%20ZH-blue.svg)](README-zh.md)

> 📖 中文版：[README-zh.md](README-zh.md) | 中文代码：[zh/](zh/)

---

## What is SuperProgramming?

SuperProgramming is a Claude Code plugin that enforces a **structured development workflow** with quality gates at every step. It replaces ad-hoc coding with a repeatable pipeline:

```
brainstorming → writing-plans → implementing → closing-the-loop
                     ↑                            │
                     └──────── hotfix ────────────┘
```

Every code change — feature, bug fix, or refactor — flows through **requirements clarification → design outline → TDD (test-first) → 8-dimension peer review → full testing → knowledge extraction**. No step is optional.

---

## How It Works

When you say "build me an X", your AI agent automatically:

| Step | Skill | What Happens |
|------|-------|--------------|
| 1 | `brainstorming` | Clarifies requirements via Socratic questioning → detects project type → explores codebase (L4) → writes design outline → saves to 6-section roadmap |
| 2 | `writing-plans` | Breaks work into per-function TDD triplets (RED→GREEN→REFACTOR) → checks roadmap §4 for existing code to reuse → tags L2/L3 patterns |
| 3 | `implementing` | **Per function**: writes failing test → minimal code → refactors → spawns 8 parallel reviewer subagents → runs 4-category full testing → integration tests across all functions |
| 4 | `closing-the-loop` | Confirms completeness → updates roadmap → extracts cross-project experience → commits locally |

**For agents reading this**: the plugin injects a SessionStart bootstrap that tells you exactly which skills to use and when. You will see the skill table and workflow diagram at the start of every session. Use the `Skill` tool — never read skill files manually.

### The 5 Skills

| Skill | Type | Trigger |
|-------|------|---------|
| `superprogramming:brainstorming` | Flexible | Before ANY coding task |
| `superprogramming:writing-plans` | Flexible | After design is approved |
| `superprogramming:implementing` | **Rigid** | Implementation phase (TDD → review → test) |
| `superprogramming:closing-the-loop` | **Rigid** | After all tasks complete |
| `superprogramming:hotfix` | Flexible | User explicitly invokes `skill:hotfix` |

- **Rigid** = every step mandatory. Cannot skip.
- **Flexible** = principles adapt to context, but core steps are enforced.

---

## Installation

### Prerequisites

- [Claude Code](https://claude.ai/code) installed and authenticated
- Git Bash (Windows) or bash (macOS/Linux)

### Step 1: Clone

```bash
git clone https://github.com/MingChao-king/SuperProgramming.git
```

### Step 2: Install the Plugin

```bash
claude /plugin install /path/to/SuperProgramming
```

Or, if already in the directory:

```bash
cd /path/to/SuperProgramming
claude /plugin install .
```

### Step 3: Configure User-Level CLAUDE.md

Add the following to your `~/.claude/CLAUDE.md` (create the file if it doesn't exist):

```markdown
# Personal Coding Instructions

## SuperProgramming Plugin

When performing any code task (writing, modifying, refactoring, bug fixing, implementing
features, adding logic), you MUST use the standard SuperProgramming workflow.

### Standard Flow

SessionStart (auto) → brainstorming → writing-plans → implementing(per-function:
TDD→review→test) → closing-the-loop
                                                    ↑
                                    hotfix ──────────┘

### Skill Dispatch by Scenario

| Scenario | Skill Chain |
|----------|-------------|
| New feature / Modify logic / Refactor | brainstorming → writing-plans → implementing → closing-the-loop |
| Bug fix | brainstorming → implementing(Bug mode) → closing-the-loop |
| Config change | brainstorming → closing-the-loop (mini) |
| Emergency fix | hotfix (explicitly invoke `skill:hotfix`) |

### Hard Rules

- Rigid skills (implementing, closing-the-loop): follow strictly, no step can be skipped.
- Flexible skills (brainstorming, writing-plans, hotfix): principles adapt to context,
  but core steps are enforced.
- If ANY SuperProgramming skill has ≥1% applicability: call it. "Simple" is not an excuse.
- Roadmap first: write/update the 6-section roadmap before any code.
```

### Verify Installation

After installation, start a new Claude Code session. You should see:

```
You are powered by SuperProgramming — structured development with a four-level cache
knowledge system.
```

This means the SessionStart hook is active and the bootstrap has been injected.

---

## Quick Start

Once installed, **you don't need to do anything special**. Just say what you want to build:

```
"Help me add a user logout feature"
"Fix the null pointer bug in checkout"
"Refactor the authentication module"
```

Your agent will automatically enter the SuperProgramming workflow chain. The SessionStart hook injects the skill table into every new session, so your agent always knows which skills are available and when to use them.

---

## Skill Reference (for Agents)

> **If you are an AI agent reading this**: the SessionStart hook already injects this table. This is your authoritative reference if the injection didn't fire.

| Trigger Condition | Skill to Call | Type |
|-------------------|---------------|------|
| Before ANY coding — new feature, refactor, bug fix, config change | `superprogramming:brainstorming` | Flexible |
| Design outline approved by user | `superprogramming:writing-plans` | Flexible |
| Implementation phase (writing code) | `superprogramming:implementing` | **Rigid** |
| All tasks complete | `superprogramming:closing-the-loop` | **Rigid** |
| User says "hotfix", "紧急修复", or explicitly calls hotfix | `superprogramming:hotfix` | Flexible |

**Core constraints** (applied by the skills):
1. **Roadmap first**: write/update the 6-section roadmap before touching code — even for bug fixes
2. **One feature = one test + one review**: each sub-function gets TDD → 8-dimension review → full testing — no batching to end-of-phase
3. **This is a loop**: bug fixes, adjustments, refactors — every code change re-enters the workflow

---

## Roadmap System (6 Sections)

The roadmap is the **single source of truth** for every project. No more scattered design docs, experience notes, and interface inventories.

```
§1. Project Overview — Tech stack, architecture decisions, project type
§2. Phase Planning — Phase goals, feature list, status
§3. Interface Inventory — API / Frontend components / Plugin skills+hooks
§4. Existing Method Index — Anti-duplication index (check before creating anything new)
§5. Implementation Log — Per-feature review results, test coverage, edge cases
§6. Experience Extraction — Pitfalls + cross-project reusable patterns
```

Roadmaps are stored in the plugin's knowledge base, NOT in project files:
`{plugin-root}/knowledge-base/{project-name}/{project-name}-roadmap.md`

---

## Four-Level Cache System

| Level | Content | Update Frequency |
|-------|---------|-----------------|
| L1 Rules | User CLAUDE.md + SessionStart bootstrap | Rarely |
| L2 Patterns | Cross-project pattern library (通用编码经验.md) | On discovering reusable patterns |
| L3 Quick-Ref | Roadmap (6-section single source of truth) | After every task |
| L4 Source | Project source code | Real-time (ultimate truth) |

---

## Philosophy

- **Outline First** — no code without a design outline. Construction without a blueprint guarantees rework.
- **L4 is the Ultimate Truth** — caches can be stale; source code cannot.
- **One Feature, One Review, One Test** — quality is built in per sub-function, not patched at the end of a phase.
- **Roadmap as Single Source of Truth** — design + interfaces + experience + progress in one file.
- **TDD is Not Optional** — write the test first, watch it fail, then write the code.
- **Closing the Loop Compounds** — every task doesn't just finish work; it builds assets for the future.

---

## Project Structure

```
SuperProgramming/
├── .claude-plugin/               # Plugin registry
│   ├── plugin.json               # Name: superprogramming, v3.0.0
│   └── marketplace.json          # Local marketplace entry
├── hooks/                        # Lifecycle hooks
│   ├── hooks.json                # SessionStart on startup|clear|compact
│   ├── session-start             # Bootstrap injection (~500 tokens)
│   └── run-hook.cmd              # Windows bash adapter
├── skills/                       # 5 skills (v3.0: down from 12)
│   ├── brainstorming/            # Requirements + exploration + outline
│   ├── writing-plans/            # Feature-level TDD triplet planning
│   ├── implementing/             # TDD → 8-dim review → 2-layer testing
│   ├── closing-the-loop/         # Roadmap update + experience extraction
│   └── hotfix/                   # Emergency fix streamlined channel
├── zh/                           # 中文版 (Chinese version)
├── CLAUDE.md                     # Project instructions (English)
├── README.md                     # This file (English)
├── README-zh.md                  # Chinese README
└── LICENSE                       # MIT
```

---

## License

MIT License — see [LICENSE](LICENSE)
