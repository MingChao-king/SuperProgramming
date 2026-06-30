---
name: hotfix
description: Activated when user explicitly invokes skill:hotfix. Emergency fix streamlined channel: mini brainstorming -> fix -> test -> closing-the-loop(mini). Flexible skill.
---

# Hotfix: Emergency Fix Streamlined Channel

## Skill Type: Flexible

Only triggered when the user explicitly invokes `skill:hotfix`. Not auto-detected.

---

## When to Use

- User explicitly says "hotfix" / "emergency fix" / "skill:hotfix"
- P0 production failure requiring rapid fix
- Emergency operations such as configuration hotfix or data repair

**Not an emergency -> use the standard workflow (brainstorming -> writing-plans -> implementing -> closing-the-loop).**

---

## Streamlined Flow

```
skill:hotfix triggered
       |
       v
Mini Brainstorming (5 minutes)
  -> Clarify the bug symptom and impact scope
  -> Locate the involved classes/methods (git log recent changes)
  -> Fix approach (1-3 sentences)
  -> Record in roadmap Section 5
       |
       v
TDD Fix
  -> RED: Write a reproduction test, confirm the bug exists
  -> GREEN: Minimal fix, tests turn green
  -> REFACTOR: Search for similar bugs (Grep for the same pattern), fix them together
       |
       v
Quick Review + Testing
  -> Subagent review (8 dimensions, focus: regression risk + correctness)
  -> Sub-function full testing (4 category scenarios)
       |
       v
Closing the Loop (mini)
  -> git commit (local)
  -> Append to roadmap Section 5
  -> If new technical knowledge gained -> L2 extraction
```

---

## Differences from Standard Workflow

| Phase | Standard Workflow | Hotfix |
|-------|------------------|--------|
| brainstorming | Full 4 steps | Mini (only clarify bug + locate code) |
| writing-plans | Feature breakdown + anti-duplication | Skipped |
| implementing | Full per-function cycle | Only fix bug + similar pattern search |
| code review | 8 dimensions, full scope | 8 dimensions, focus on regression + correctness |
| sub-function testing | 4 categories, parallel | 4 categories, parallel |
| integration testing | Cross-scenario | Skipped |
| closing-the-loop | Full 5 items | Mini (3 items) |

---

## Red Flags

| Your Thought | The Truth |
|-------------|-----------|
| "This bug is urgent, skip testing and fix it directly" | Emergency fixes need testing the most -- fixing the wrong thing turns one minute into one hour. |
| "Hotfix doesn't need a reproduction test, just change it" | Not reproducing first = you don't know the root cause = may fix the wrong place. |

---

## Completion Criteria

- [ ] Mini brainstorming completed (bug symptom + involved code + fix approach recorded in roadmap)
- [ ] Reproduction test passes (confirms bug exists)
- [ ] All tests green after fix
- [ ] Subagent review passed (P0=0, P1=0)
- [ ] Similar bug search completed
- [ ] Git commit (local)
- [ ] Roadmap Section 5 appended
- [ ] Closing-the-loop (mini) completed
