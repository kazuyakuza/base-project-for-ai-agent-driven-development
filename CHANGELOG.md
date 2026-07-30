# Changelog

All notable changes to the AI Agent Driven Development base project will be documented in this file.

## 2026-07-29

### Changes

#### Critical Workflow: Frontend Specialist Integration

- **Conditional front-end sub-steps `4.1a` and `4.5a`**: For front-end related tasks, the Critical Workflow now invokes `frontend-specialist` before `architector`.
  - `4.1a` — Front-end Technical Specification: `frontend-specialist` analyzes framework/version, component structure, contracts, styling architecture, responsive behavior, API integration, accessibility (a11y), and performance budgets; produces a spec saved to `.kilo/plans/<YYYYMMDD>-<plan-name>-frontend-spec.md`.
  - `4.1b` — Implementation Plan: `architector` consumes the front-end spec and produces the complete implementation plan.
  - `4.5a` — Front-end Verification: `frontend-specialist` verifies the implementation against the spec, reporting diffs and quality issues.
  - `4.5b` — Overall Plan Adherence: `architector` incorporates the front-end verification report and checks overall plan adherence.
- **Non-front-end tasks unchanged**: Single `architector` assignment in `4.1` and `4.5` remains the default.
- **Plan Agent pre-analysis**: Global plan now determines per task whether it is front-end related (UI, components, templates, styling, layout, responsiveness, front-end state, UI API consumption, or front-end framework files).

#### Agent Definition: frontend-specialist.md

- **Added `## Context Loading`, `## Process`, and `## Boundaries` sections**: Defines how the agent analyzes front-end tasks, produces technical specifications, and verifies implementations. Agent is bounded to specification and verification only; no application code writing or state-modifying git commands.
- **Added `## Workflow Integration` section**: Cross-references the Critical Workflow conditional steps.
- **Fixed duplicate YAML keys**: Removed duplicate `mode: subagent` and `grep: allow` entries.

#### Review & Simplification Fixes

- **Removed duplicated checklists**: Front-end analysis and verification criteria now live only in `frontend-specialist.md`; `critical-workflow.md` references them instead of repeating.
- **Clarified command wording**: Changed "run allowed build/typecheck/lint/test commands" to "run allowed verification commands" in `frontend-specialist.md`.
- **Explicit context passing**: Added notes that the Plan Agent must pass the front-end spec path and verification report to `architector` in steps `4.1b` and `4.5b`.

#### Documentation

- **Updated `README.md`**: Mermaid diagram now shows front-end conditional decision paths (`Front-end task?` → `4.1a` → `4.1b` and `4.5a` → `4.5b`).

## 2026-06-11 to 2026-07-11

### Changes

#### Agent Renames

- **Architect → Architector**: Renamed the "architect" sub-agent to "architector" across all references — agent files, workflows, rules, and TODO files (2026-07-11).

#### Critical Workflow Updates

- **Plan Agent forced to never call `plan_exit`**: Plan Agent must auto-approve plans and proceed without calling `plan_exit` (2026-07-05, 2026-07-08).
- **Code-simplifier added to step 4.3**: Code Review & Simplification step now runs both code-reviewer and code-simplifier concurrently (2026-07-05).
- **Improved plan generation and auto-approval**: Enhanced implementation plan generation and auto-approval behavior for task plans (2026-06-13, 2026-06-14, 2026-06-29).
- **Updated steps 4.4 and 4.5**: Refined the Documentation and Verification steps in the Critical Workflow (2026-06-28).
- **Enhanced context passing to sub-agents**: Plan Agent now passes more complete context (TODO path, plan path, constraints) when delegating tasks (2026-07-05).
- **Documentation workflow updates**: Refined documentation-related steps in the Critical Workflow (2026-07-09).

#### Permissions Updates

- **Agent permissions overhaul**: Updated, fixed, and re-updated agent permission rules across all agent definition files (2026-07-10).
- **Plan Agent bash access**: Updated Plan Agent permissions to allow necessary bash commands (2026-07-09).
- **Tool Selection Priority Rule updated**: Added `semantic_search` to the tool selection preference hierarchy (2026-07-09).
- **General permissions improvements**: Enhanced tool permission declarations across agent definitions (2026-06-28).

#### Project Info Improvements

- **Fixed project-info instructions**: Corrected details in the project-info instruction files (2026-06-12).
- **More frequent info file updates**: Updated project-info instructions to refresh information files more frequently (2026-07-05).

#### Minor Fixes

- **Fixed TODO file handling**: Corrected logic for how TODO files are processed (2026-06-15).
- **Git setup documentation**: Added command documentation for Git setup (2026-06-18).

## 2026-06-11

### Changes

#### Agent Permission Clarification

- Clarified tool permissions for all agent and mode definition files — each file now explicitly declares allowed tools in both the YAML frontmatter `permission` block and a `## Tools` section in the prompt body.
- Updated files: `.kilo/modes/plan.md`, `.kilo/agents/architect.md`, `.kilo/agents/code-reviewer.md`, `.kilo/agents/code-simplifier.md`, `.kilo/agents/docs-specialist.md`, `.kilo/agents/frontend-specialist.md`, `.kilo/agents/implementer.md`.

