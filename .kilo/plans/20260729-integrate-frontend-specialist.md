# Integrate frontend-specialist into Critical Workflow

## Objective

Update the Critical Workflow so that `frontend-specialist` contributes front-end technical analysis/specification (4.1) and front-end verification (4.5) for front-end tasks, while `architector` remains the primary planner and overall verifier. The integration is **conditional**: front-end sub-steps run only when the Plan Agent's pre-analysis marks a TODO task as front-end related; non-front-end tasks keep the original single-architector behavior.

## Analysis of Current State

### `.kilo/agents/frontend-specialist.md` (57 lines)

- Frontmatter defines `description`, `mode: subagent`, `permission` (read, edit restricted to `*.md`, limited read-only bash, denied `task`), `hidden`.
- Body has `## Tools Preference` and `## Role`. There is **no `## Process` section** and **no `## Context Loading` section**.
- Known YAML defects (out of explicit task scope, listed as optional cleanups):
  - Duplicate `mode: subagent` key (lines 3 and 4).
  - Duplicate `grep` key inside `permission` (lines ~10 and ~44).
- Permissions already allow editing `*.md`, so producing a front-end spec file (markdown) is permitted. No permission changes required.

### `.kilo/commands/critical-workflow.md` (200 lines)

- Step `4.1. Analysis and Planning` (lines 85–99) assigns a single `architector` to analyze + plan.
- Step `4.5. Verification` (lines 127–133) assigns a single `architector` to verify adherence.
- Step 1, Plan Agent item 2 (line 28) describes the global plan generation but does **not** mention identifying front-end tasks or conditional sub-steps.
- Global Plan Example (lines 179–194) lists 4.1/4.5 as architector-only entries with no front-end conditional sub-steps.

### `.kilo/agents/architector.md` (reference, 88 lines)

- Defines `## Context Loading` and `## Process` with numbered steps, plus `## Boundaries`. `frontend-specialist.md` will mirror this structure for consistency.

## Design Decisions

1. **Conditional front-end detection**: The Plan Agent, during the per-task pre-analysis in step 1, determines whether a TODO task is front-end related. Criteria: the task touches UI, components, templates, styling, layout, responsive behavior, front-end state, API consumption for the UI, or front-end framework files (Angular/Vue/TypeScript/CSS). The global plan records the decision per task so the 4.x cycle knows whether 4.1a/4.5a apply.
2. **Sub-step numbering**: `4.1a` (frontend-specialist spec) then `4.1b` (architector full plan); `4.5a` (frontend-specialist verification) then `4.5b` (architector overall adherence). For non-front-end tasks, only `4.1b`/`4.5b` run (equivalent to original behavior).
3. **Spec artifact path**: `.kilo/plans/<YYYYMMDD>-<plan-name>-frontend-spec.md`. The architector consumes it as front-end input.
4. **No permission changes**: existing `edit: "*.md": allow` covers spec creation.
5. **No agent additions/removals**: only `frontend-specialist.md` content and `critical-workflow.md` flow change.

## Files to modify

1. `.kilo/agents/frontend-specialist.md` — add `## Context Loading` and `## Process` sections (and `## Boundaries`).
2. `.kilo/commands/critical-workflow.md` — add conditional front-end sub-steps to 4.1 and 4.5; update step 1 global-plan clause; update Global Plan Example.

Out of scope: other agents, workflows, `kilo.jsonc`, permissions, and anything beyond the two files above. Optional cleanups (duplicate YAML keys in `frontend-specialist.md`) are listed separately and are NOT required.

---

## Detailed Implementation Steps

### Step 0. Git

- Before editing: confirm on the feature branch created in step 2 of the Critical Workflow.
- After edits: stage only the two changed files; commit with message:
  `feat(workflow): integrate frontend-specialist into critical workflow analysis and verification`

### Step 1. Edit `.kilo/agents/frontend-specialist.md`

Append the following sections **after the existing `## Role` section** (after current line 57). Do NOT modify the frontmatter or existing body sections.

Insert (with actual newlines):

