---
name: implementing
description: Activated after writing-plans completes. Execute TDD (RED→GREEN→REFACTOR) per sub-function → subagent 8-Dimension Review → Sub-function Full Testing → Full-Scenario Integration Testing after all pass. Bug fixes follow the same flow. Rigid skill.
---

<EXTREMELY-IMPORTANT>
This skill is rigid. Every sub-function must complete the full loop: TDD → Review → Sub-function Full Testing.
Skipping any step = unverified code entering the main branch.

If writing-plans already produced a task group breakdown, execute them group by group.
If this is a bug fix (no writing-plans), use Bug Fix Mode.

If you find yourself thinking "this feature is too simple, let me just quickly go through it" — read Red Flags.
</EXTREMELY-IMPORTANT>

# Implementing: TDD → Review → Testing

## Skill Type: Rigid

Follow strictly. Every sub-function goes through the full loop; after all pass, proceed to Full-Scenario Integration Testing. No skipping.

---

## Core Philosophy

**Implementation != writing code. Implementation = writing code + proving it correct + proving it didn't break anything else.**

```
Full loop for one sub-function:
  TDD(RED→GREEN→REFACTOR) → subagent 8-Dimension Review (P0/P1 cleared) → Sub-function Full Testing
                                                                                    │
After all sub-functions pass:                                                       │
  Full-Scenario Integration Testing (cross-feature combination scenarios) ──────────┘
```

---

## Pre-Flight Checks

| # | Check Item | Notes |
|---|------------|-------|
| 1 | writing-plans produced a task list? | Each task broken down into RED→GREEN→REFACTOR |
| 2 | brainstorming produced test scenarios? | 2 layers x 4 scenario types (isolation/boundary/concurrency/exception) |
| 3 | roadmap §4 consulted? | Confirm no wheel reinvention |
| 4 | L2 general experience searched? | Subagent search recommended |

---

## Per-Function Loop (each task group executed independently)

```
writing-plans task list
       │
       ▼
┌─────────────────────────────────────────────────┐
│ For each task group X:                          │
│                                                 │
│ Step 1: TDD                                     │
│   X-R  RED: write test first → confirm FAIL      │
│   X-G  GREEN: minimal impl + L4 signature verify → PASS │
│   X-F  REFACTOR: clean up → keep PASS            │
│                                                 │
│ Step 2: Subagent 8-Dimension Review (parallel dispatch) │
│   → aggregate → clear P0/P1 → record in roadmap §5      │
│                                                 │
│ Step 3: Sub-function Full Testing (subagent parallel)   │
│   → full boundary/null/concurrency/exception coverage    │
│   → all pass → record in roadmap §5              │
│                                                 │
│ Any step fails → fix → retry from the failed step│
└─────────────────────────────────────────────────┘
       │
       ▼
All task groups pass → Full-Scenario Integration Testing → enter closing-the-loop
```

---

## Step 1: TDD Cycle

### RED: Write Test First

```
→ Load from brainstorming test scenarios (2 layers x 4 types)
→ ≥2 test methods per type, ≥8 total
→ Method names express intent: should_xxx_when_yyy
→ Run tests → confirm all FAIL (red)
→ If a test passes immediately → the test is written incorrectly
→ git commit -m "RED: {scenario description}"
```

### GREEN: Minimal Implementation

```
→ Write only the code needed to make current tests pass (YAGNI)
→ ★ L4 Signature Verification: Read at least 3 called key methods, confirm signatures match
→ Run tests → confirm all PASS (green)
→ git commit -m "GREEN: {implementation description}"
```

**L4 Signature Verification (mandatory)**:
```
Calling UserService.getById(Long id)?
→ Read UserService.java → return type? exception declarations? parameter order?
→ LLM method signatures recalled from memory have ~15% chance of being wrong.
   30 seconds of verification > 30 minutes of debugging.
```

### REFACTOR: Clean Up

```
→ Eliminate duplication (DRY), improve naming, extract constants
→ Run tests after each change (keep green)
→ git commit -m "REFACTOR: {cleanup description}"
```

### Bug Fix TDD Pattern

```
bug report → RED (write reproduction test, confirm failure) → GREEN (apply fix, test turns green)
→ same-pattern search (Grep for identical bug pattern, fix all) → REFACTOR
```