## 2026-06-01

### Changes

#### State Tracking Removed

- **Removed `.kilo/state.md`**: State tracking file deleted due to unresolvable permission conflicts in extended projects. Plan Agent no longer maintains process state between sub-steps.
- **Removed `State Tracking` section** from `critical-workflow.md`: Eliminated all state read/write references (6 occurrences).
- **Removed `On start` clause** from Task Origin step (line referencing state fields).
- **Removed `State Sync` line** from Overall Process Management.
- **Removed state update lines** from Task Completion and TODO File Completion steps.
- **Updated `kilo.jsonc`**: Removed `.kilo/state.md` from edit permissions.
- **Updated global `kilo.jsonc`**: Removed `.kilo/state.json` from `external_directory` permissions.

## 2026-05-28

### Changes

#### Custom Subagents: Architect and Implementer

- **Created `architect` subagent** (`.kilo/agents/architect.md`): `mode: subagent`, `hidden: true`, read-only + `.md` edit, no bash, no task delegation. Used for Critical Workflow step 4.1 (task analysis and implementation planning). System prompt instructs agent to read project context files before generating plans.
- **Created `implementer` subagent** (`.kilo/agents/implementer.md`): `mode: subagent`, `hidden: true`, full read/edit/bash/glob/grep access, no task delegation. Used for steps 2, 3, 4.2, 4.3-fix, 4.5, 4.6, 5. System prompt instructs agent to read `.kilo/rules/` for code standards instead of duplicating them inline.
- **Converted custom agents to subagent-only**: Changed `code-reviewer`, `docs-specialist`, `code-simplifier`, `frontend-specialist` from `mode: all` to `mode: subagent` — they no longer appear as selectable primary agents.
- **Updated Critical Workflow Sub-Agent Type Mapping**: Replaced `code` → `implementer` and `plan` → `architect` throughout `.kilo/commands/critical-workflow.md`. Added context-passing instructions requiring Plan Agent to include file paths and task context in task prompts.
- **Updated project-structure command** (`.kilo/commands/project-structure.md`): Replaced `Plan sub-agent`/`Code sub-agent` with `Architect sub-agent`/`Implementer sub-agent` and explicit `subagent_type` values.

#### Rationale

Built-in `code` and `plan` agents are primary-only and cannot be delegated via the `task` tool, causing "Agent is a primary agent and cannot be used as a subagent" errors during Critical Workflow execution. Custom subagents with `mode: subagent` resolve this while keeping project-specific context instructions in their system prompts.

## 2026-05-27

### Changes

#### Critical Workflow Sub-Agent Type Mapping

- **Explicit `subagent_type` values**: Added Sub-Agent Type Mapping table to critical workflow, mapping each step to its correct `subagent_type` parameter value (`code`, `plan`, `code-reviewer`, `docs-specialist`)
- **Replaced generic agent references**: All "Code sub-agent" and "Plan sub-agent" delegation instructions now include explicit `subagent_type` values (e.g., `subagent_type: "code"` instead of untyped delegation)
- **Updated Sub-Task Prompt Requirements**: Added mandatory check that `subagent_type` matches the Sub-Agent Type Mapping table
- **Updated Compliance Self-Check**: Added check (c) verifying correct `subagent_type` usage
- **Updated Global Plan Example**: All example entries now show explicit agent types per step, including new `4.3-fix` entry

#### README

- Fixed command template formatting: `follow /critical-workflow and full read @AGENTS.md` → `full read @AGENTS.md & follow /critical-workflow`

## 2026-05-25

### Changes

#### Critical Workflow Enforcement

- **Force Task Delegation**: All agent assignments in the critical workflow now use explicit `task` tool invocations instead of `@mentions`
  - Added CRITICAL rule: Plan Agent MUST NOT assign the entire global plan or all 4.x steps to a single sub-agent
  - Each sub-step (4.1-4.6) now requires a separate `task` tool invocation
  - Updated all workflow examples and error handling to reference `task` tool
  - Fixed typo in path templates: `<YYYYMMDD}` → `<YYYYMMDD>`

#### Configuration Changes

- **Plan Agent Prompt Externalized**: Moved Plan Agent behavior prompt from `kilo.jsonc` to dedicated `.kilo/modes/plan.md` file
  - Simplified `kilo.jsonc` to only enable plan agent; no inline prompt
  - Added `.kilo/commands/**/*.md` to instruction paths in config

#### New Rules

- **Tool Selection Priority Rule** (`.kilo/rules/tool-selection-priority.md`): Agents must prefer semantic/code-aware tools over raw file commands for code operations
- **Gitignore Compliance Rule** (`.kilo/rules/gitignore-compliance.md`): Agents must verify `.gitignore` before every commit and ensure no ignored files are staged

#### Agent Availability

