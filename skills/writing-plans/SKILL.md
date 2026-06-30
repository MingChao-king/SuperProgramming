---
name: writing-plans
description: Activate after design is approved. Break work into function-level TDD Triplet tasks → reference roadmap §4 Anti-Duplication → annotate L2/L3 reuse. Reference implementing and closing-the-loop at end of plan. Flexible skill.
---

<EXTREMELY-IMPORTANT>
After the design outline is approved, you MUST use this skill to create the implementation plan.
Every function task MUST be broken into RED→GREEN→REFACTOR Triplet. "Write code first, add tests later" is forbidden.
The plan MUST reference implementing (per-function implementation + review + testing) and closing-the-loop at the end.
</EXTREMELY-IMPORTANT>

# Writing Plans: Function Breakdown + Anti-Duplication + Reuse Annotation

## Skill Type: Flexible

The granularity and grouping of task breakdown may adapt to context, but the TDD Triplet structure, Anti-Duplication checks, and execution chain completeness are mandatory.

---

## Execution Chain

```
brainstorming(design outline) → writing-plans(break into TDD Triplets) → implementing(per function) → closing-the-loop
```

**implementing carries the three phases of implementation, review, and testing.** writing-plans is responsible for task breakdown + reuse annotation, not for defining how to review and test — that is implementing's responsibility.

---

## Step 1: Function Breakdown — TDD Triplet

### Granularity Standard

| Too Coarse | Just Right |
|-----------|-------------|
| "Implement user authentication module" | Break into: login, register, token refresh — each goes through TDD Triplet |
| "Refactor database layer" | Break into: change connection config, change query methods, change transaction boundaries — each goes through TDD |

**One function = one independently verifiable, user-visible behavior.** After breakdown, each function goes through one complete implementing cycle (TDD → review → testing).

### Project Type Adaptation

| Project Type | Function Breakdown Unit | "Test Passes" Meaning |
|-------------|------------------------|----------------------|
| **Backend** | API endpoint / Service method | Correct response + boundary coverage |
| **Frontend** | Component / Page / Route | Correct rendering + correct interaction behavior |
| **Full-stack** | Frontend function + Backend endpoint (paired) | Frontend→Backend→Response full chain |
| **Plugin** | Skill / Hook / Config | Complete content + correct format |

### TDD Triplet Template (Mandatory)

```
Task Group X: {function description}

X-R  RED: Write test first
     ├─ File: test file path
     ├─ Content: Write tests based on brainstorming's 2-layer × 4-category scenarios
     ├─ Verify: Tests → FAIL (Red)
     └─ Commit: git commit -m "RED: {scenario description}"

X-G  GREEN: Minimal implementation
     ├─ File: implementation file path
     ├─ Content: Write only the code that makes X-R tests pass, YAGNI
     ├─ L4 Signature Verification: Read at least 3 key method signatures being called
     ├─ Verify: Tests → PASS (Green)
     ├─ Depends on: X-R
     └─ Commit: git commit -m "GREEN: {implementation description}"

X-F  REFACTOR: Clean up
     ├─ File: same as X-G
     ├─ Content: Eliminate duplication, improve naming, extract constants, keep tests green
     ├─ Verify: Tests → PASS
     ├─ Depends on: X-G
     └─ Commit: git commit -m "REFACTOR: {cleanup description}"
```

### Dependency Direction

```
❌ Wrong: D3 (implement Service) → T1 (test Service)    ← Waterfall
✅ Right: T1-R → T1-G → T1-F (GREEN depends on RED)
```

### Pure Data Structure Exemption

Entity/Enum/DTO containing only field declarations/getters/setters/annotations, with zero `if`/`for`/`throw`/`return` → may skip TDD, annotate "No TDD (pure data structure)." Any branch or loop → must use Triplet.

---

## Step 2: Anti-Duplication (Mandatory Checklist)

**Execute item by item before planning each new class/method.**

