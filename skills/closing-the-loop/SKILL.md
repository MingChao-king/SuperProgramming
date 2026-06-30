---
name: closing-the-loop
description: Activate after all tasks are complete. Confirm implementation integrity → roadmap recording → cross-project experience extraction → branch cleanup → local git commit. Rigid skill.
---

<EXTREMELY-IMPORTANT>
This skill is a Rigid skill. Not a single step may be skipped. Without closing the loop, knowledge cannot accumulate, and the next task will be a cold start again.
Incomplete closing-the-loop = task not delivered.
</EXTREMELY-IMPORTANT>

# Closing the Loop: Integrity Confirmation + Experience Extraction + Wrap-Up

## Skill Type: Rigid

Follow strictly. Closing the loop is part of coding, not an optional add-on step.

---

## Core Philosophy

**Every coding session is not just about completing the current task — it is about accumulating assets for all future projects.**

```
Without closing the loop: every task → cold start → start from scratch
With closing the loop:   every task → hit existing patterns → complete quickly → write back new experience
```

---

## Core Checklist — 5 Items (Confirm One by One)

### 1. Confirm Implementation Integrity

Check against the writing-plans plan item by item:

- [ ] All task group TDD Triplets completed?
- [ ] All sub-function 8-dimension reviews passed (P0=0, P1=0)?
- [ ] All sub-function full testing passed?
- [ ] Full-scenario integration testing passed?

**Any item incomplete → stop, return to implementing to complete it.**

### 2. Code Repository Git Commit

```bash
cd {project directory} && git add -A && git commit -m "{description}"
```

**Local commit only, no push.** (User specified: all Git operations beyond code stay local.)

Criterion: `git status` is clean.

### 3. Roadmap Append

**Main agent executes** (roadmap requires global perspective). Append to `knowledge-base/{project-name}/{project-name}-roadmap.md`:

#### §5 Implementation Record — New Row

```markdown
| Date | Step | What Was Done | Files Involved | Verification Result |
|------|------|--------------|---------------|--------------------|
| 06-23 | N | {description} | {file list} | ✅ Review passed / ✅ Tests passed |
```

#### §2 Phase Plan Update
- Check off completed Features
- Append newly added Features

#### §3 Interface Inventory Update (if new APIs/skills/components added)
- Backend: new API endpoints
- Frontend: new components/routes
- Plugin: new skills/hooks

#### §4 Encapsulated Method Index Update (if new encapsulated methods added)
- Record new Service methods, utility classes, DTOs, etc.

### 4. Cross-Project Experience Extraction (§6)

**Determine whether this task has cross-project reusable experience:**

```
Discovered a dependency version pitfall? Framework configuration anti-pattern? Cross-project universal solution? Technical pitfall? Performance optimization pattern?
  ├─ Any of the above → delegate subagent to search knowledge-base/通用编码经验.md
  │         ├─ Related entry exists → merge and append
  │         └─ No related entry → create new entry (in L2 format: ✅ Recommend + ❌ Avoid + code comparison)
  └─ None of the above → record in roadmap §6: "No L2 write-back for this task"
```

**L2 write-back delegated to subagent** (通用编码经验.md may be tens of thousands of lines — saves main context):

```
You are a closing-the-loop write-back subagent. Complete the knowledge base experience extraction:

Task Summary:
- What was done: [description]
- Files involved: [list]
- Pitfalls encountered: [describe, or "None"]
- Cross-project experience: [describe, or "None"]

Your work:
1. Read knowledge-base/通用编码经验.md
2. Determine whether this experience already has a related entry
   - Exists → merge and append
   - Does not exist → create new entry in L2 format
3. Update knowledge-base/{project-name}/{project-name}-roadmap.md §6 (Experience Extraction)
4. Report back: which files were modified + summary of modifications
```

### 5. Branch Wrap-Up

After all tasks are complete:

```
→ Present options:
  ├─ Merge into main/master (direct merge or PR)
  ├─ Keep branch (follow-up work remains)
  └─ Discard branch (experimental work)
→ Clean up git worktrees (if any were used)
→ Delete merged local branches
```

---

## Closing the Loop (mini)

Config changes, hotfixes, and other tasks that don't produce new features use the simplified version:

| Core Checklist Item | Full Version | Mini Version |
|--------------------|-------------|-------------|
| 1. Implementation integrity confirmation | ✅ | ✅ |
| 2. Git commit (local) | ✅ | ✅ |
| 3. Roadmap append | ✅ | ✅ (§5 one row only) |
| 4. L2 experience extraction | ✅ | ❌ (unless new technical knowledge) |
| 5. Branch wrap-up | ✅ | ❌ |

---

## Subagent Delegation Suggestions

| Operation | Executor | Reason |
|-----------|---------|--------|
| Roadmap §5 implementation record append | Main agent | Requires global perspective |
| Roadmap §2/§3/§4 update | Main agent | Requires architectural judgment |
| L2 general coding experience search + update | Subagent | File may be tens of thousands of lines |
| Git commit | Subagent | Purely mechanical operation |
| Branch merge/cleanup | Subagent | Purely mechanical operation |

---

## Red Flags — You Are Skipping Closing the Loop

| Your Thought | Reality |
|-------------|---------|
| "This change is too small to be worth recording" | The most valuable experiences come from small changes. Not recording = stepping on the same rake next time. |
| "Too tired, I'll write back next time" | Not writing now = cold start next time. 2 minutes now saves 20 minutes next time. |
| "I'll update the roadmap when I have time" | Roadmap update is the last step of the task. Not updating = task not complete. |
| "This experience only applies to this project" | Judge first, conclude later. Many "project-specific" experiences are actually cross-project reusable. |
| "No need to clean up branches, just leave them" | Uncleaned branches pile up. Check once at every closing-the-loop. |

---

## Completion Criteria

- [ ] 1. All sub-functions completed + reviews passed + tests passed (cross-checked against writing-plans)
- [ ] 2. Code has been git committed (local)
- [ ] 3. Roadmap §2/§3/§4/§5 updated
- [ ] 4. L2 experience extraction completed (write back if applicable, otherwise record "None for this task")
- [ ] 5. Roadmap §6 updated
- [ ] 6. Branch wrap-up options presented
