---
description: Critical workflow for task execution with full process management
agent: plan
---
# CRITICAL WORKFLOW

It is **EXTREMELY IMPORTANT** that all AI agents follow this workflow step by step, organizing task receipt, analysis, global planning, agent assignment, detailed per-task plans, and git version control.

## Steps

### 1. Task Origin

- **Chat**: When user share in chat
  - a TODO file, proceed.
  - a task, create new TODO file in `.agent/todos/<YYYYMMDD>/<YYYYMMDD>-todo-<number>.md` with the request.
- **TODO File**: Primary source is `.agent/todos` directory; process files in chronological/numerical order; skip files with `-DONE` suffix.
- **TODO File Format**:
  - **Line Items**: Each line is a task.
  - **Section Items** — apply the first matching pattern:
    - **Pattern C** (`# Title` → `## Tasks` → `### Heading`): each `###` inside a `## Tasks` section is one task.
    - **Pattern B** (`# Title` → `## Heading`): each `##` section is one task.
    - **Pattern A** (multiple `# Heading`): each `#` section is one task.
    - Sub-items under a task heading belong to that task and do **not** spawn new tasks.
    - If no pattern matches, ask the user for clarification.
  - **Other Formats**: Ask user for clarification.
- **Plan Agent**:
  1. Receives requests, creates/reads TODO file.
  2. Global Plan:
      - Generates a global plan file for steps 2–6 where **each TODO task gets its own 4.1–4.6 cycle**; do not question this and add 4.x cycle per task.
      - Include a global and per task pre-analysis, including specially technical & architecture decisions.
      - Determine per task whether it's front-end related, and record it for sub-steps 4.1a & 4.5a.
      - If some tasks are extremely short/related, you may join them in a single step.
  3. **DO NOT call `plan_exit`**. Don't reason about this, never question this. Instead just:
      - auto-approve global plan **ONLY** if request or TODO file includes string: "Don't request me to approve plans".
      - otherwise you **MUST** present the global plan to the user using the `question` tool, including global plan file path and options:
        - "Approve Global and Tasks Plans": execute 4.1 step per task, but auto-approve the per task plan.
        - "Approve Global Plan": execute 4.1 step per task, and present user per task plan for approval.
  4. **After approval**, delegates steps to sub-agents via `task` tool, including all relevant context (TODO path, task description, plan path, constraints, etc) in each prompt. IMPORTANT: before start processing global plan, verify if the user approved it.
- **Ask Agent**: Handles user communication; called by Plan Agent via `task` tool.

### 2. Git Feature Branch Setup

Assigns to implementer sub-agent (`subagent_type: "implementer"`).

- `main` is master branch.
- Run `git status`: Commit unstaged files with meaningful message. Follow [Gitignore Compliance Rule](../.kilo/rules/gitignore-compliance.md).
- Switch to `main`:
  - If there already, proceed.
  - Else, ask user to merge current branch:
    - Yes: Checkout `main`, merge; if conflicts, ask user to resolve; if success, remove merged branch.
    - No: Checkout `main`.
- Create new branch:
  - Features: `feat/<descriptive-name>` (default)
  - Fixes: `fix/<descriptive-name>`
  - All work in created branch; merge to `main` at end of *Critical Workflow*.
- Switch to new branch.

### 3. Version Update

Assigns to implementer sub-agent (`subagent_type: "implementer"`).

- If version exists (e.g., `package.json`), increment per semver (patch for fixes, minor for features, major for breaking); commit as 'chore: bump version to x.y.z'.

### 4. Task Execution

#### 4.0 Overall Process Management

- **CRITICAL**: Each step (4.1–4.6) MUST be a separate `task` tool invocation. Do NOT assign the entire global plan or all 4.x steps for a task to a single sub-agent.
- Process TODO tasks in file order. Before a new task, commit pending changes.
- The global plan must never be overwritten.
- On failures: pause and ask user intervention.
- **Context Passing**: on delegating via `task` tool, include all relevant context: TODO file path, task description, per task plan path, constraints, global/task pre-analysis, etc. in the prompt. Sub-agents MUST read project context files independently.