---

## Step 2: Subagent 8-Dimension Parallel Review

After TDD REFACTOR commit, immediately dispatch 8 read-only reviewer subagents (parallel).

**P0/P1 must be cleared to zero before entering Step 3.**

### Severity Definitions

| Level | Meaning | Example | Action |
|-------|---------|---------|--------|
| **P0 Critical** | Security vulnerability, data loss, guaranteed crash | SQL injection, unconditional NPE, deadlock | **Block** |
| **P1 Major** | Logic error, regression risk, missing boundary | User A accessing User B data, transaction boundary error | **Block** |
| **P2 Minor** | Pattern deviation, duplicate code, naming issues | Unused existing utility, swallowed exception | Should fix |
| **P3 Suggestion** | Better approach, optional optimization | Stream over for-loop | Optional |

### 8 Reviewers Dispatched in Parallel

All 8 reviewers dispatched simultaneously. Use 5-Segment Prompt, read-only.

| # | Reviewer | Focus | Severity Range |
|---|----------|------|----------------|
| 1 | Correctness Deep-Dive | Per-code-path logic verification, condition coverage, type casting | P0-P3 |
| 2 | Null Safety/Boundaries | Null propagation chain, collection bounds, numeric overflow, string boundaries | P0-P2 |
| 3 | Security Audit | SQL injection, privilege escalation, sensitive data exposure, input validation | P0-P1 |
| 4 | Concurrency Safety | Race conditions, thread safety, idempotency, lock ordering | P0-P1 |
| 5 | Error Handling | Exception coverage completeness, empty catch, degradation, timeout | P0-P2 |
| 6 | Performance/Resources | N+1 queries, connection leaks, unnecessary object creation | P1-P3 |
| 7 | Regression Risk | Call chain impact, API compatibility, database migration | P1-P2 |
| 8 | Contract Verification | API signature/DTO field consistency with design outline/roadmap §3 | P1-P2 |

### 5-Segment Reviewer Prompt Template

```
SCOPE: All source files in this change (new and modified)

GOAL: {Select the corresponding GOAL from the 8 reviewers above}

CONSTRAINTS:
  - Don't trust method names — open the called method's source to verify actual behavior
  - Reference every issue with specific file:line

DO NOT:
  - Do not modify code (you are a read-only reviewer)
  - Do not review content outside your assigned dimension
  - Do not propose "better but out of scope" suggestions

RETURN:
  - Issue list (file:line + specific problem + P0/P1/P2/P3)
  - If no issues: "{Dimension}: Pass"
```

### Main Agent Aggregation

```
Step 1: Deduplicate → same issue found by multiple reviewers, merge into one
Step 2: Prioritize → P0 > P1 > P2 > P3
Step 3: Decide → P0+P1 = 0 → proceed to Step 3 (Sub-function Full Testing)
               → P0+P1 > 0 → implement fixes → re-review only affected dimensions (≤3 rounds)
Step 4: Record → review summary written to roadmap §5
```

### Review Loop (≤3 Rounds)

Each round only fixes P0/P1, no opportunistic refactoring. Only re-dispatch affected reviewers. Round 3 still has P0/P1 → STOP, main agent intervenes to decide.

---

## Step 3: Sub-function Full Testing

After P0/P1 cleared to zero, dispatch test subagents in parallel covering 4 scenario types:

```
Dispatch 4 test subagents in parallel (Agent tool):

Test A — Boundary Conditions:
  Null parameters, null, empty list, max/min values, excessively long strings
  RETURN: pass/fail + test results

Test B — Isolation/Multi-User:
  User A's data ≠ User B's, permission boundaries across roles
  RETURN: pass/fail + test results

Test C — Concurrency/Race Conditions:
  Simultaneous operation conflicts, duplicate submission, idempotency
  RETURN: pass/fail + test results

Test D — Exception Paths:
  Dependent service unavailable, timeout, illegal arguments
  RETURN: pass/fail + test results
```

All pass → this sub-function is complete, record in roadmap §5. Any fails → fix → re-dispatch that test subagent.

---

## Full-Scenario Integration Testing (after all sub-functions pass)