```markdown

## Context Loading

Before analyzing a front-end task or verifying an implementation, read these project files for context:

- `AGENTS.md`
- `.agent/project-info/*` (all files)
- `.agent/project-structure.md`
- `.agent/WORKFLOWS.md`
- `.kilo/rules/important-paths.md` — defines plan/spec file naming convention

Also read any files referenced in the task prompt from the caller, including the front-end technical spec produced in 4.1a (for verification) or the implementation plan when relevant.

## Process

### 1. Intake

Read the front-end task from the TODO file or description provided in the task prompt, and all context files listed in Context Loading.

### 2. Front-end Analysis (for 4.1a)

Analyze the front-end requirements of the task and document:

- Target framework(s) and version (Angular, VueJS, etc.) and TypeScript configuration.
- Component structure: boundaries, hierarchy, and reuse.
- Contracts: inputs (props), internal state, outputs (events/emitters), and service injections.
- Routing and navigation changes, if any.
- Styling architecture: CSS approach (vanilla or libs/frameworks), design tokens/theming, and naming conventions.
- Responsive behavior and breakpoints.
- API integration: endpoints, request/response shapes, error handling, and loading states.
- Accessibility (a11y): semantic markup, ARIA, keyboard navigation, color contrast.
- Performance budgets: bundle size, lazy loading, change-detection strategy.

### 3. Produce Front-end Technical Specification (for 4.1a)

Produce a Front-end Technical Specification capturing the analysis above with:

- Concrete component boundaries and contracts (props/states/events).
- Design tokens and styling decisions.
- API integration contract.
- Acceptance criteria for UI (a11y, responsive, performance).

Save the spec to `.kilo/plans/<YYYYMMDD>-<plan-name>-frontend-spec.md` and return its path to the caller.

### 4. Front-end Verification (for 4.5a)

Verify the implemented front-end against the Front-end Technical Specification from 4.1a:

- Confirm component structure and contracts match the spec.
- Confirm CSS/styling architecture and design tokens applied correctly.
- Confirm responsive behavior and layout correctness.
- Confirm accessibility requirements are met.
- Confirm state management and API integration behave as specified.
- Run allowed build/typecheck/lint/test commands (npm/npx) to confirm front-end code is valid.

Report diffs between the spec and the implementation, plus front-end quality issues, so architector can incorporate them in the overall verification (4.5b).

## Boundaries

- Specification and verification only. Do NOT write application code files.
- Do NOT run state-modifying git commands; only read-only git commands from the allowed list are permitted.
- Return the spec path or the verification report. Do NOT proceed to implementation or plan approval.
```

### Step 2. Edit `.kilo/commands/critical-workflow.md` — step 1 global-plan clause

Replace the text of Plan Agent item 2 (line 28) with a version that records front-end relevance and the conditional sub-steps.

Old (exact, line 28):

```
  2. Generates a global plan file for steps 2–6 where **each TODO task gets its own 4.1–4.6 cycle**; do not question this and add 4.x cycle per task. Include a global and per task pre-analysis, including specially technical & architecture decisions.
```

New:

```
  2. Generates a global plan file for steps 2–6 where **each TODO task gets its own 4.1–4.6 cycle**; do not question this and add 4.x cycle per task. Include a global and per task pre-analysis, including specially technical & architecture decisions. **Determine per task whether it is front-end related** (touches UI, components, templates, styling, layout, responsiveness, front-end state, UI API consumption, or front-end framework files); record this per task so front-end sub-steps **4.1a** and **4.5a** are included only for front-end tasks.
```

### Step 3. Edit `.kilo/commands/critical-workflow.md` — replace step 4.1 block

Replace the entire current 4.1 block (lines 85–99, from `#### 4.1. Analysis and Planning` through the end of the approval bullet) with the new block that keeps architector as the planner.formatira

New 4.1 block (replace lines 85–99):

```markdown
#### 4.1. Analysis and Planning

> **Front-end tasks**: When the per-task pre-analysis (step 1) marks the TODO task as front-end related, execute sub-step **4.1a** (front-end spec) then **4.1b** (full plan). For all other tasks, execute **4.1b** only (single architector assignment).

##### 4.1a. Front-end Technical Specification (front-end tasks only)

Assign to frontend-specialist sub-agent (`subagent_type: "frontend-specialist"`).

- Analyze front-end requirements: framework(s)/version, component structure, contracts (props/states/events), routing, styling architecture, design tokens, responsive behavior, API integration, accessibility (a11y), and performance budgets.
- Produce a **Front-end Technical Specification** with concrete component boundaries, contracts, design tokens, API contract, and UI acceptance criteria.
- [CRITICAL] Save spec to `.kilo/plans/<YYYYMMDD>-<plan-name>-frontend-spec.md`.
- Return the spec path to the Plan Agent.

##### 4.1b. Implementation Plan

Assign to architector sub-agent (`subagent_type: "architector"`).

- For front-end tasks: read the front-end spec produced in 4.1a and use it as front-end input for the plan.
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
```

### Step 4. Edit `.kilo/commands/critical-workflow.md` — replace step 4.5 block

Replace the entire current 4.5 block (lines 127–133, from `#### 4.5. Verification` through the last bullet) with the new block that keeps architector as the overall verifier.

New 4.5 block (replace lines 127–133):

```markdown
#### 4.5. Verification

> **Front-end tasks**: When the per-task pre-analysis (step 1) marks the TODO task as front-end related, execute sub-step **4.5a** (front-end verification) then **4.5b** (overall adherence). For all other tasks, execute **4.5b** only (single architector assignment).

##### 4.5a. Front-end Implementation Verification (front-end tasks only)

Assign to frontend-specialist sub-agent (`subagent_type: "frontend-specialist"`).

- Verify front-end implementation against the Front-end Technical Specification from 4.1a:
  - Component structure and contracts (props/states/events) match the spec.
  - CSS/styling architecture and design tokens applied correctly.
  - Responsive behavior and layout correctness.
  - Accessibility (a11y) requirements met.
  - State management and API integration behave as specified.
  - Build/typecheck/lint pass for front-end code (run allowed npm/npx read-only commands).
- Report diffs between spec and implementation, and front-end quality issues.

##### 4.5b. Overall Plan Adherence

Assign to architector sub-agent (`subagent_type: "architector"`).

- For front-end tasks: incorporate the front-end verification report from 4.5a.
- Check implementation plan adherence.
- Report found diffs, if any.
- Report if deviations from the original plan are acceptable. If not, propose changes in a new TODO file.
```

### Step 5. Edit `.kilo/commands/critical-workflow.md` — update Global Plan Example

Replace the intro sentence and the fenced plan block of the Global Plan Example (the block at lines ~181–194) so it reflects the conditional front-end sub-steps.

Replace the existing fenced block content (the lines starting with `- Step 2:` ... through `- Step 5: TODO File Completion => implementer`) with:

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

Also insert a clarifying note right after the example intro line ("Each entry is a separate `task` tool invocation with the appropriate `subagent_type`:"):

```markdown
The lines marked "(front-end tasks only)" are included solely for front-end related tasks; omit them for non-front-end tasks.
```

### Step 6. Verify changes (read-only)

- Read both modified files end-to-end to confirm no duplicated/merged sections and YAML frontmatter unchanged.
- Confirm `4.1a`, `4.1b`, `4.5a`, `4.5b` references are consistent across step 1 clause, 4.1, 4.5, and the Global Plan Example.
- Confirm no application source files were changed (only the two `.md` files).

### Step 7. Commit

Commit the two changed files with message:
`feat(workflow): integrate frontend-specialist into critical workflow analysis and verification`

---

## Optional Cleanups (NOT required for this task)

These are pre-existing YAML defects in `.kilo/agents/frontend-specialist.md`. They are out of the explicit task scope. If the user approves, the implementer may also:

- Remove the duplicate `mode: subagent` key (keep one).
- Remove the duplicate `grep: allow` in `permission` (keep one).

These are listed for awareness only and may be deferred to a separate TODO.

## Verification Against Original TODO

TODO: `20260729-todo-01.md`, section `### Update frontend-specialist.md and critical-workflow.md`, sub-items:

- [ ] Add `Process` section to `frontend-specialist.md` defining front-end analysis/spec generation and front-end verification steps → covered by Step 1 (adds `## Process`, plus `## Context Loading` and `## Boundaries`).
- [ ] Update `critical-workflow.md` to include conditional front-end sub-steps in 4.1 and 4.5 → covered by Steps 2–4.
- [ ] Update global plan example in `critical-workflow.md` → covered by Step 5.

The plan matches all three sub-items. No scope creep beyond the two files.

## Risk / Edge Cases

- Conditional wording must be unambiguous so non-front-end tasks behave exactly as before (single architector in 4.1/4.5). Plan uses explicit "execute X only" guardrails.
- Spec artifact path must follow `important-paths.md` plan naming convention (extended with `-frontend-spec` suffix). Verify during 4.5.
- `frontend-specialist` `task: deny` is fine: the Plan Agent delegates; the specialist never delegates.