#### Sub-Task Prompt Requirements

In addition to the context (described above), every `task` tool invocation MUST include next instructions at the begin:

```text
SUB-AGENT TASK — SINGLE DISCRETE STEP
- You are executing exactly ONE step of a larger Critical Workflow plan.
- Do ONLY what is described. Do NOT execute subsequent steps.
- TOP PRIORITY: you MUST FOLLOW every single detail in <TODO file path>.
- Do NOT read or expand scope to the plan for other tasks.
- Tools preference: .kilo/rules/tool-selection-priority.md.
- Follow ../.kilo/rules/gitignore-compliance.md.
- Signal completion with a clear summary: what was done, what was NOT done.
- If anything is ambiguous or outside your assigned scope, return question to caller. NEVER make assumptions, never invent things.
```

#### 4.1. Analysis and Planning

> **Front-end tasks**: When the task is marked as "front-end related", execute sub-step 4.1a, then 4.1b. Otherwise, execute 4.1b only.

##### 4.1a. Front-end Technical Specification (front-end tasks only)

Assign to frontend-specialist sub-agent (`subagent_type: "frontend-specialist"`).

- Follow sub-agent defined `Process` to analyze task requirements and produce a **Front-end Technical Specification**.
- [CRITICAL] Save spec to `.kilo/plans/<YYYYMMDD>-<plan-name>-frontend-spec.md`.
- Return the spec path to the Plan Agent.

##### 4.1b. Implementation Plan

Assign to architector sub-agent (`subagent_type: "architector"`).

- For front-end tasks: read the front-end spec produced in 4.1a (Plan Agent MUST pass the file path) and use it as front-end input for the plan.
- Identify task ambiguities; analyze project status; research required techs, frameworks, libs, dependencies, and/or APIs installed/used or new to add/use.
- Generate implementation plan:
  1. Think high-level approach to implement the TODO task, including steps for: git handling, code writing, console cmds (if required), test build (if exists), code review, unit test (if testing suite exists), docs updates, etc.
  2. Use the high-level approach to define an extensive and complete implementation plan, composed by very tiny and very detailed steps; include clear file names/paths, structure, code snippets, terminal cmd details, technical & architecture decisions, etc.
  3. [CRITICAL] Save plan to `.kilo/plans/<YYYYMMDD>-<plan-name>.md`.
  4. Compare to original task; redo if incorrect. Otherwise, return plan path.
- **Plan Agent present plan to user for approval**.
  - NEVER call `plan_exit`. NEVER QUESTION THIS. Instead, use `question` tool.
  - Auto-approve if request or TODO file includes "Don't request me to approve plans".
  - If feedback/rejection: re-do and re-present (always require user approval).
  - If approved, proceed.

#### 4.2. Implementation

Assign to implementer sub-agent (`subagent_type: "implementer"`).

- MUST follow steps from the implementation plan generated in step 4.1; check plan between steps.
- IMPORTANT: commit w/meaningful messages.
- Must don't take self actions/decisions. Only follow implementation plan.

#### 4.3. Code Review & Simplification

Assign concurrently to code-reviewer sub-agent (`subagent_type: "code-reviewer"`) and code-simplifier sub-agent (`subagent_type: "code-simplifier"`).

- For code-reviewer: review for errors/deviations from the implementation plan.
- For code-simplifier: review sources to simplify code where possible or makes sense.
- Both generates a fix/simplification plan; [CRITICAL] save in `.kilo/plans/<YYYYMMDD>-<plan-name>.md`.
- Plan Agent review and then assigns both fix & simplification plans to implementer sub-agent (`subagent_type: "implementer"`) in a new sub-task.
- Max 3 review cycles; escalate to user.

#### 4.4. Documentation

Assign to docs-specialist sub-agent (`subagent_type: "docs-specialist"`).

