---
name: brainstorming
description: Activated before any coding begins. Socratic requirements clarification → project type detection → L4 exploration → outline-first design → save to 6-section roadmap. Flexible skill.
---

<EXTREMELY-IMPORTANT>
You must use this skill before any coding task begins. Do not jump straight to writing code.
Even if you think "the requirements are already clear," walk through this process.
</EXTREMELY-IMPORTANT>

# Brainstorming: Requirements Clarification + Project Type Detection + L4 Exploration + Outline First

## Skill Type: Flexible

All four steps are mandatory. Adapt the principles to the specific context.

---

## Workflow

```
User need → Step 1: Requirements Clarification (Socratic questioning + test scenario design)
          → Step 2: Project Type Detection + L4 Exploration (project landscape + reusable components)
          → Step 3: Outline First (design document → save 6-section roadmap to plugin knowledge-base)
          → Proceed to writing-plans
```

---

## Step 1: Requirements Clarification

**Do not assume you understand the user's intent.** Use questioning to refine vague ideas.

### Socratic Questioning Framework (cover at least 6 dimensions)

| Dimension | Questions to clarify |
|-----------|---------------------|
| **Goal** | What problem does it solve? Who will use it? |
| **Scope** | What does the MVP include? What is explicitly out of scope? |
| **Constraints** | Tech stack limitations? Performance requirements? Compatibility? |
| **Alternatives** | Is there a simpler approach? Can existing tools/libraries solve this directly? |
| **Acceptance Criteria** | How do we know it's done? What counts as "correct"? |
| **Test Scenarios** | Multi-user isolation? Permission boundaries? Null/empty/extreme values? Concurrent conflicts? |

### Two-Layer Test Scenario Design (mandatory)

**Implementation limits your imagination. If you think about tests after writing code, you will only test scenarios the implementation can pass.**

| Layer | Coverage | Timing |
|-------|----------|--------|
| **Sub-function Full Testing** | Boundary/null/exception/concurrency for each sub-function, >=2 items per category | After each sub-function completes during implementing phase |
| **Cross-Scenario Integration Testing** | Combined scenarios across functions, complete business flows | After all sub-functions pass |

| Category | Examples |
|----------|----------|
| **Multi-user / Isolation** | User A's data != User B's data; permission boundaries across roles |
| **Boundary Conditions** | null, empty list, max value, extremely short/long input |
| **Concurrency / Race Conditions** | Simultaneous operation conflicts? Duplicate submissions? |
| **Exception Paths** | Dependent service down? Timeout? Invalid arguments? |

At least 2 test scenarios per category per layer, written into the "Test Scenario Design" column of the design outline.

### Key Behavioral Guidelines

- **Present design in chunks**: Present in 3-5 digestible sections, wait for user confirmation on each before continuing
- **Proactively suggest alternatives**: Speak up when you see a simpler approach
- **Clearly mark uncertainties**: Mark as "TBD" rather than guessing
- **Save to knowledge-base**: Save outline to `knowledge-base/{project-name}/{project-name}-roadmap.md`

---

## Step 2: Project Type Detection + L4 Exploration

### 2.1 Project Type Detection (mandatory)

**Before exploration, determine the project type.** Different types use different exploration checklists and implementation strategies.

Auto-detection rules:

```
Detection items:
├─ .claude-plugin/ or plugin.json exists? → Plugin project
├─ pom.xml or build.gradle exists?
│   ├─ Also has package.json (vue/react/angular)? → Full-stack project
│   ├─ Java build file only? → Pure backend project
│   └─ package.json only? → Pure frontend project
└─ No build files? → General project (docs/scripts)
```

| Project Type | Exploration Focus | Roadmap Section 3 Content |
|-------------|-------------------|--------------------------|
| **Backend** | Modules/ports/package structure, existing DTO/Entity/Service | API interface inventory |
| **Frontend** | Component tree/routing/state management/API call patterns | Component + route inventory |
| **Full-stack** | Explore both frontend and backend | API + component inventory |
| **Plugin** | Skill list/Hooks/Configuration | Skill + hook inventory |

**Show detection results to user for confirmation, then write to roadmap Section 1 (Project Overview).**

### 2.2 L4 Project Exploration

**You cannot know what is possible without knowing what the project already has. L4 is the single source of truth.**

#### Exploration Checklist

| # | Target | Action |
|---|--------|--------|
| 1 | Project structure | List all modules/services, ports, package structure, responsibilities |
| 2 | Existing DTOs/Entities | Glob `**/dto/**`, `**/entity/**`, `**/vo/**` (backend/full-stack) |
| 3 | Existing utilities | Grep `**/utils/**`, `**/common/**` |
| 4 | Existing interfaces | Grep `interface.*Service`, `interface.*Mapper` (backend/full-stack) |
| 5 | Code patterns | Find 3-5 similar features, observe pagination/exception handling/response format |
| 6 | Configuration rules | Where are config files? Nacos? bootstrap? Environment variables? |
| 7 | Dependency inventory | Key dependencies already in pom.xml / build.gradle |