- **Custom Agents Always Available**: Changed all 4 custom agents (`code-reviewer`, `code-simplifier`, `docs-specialist`, `frontend-specialist`) from `mode: subagent` to `mode: all`
- Normalized bash permission format across agent definitions

#### New File: `.kilocodeignore`

- Added `.kilocodeignore` to control codebase indexing exclusions: lock files, dependency directories, build outputs, binary/media assets, and IDE configs

#### Minor Fixes and Updates

- Updated `.agent/RULES.md`: Added links to the two new rules
- Updated `.agent/project-structure.md`: Added `.kilo/modes/` directory reference
- Updated `.agent/project-info/brief.md`: Added `.kilocodeignore` mention, fixed workflow path reference
- Updated `.kilo/commands/project-structure.md`: Replaced `@general`/`@code` mentions with `Plan sub-agent`/`Code sub-agent` and `task` tool
- Updated `.kilo/commands/project-info-init.md`: Replaced `@plan` with `/critical-workflow` command reference
- Updated `.kilo/rules/important-paths.md`: Fixed typo in plan file path template
- Updated `.kilo/rules/markdown-generation-rule.md`: Replaced `@agent` mentions with explicit agent names

## 2026-05-20

- Fixes mermaid diagram in README file
- Removes workflows files under deprecated workflows folder (already moved to commands)
- Removes this project's plan files

## 2026-05-19

### Changes

#### Major Restructuring

- **Folder Migration**: `.kilocode/` → `.kilo/`
  - Workflows moved from `.kilo/workflows/` to `.kilo/commands/`
  - Updated all references in documentation and workflows
  - Commands now invoked via `/command-name` format

- **Folder Migration**: `.ai-agent/` → `.agent/`
  - Project info files moved from `.kilo/project-info/` to `.agent/project-info/`
  - Updated instructions to reference new paths
  - Created `.initialized` marker file for new projects

- **Memory Bank → Project Info Migration**
  - Replaced Memory Bank with Project Info system
  - Project info files now located in `.agent/project-info/`
  - Updated AGENTS.md to reference Project Info instructions

#### Configuration Updates

- **Kilo.jsonc Rewrite**
  - Removed deprecated fields: `experimentalWorkflow`, `strictWorkflow`, `planStoragePath`, `requireUserApproval`
  - Removed deprecated blocks: `subagents`, `features`
  - Added `agent.plan.prompt` for strict planning behavior
  - Updated instructions path to `.kilo/rules/**/*.md`

#### New Custom Agent Definitions

Created 4 custom agent files in `.kilo/agents/`:

- `code-reviewer.md` - Code quality, security, and plan deviations
- `docs-specialist.md` - Documentation and code comments
- `code-simplifier.md` - Code simplification and refactoring
- `frontend-specialist.md` - Frontend development tasks

#### Workflow Updates

- **Critical Workflow**: Updated to use new command structure
  - Agent mentions: `@code`, `@code-reviewer`, `@docs-specialist`, `@general`
  - Plan directory: `.kilo/plans/` (was `.kilo/_generated/plans/`)
  - Removed deprecated agent references

- **Project Info Initialization**: Updated trigger conditions
  - Checks for `.initialized` marker file instead of text match
  - Simplified workflow description

#### Rule Changes

- **New Rules Added**:
  - `markdown-generation-rule.md` - Defines which agents can create markdown files
  - `military-mode-communication.md` - Concise output with state tracking requirement
  - `max-lines-per-file.md` - Restricts source code files to 200 lines

- **Rules Removed**:
  - `prevent-empty-responses.md` - Superseded by military-mode-communication
  - `git-commit-msg.md` - AI models already know Conventional Commits

- **Rules Updated**:
  - `markdown-generation-rule.md` - Restricting agent permissions
  - `military-mode-communication.md` - Adding state tracking
  - `max-lines-per-file.md` - Scope to src/code only
  - `newline-prevention.md` - Shortened from 51 to 5 lines
  - `important-paths.md` - Updated plan directory path

#### Documentation Updates

- **README.md**: Updated all references from `.kilocode/` and `.ai-agent/` to new paths
- **AGENTS.md**: Restructured with links to project info sections
- **brief.md**: Removed HTML comment marker block
- **WORKFLOWS.md**: Updated workflow command paths

#### Cleanup

- Removed `.kilo/_generated/` directory (was `.kilo/_generated/plans/`)
- Added `.kilo/plans/.gitkeep` for plan directory
- Updated `.gitignore` with agent-manager.json

#### Security Improvements

- Added security note to `how-to-set-up-git.md` recommending SSH keys or GitHub CLI
- Updated `.gitignore` to ignore agent-manager.json

### Technical Details

- All workflow files migrated to command format with YAML frontmatter
- Commands now support agent assignment and permissions
- State tracking enhanced with `.kilo/state.json` updates
- Plan file locations unified across all workflows

### Breaking Changes

- Workflows now use `/command-name` format instead of file references
- Plan storage path changed from `.kilo/_generated/plans/` to `.kilo/plans/`
- Agent mentions changed from descriptive text to `@agent-name` format