| # | Check Item | Action |
|---|-----------|--------|
| 1 | **Check roadmap §4 first** | Read `knowledge-base/{project-name}/{project-name}-roadmap.md` §4 (existing encapsulated method index), confirm no similar encapsulation |
| 2 | New Service method? | Grep method name `**/service/**/*.java` |
| 3 | New DTO/VO? | Glob `**/dto/**`, `**/vo/**` |
| 4 | New util method? | Grep method signature `**/utils/**`, `**/common/**` |
| 5 | New Mapper method? | Grep method name `**/mapper/**` |
| 6 | New constant/enum? | Grep constant name `**/enums/**` |
| 7 | New dependency? | Grep artifactId `pom.xml` |
| 8 | Functionally similar method? | Grep business keyword `**/*.java` (fuzzy search) |

**Judgment criteria**: Existing method name differs by ≤2 words AND parameter list is essentially the same → reuse. Existing DTO covers ≥80% of fields → extend fields.

**roadmap §4 takes priority over code search.** The roadmap already indexes the project's encapsulated methods — check roadmap first to avoid missing anything.

---

## Step 3: L2/L3 Reuse Annotation

From brainstorming's L4 exploration results, annotate reusable components for each task:

```
Task Group T1: CouponTemplateService
  T1-R → Reuse: Existing Service test pattern (reference XxxServiceTest)
  T1-G → Reuse: BaseEntity's createTime/updateTime
```

**L2 Search**: Search `knowledge-base/通用编码经验.md` for optimal solutions in the current problem domain. **Subagent search is recommended** (L2 file may be tens of thousands of lines — saves main context).

---

## Plan Output Format

```markdown
## Implementation Plan: {feature name}

> Linked Design Outline: {roadmap phase} | Project Type: {backend/frontend/full-stack/plugin}

### Reusable Component Summary
| Existing Component | Path | Source | Used In |
|-------------------|------|--------|---------|
| xxx | xxx.java | roadmap §4 | T1-G |

### Function-Level Task Groups

#### Task Group X: {function description}
| # | Sub-Task | File | Key Points | Verification | Depends On |
|---|----------|------|-----------|-------------|------------|
| X-R | RED: {test description} | Test file | 2-layer × 4-category scenarios | Tests → FAIL | — |
| X-G | GREEN: {implementation description} | Impl file | Minimal impl + L4 verify ≥3 signatures | Tests → PASS | X-R |
| X-F | REFACTOR: {cleanup description} | Same as X-G | DRY + naming + constants | Tests → PASS | X-G |

#### Pure Data Structure Tasks
| # | Task | File | Verification | Depends On |
|---|------|------|-------------|------------|
| B1 | Create XxxEnum | enums/XxxEnum.java | Compiles | — |

### Execution Order

Each task group independently goes through implementing (TDD → review → sub-function full testing).
After all task groups pass, execute full-scenario integration testing.
Finally execute closing-the-loop.
```

---

## Red Flags — Your Plan Has Structural Problems

| Your Thought | Reality |
|-------------|---------|
| "List all implementations first, put testing as the last phase" | This is Waterfall. TDD requires RED before every function. |
| "Writing a new one is faster, searching for existing ones is too slow" | Writing new: 5 min + debugging: 20 min. Check roadmap §4 first, then Grep — total 2 min. |
| "Tests depend on implementation, so implementation must be done first" | Dependency direction is reversed. RED is written first, GREEN depends on RED. |
| "This function is too special, there's definitely nothing existing" | Check roadmap §4 first — it exists precisely to answer "is there something existing?" |
| "The plan is just a task list, execution chain is something for execution time" | Plan structure determines execution structure. Tests at the end → execution becomes "write code then backfill tests." |

---

## Completion Criteria

Must confirm before entering implementing:

- [ ] All functions broken into RED → GREEN → REFACTOR Triplets (pure data structures excepted)
- [ ] Each Triplet has correct dependency direction (GREEN depends on RED)
- [ ] Anti-Duplication checks executed (roadmap §4 + Grep, 8 items total)
- [ ] Each task annotated with reusable existing components
- [ ] L2 search completed (subagent recommended), relevant patterns annotated
- [ ] No standalone "testing phase" — testing is interleaved within function Triplets
- [ ] User has confirmed the task list