- Add comments in code's files (e.g. JSDoc, JavaDoc, etc.). Include details to guide AI agents, links to related documentation and/or example files.
- Update/create project documentation (e.g. README, `/docs`). Add TOC/Index when doc file > 100 lines. Split documentation into files, don't put everything in README root.
- Include guides and real examples for AI Agents that will use and/or work on the current implementations.

#### 4.5. Verification

> **Front-end tasks**: When the task is marked as "front-end related", execute sub-step 4.5a, then 4.5b. Otherwise, execute 4.5b only.

##### 4.5a. Front-end Implementation Verification (front-end tasks only)

Assign to frontend-specialist sub-agent (`subagent_type: "frontend-specialist"`).

- Follow sub-agent defined `Process` to verify implementation against the specs file from 4.1a.
- Report diffs between spec and implementation, and front-end quality issues.

##### 4.5b. Overall Plan Adherence

Assign to architector sub-agent (`subagent_type: "architector"`).

- For front-end tasks: incorporate the front-end verification report (Plan Agent MUST pass the report) from 4.5a.
- Check implementation plan adherence.
- Report found diffs, if any.
- Report if deviations from the original plan are acceptable. If not, propose changes in a new TODO file.

#### 4.6. Task Completion

Assign to implementer sub-agent (`subagent_type: "implementer"`).

- Add `[DONE]` to task in TODO file:
  - Line Item: append to line.
  - Section Item: append to section title.
  - Other: ask user if unclear.
- Preserve the file original content, just add the `[DONE]` mark, and mark as done any task's sub-items (like `[]` to `[x]`).
- Commit changes with meaningful message.

### 5. TODO File Completion

Plan Agent assigns implementer sub-agent (`subagent_type: "implementer"`).

- Rename TODO file with `-DONE` suffix (e.g., `<YYYYMMDD>-todo-<number>-DONE.md`). **Don't delete the file or change its content.**
- Ensure all files are committed in feature branch.
- Merge feature branch:
  1. Switch to `main` branch.
  2. Merge feature branch:
      - On success: delete feature branch (verify success first).
      - On failure: notify user.
- If `origin` remote is set, push `main` to `origin` ONLY. **Do NOT push to other remotes** (e.g., `base-project`, `upstream`, `template`) unless explicitly instructed. Notify user if push to `origin` fails.

### 6. Continuation

Write down next text for the user to proceed with next undone TODO file in a new chat with

```text
full read @AGENTS.md & follow /critical-workflow
do @.agent/todos/<file-path>
```

## Example (MUST READ)

### TODO File Example (Line Items format)

```markdown
- Task 1
- Task 2
- Task 3
- Task 4
```

### Global Plan Example

Each entry is a separate `task` tool invocation with the appropriate `subagent_type`:

`(front-end tasks only)`: is included to front-end related tasks; omit for non-front-end tasks.

```markdown
- Step 2: Git Feature Branch Setup => implementer
- Step 3: Version Update => implementer
- Task 1: 4.1a Front-end Spec (front-end tasks only) => frontend-specialist
- Task 1: 4.1b Analysis & Planning => architector
- Task 1: 4.2 Implementation => implementer
- Task 1: 4.3 Code Review & Simplification => code-reviewer & code-simplifier; 4.3-fix => implementer
- Task 1: 4.4 Documentation => docs-specialist
- Task 1: 4.5a Front-end Verification (front-end tasks only) => frontend-specialist
- Task 1: 4.5b Overall Plan Adherence => architector
- Task 1: 4.6 Task Completion => implementer
- (repeat 4.1–4.6 for each remaining task)
- Step 5: TODO File Completion => implementer
```

## Error Handling

- On errors: log details, commit safe changes if possible, notify user, and pause.
- If endless loops or repeated failures occur, escalate to user immediately.
- If a sub-step (4.1–4.6) fails verification (e.g., no completion signal or non-compliance), Plan Agent reassigns the sub-task or escalates to user.