```
Cross-feature combination scenario testing (parallel dispatch):

Scenario 1: Complete business flow (end-to-end)
Scenario 2: Multi-user concurrent operations across different features
Scenario 3: Cross-feature data passing/state change chain
Scenario 4: Combined exception scenarios (Feature A fails → Feature B degrades)
```

All scenarios pass → enter closing-the-loop.

---

## Bug Fix Mode

When there are no writing-plans outputs (bug fix), use this mode.

**Must go through brainstorming first** (workflow requires `brainstorming → implementing(Bug Mode)`). brainstorming should have already written the bug analysis + fix outline to the roadmap. If brainstorming was not executed, implementing must complete this mini version and record to roadmap before writing any code:

```
Pre-flight: Confirm brainstorming is complete (bug analysis + fix outline written to roadmap §5)
  If not → first complete mini brainstorming:
    1. Clarify bug symptoms and impact scope
    2. Identify involved classes/methods (git log recent changes)
    3. Fix approach (1-3 sentences)
    4. ★ Record in roadmap §5 (date + bug description + files involved + fix approach)
       ↓ only then proceed to RED

RED (reproduction test, confirm bug exists)
    → GREEN (minimal fix, test turns green)
    → REFACTOR (search for same bug pattern, fix all occurrences)
    → Subagent Review (8 dimensions, emphasis on Regression Risk)
    → Sub-function Full Testing (4 scenario types)
    → closing-the-loop
```

**Not writing a reproduction test first = you don't know whether you "fixed it" or it "just happened to stop erroring."**
**Not writing to roadmap first = the next person hitting a similar bug starts from scratch.**

---

## Subagent Usage Principles

| Phase | Tool Permission | Parallel Count | Notes |
|-------|----------------|----------------|-------|
| TDD (RED/GREEN/REFACTOR) | Full | 1 | Main agent executes or dispatches 1 implementer |
| 8-Dimension Review | Read-only | 8 parallel | Independent reviewers, no cross-interference |
| Sub-function Full Testing | Read-only | 4 parallel | One per scenario type |
| Full-Scenario Integration Testing | Read-only | 4 parallel | Cross-feature scenarios |

**Git operations** (commit, branch management, etc.) are recommended to be delegated to subagents. The main agent is responsible for roadmap writing and architectural decisions.

---

## Test Anti-Patterns

| Anti-Pattern | Correct Approach |
|--------------|------------------|
| Tests depend on external services | Mock external dependencies |
| Tests have ordering dependencies | Each test is independent |
| Method names don't express intent | `should_throw_when_email_is_empty()` |
| Only testing happy path | Cover null, empty list, max values |
| Writing tests for coverage metrics | Test behavior, not implementation details |
| Writing tests after code is done | Write tests first — the design thinking in RED phase is half of TDD's value |

---

## Red Flags — You Are Skipping Quality Assurance

| What You Think | The Truth |
|----------------|-----------|
| "This feature is too simple, no need for review" | Simple code has boundary bugs too. Simple = review is faster = no excuse. |
| "I can review it myself, no need for 8 reviewers" | You have blind spots reviewing your own code. Independent reviewers don't carry the implementer's assumptions. |
| "The change is tiny, just quickly go through it" | One wrong line can cost 2 hours of debugging. Small changes still need 8-Dimension Review. |
| "8 reviewers is too many, 3-4 is enough" | The 4 dimensions you skip are the escape hatches for bugs. |
| "Write the implementation first, add tests later" | Tests added later become "verify implementation" not "verify behavior." Half of TDD's value is in the RED phase. |
| "I'll just run integration tests manually" | Manual testing = tested this time, forgotten next time. Automation means reproducible every time. |
| "The reviewer flagged an issue but I think it's fine" | Don't override the reviewer. Independent judgment covers your blind spots. |

---

## Completion Criteria

Per sub-function completion:
- [ ] TDD Triplet complete (RED→GREEN→REFACTOR, one git commit each)
- [ ] L4 Signature Verification complete (≥3 key method signatures verified against source)
- [ ] 8-Dimension Review complete (P0=0, P1=0)
- [ ] Sub-function Full Testing passed (4 scenario types fully covered)
- [ ] Review results and test coverage recorded in roadmap §5

After all sub-functions complete:
- [ ] Full-Scenario Integration Testing passed (4 cross-feature scenarios)
- [ ] All tests green
- [ ] Enter closing-the-loop