**Output a "Project Landscape Quick Reference"**: module table + reusable key class table + code pattern list + components relevant to the current need.

**Critical**: For existing projects, existing encapsulated methods, interfaces, and implementation patterns must be recorded in roadmap Section 4 (Existing Method Index). Check this section first to avoid reinventing the wheel during subsequent implementation.

### 2.3 Parallel Exploration Mode (recommended for large projects)

When a project has **>5 modules** or a large codebase, split the 7 exploration items into 3-4 parallel **Scout subagents**:

```
Dispatch 3-4 Scout subagents in parallel (Agent tool, read-only):

Scout A -- Project Structure:
  SCOPE: Project root build files + all module directories
  GOAL: List all module/service names, ports, package structure, responsibilities
  RETURN: Module table

Scout B -- DTO/Entity + Utilities:
  SCOPE: **\/dto/**, **\/entity/**, **\/vo/**, **\/utils/**, **\/common/**
  GOAL: Find existing DTO/Entity/VO + utility classes
  RETURN: Reusable class table

Scout C -- Interfaces + Code Patterns:
  SCOPE: **\/service/**, **\/controller/**
  GOAL: Find existing Service/Controller interface signatures + 3-5 code patterns
  RETURN: Interface list + code pattern list

Scout D -- Configuration + Dependencies:
  SCOPE: **\/resources/*.yml|*.yaml|*.properties, pom.xml|build.gradle
  GOAL: List configuration locations, key dependencies
  RETURN: Configuration summary + dependency inventory
```

**Small projects (<=3 modules)**: Main agent does serial exploration.

---

## Step 3: Outline First

**Before writing code, you must have a design outline. Construction without a blueprint guarantees rework.**

### Design Outline Must Include

| Element | Description |
|---------|-------------|
| **What to do** | One sentence + why |
| **Module breakdown** | Which files to create/modify, their responsibilities |
| **Data flow** | Where data comes from -> what processing -> where it goes |
| **Key decisions** | Why this approach (including rejected alternatives) |
| **Files involved** | Estimated list of files to modify/create |
| **Impact scope** | Will changes affect other features? |
| **Verification method** | How to confirm the change is correct? |
| **Test scenario design** | Two layers x 4 categories, >=2 items per category (from Step 1) |
| **Step checklist** | Actionable construction steps with checkboxes |

### Save to 6-Section Roadmap

**All projects use the unified 6-section roadmap structure (`{project-name}-roadmap.md`)**:

```markdown
# {Project Name} Project Roadmap

## 1. Project Overview
Tech stack, architecture decisions, project type

## 2. Phase Planning
Per-phase goals + feature list + status

## 3. Interface Inventory
Backend API / Frontend component routes / Plugin skills+hooks (adaptive by type)

## 4. Existing Method Index
Existing project Service/utility classes/DTOs/middleware etc. (anti-duplication)

## 5. Implementation Records
Per-feature review results, test coverage, edge cases (reverse chronological)

## 6. Experience Extraction
Project pitfalls + cross-project reusable patterns
```

**New project**: Create `knowledge-base/{project-name}/` directory, write the roadmap following this structure.

**New feature on existing project**: Present design outline, get confirmation, then append to the corresponding section of the existing roadmap.

**experience.md handling**: If an existing project has an experience.md, migrate its content to roadmap Section 4 (encapsulated methods) + Section 6 (experience extraction), then delete it. Do not create new experience.md files.

---

## Red Flags -- You Are Skipping the Thinking

| Your Thought | The Truth |
|-------------|-----------|
| "The requirements are already clear" | If truly clear, walking through takes 2 minutes. Taking longer proves it's not clear. |
| "I know this project well, no need to explore" | Projects change. At minimum, verify the signatures of the top 3 key classes. |
| "It's just one line, no need for an outline" | One wrong line costs 2 hours to debug. Spend 30 seconds on a mini outline. |
| "I'll figure out test scenarios by running the code" | Thinking about tests after writing code is too late -- implementation limits thinking. |
| "No need to detect project type" | Type determines exploration strategy and roadmap Section 3 content. 2 minutes of detection saves confusion later. |

---

## Completion Criteria

Before proceeding to writing-plans, confirm:

- [ ] User requirements are clarified (goal, scope, constraints, acceptance criteria)
- [ ] Project type detected and confirmed
- [ ] L4 project landscape understood (modules/existing classes/code patterns/configuration)
- [ ] Design outline written (including two-layer test scenario design)
- [ ] User confirmed the design outline
- [ ] Outline saved to 6-section roadmap (`knowledge-base/{project-name}/{project-name}-roadmap.md`)
- [ ] Project information recorded only in plugin knowledge-base roadmap, not in project files
