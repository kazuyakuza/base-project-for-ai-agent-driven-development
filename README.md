# Base Project for AI Agent Driven Development

This project serves as a foundational template for future AI-agent driven development. It is pre-configured with essential rules, workflows, and structures optimized for collaboration between human developers and AI agents (specifically Kilo Code).

**Attention AI Agents:** Before making any changes, you **must** read and adhere to the guidelines outlined in [`AGENTS.md`](AGENTS.md). This file contains critical information about the project's workflow, rules, and architectural standards.

## Compatibility

This template was implemented and tested with the **Kilo Code VSCode plugin**. It should also work with:

- **Kilo Code CLI** (command-line interface)
- Any AI agent manager or similar tool that supports custom sub-agent definitions, rule files, and workflow commands via markdown-based configuration

The project uses standard Markdown-based configuration (`.kilo/`, `.agent/`) and does not depend on any proprietary format, making it adaptable to other AI-driven development tools.

## Prerequisites

- **Kilo Code**: Optimized for the Kilo Code plugin for VSCode, with CLI support. See [compatibility section](#compatibility) for details.
- **Git**: Ensure your environment is configured for the workflow. See [`how-to-set-up-git.md`](docs/how-to-set-up-git.md).

## About this Project

The primary goal of this repository is to provide a clean, structured starting point for new projects with built-in "AI-Readiness."

### Design Principles

- **Foundation**: A structured baseline for new repositories.
- **AI-Readiness**: Integrated configurations (like `.kilo`, `.agent`, and `.kilocodeignore`) to enable immediate and effective AI agent participation.
- **Standardization**: Established coding standards, workflows, and documentation practices.
- **Project Info**: A persistent context and knowledge management system for agents.

## Project Structure

Understanding the purpose of the configuration directories is key to effective development:

- [`.agent/`](.agent/): Stores project-specific agent context. Includes [`.agent/project-info/`](.agent/project-info/) for persistent project knowledge (`brief.md`, `product.md`, `context.md`, `architecture.md`, `tech.md`), the [`.agent/todos/`](.agent/todos/) directory for task tracking, local rules, and the [`project-structure.md`](.agent/project-structure.md) map.
- [`.kilo/`](.kilo/): The operational core of the AI integration. Contains custom [`.kilo/agents/`](.kilo/agents/) (Architector, Implementer, Code Reviewer, Docs Specialist, etc.), global [`.kilo/rules/`](.kilo/rules/) (19 rule files), standardized [`.kilo/commands/`](.kilo/commands/) (workflows like the Critical Workflow), [`.kilo/modes/`](.kilo/modes/) for agent mode overrides, and the [`.kilo/plans/`](.kilo/plans/) directory where agents store detailed implementation plans.
- [`.kilocodeignore`](.kilocodeignore): Controls which files are excluded from codebase indexing, skipping lock files, dependency directories, build outputs, and binary assets.

## The Critical Workflow

The project follows a standardized process for task execution, ensuring systematic progress from analysis to deployment. Each step is handled by a dedicated sub-agent:

```mermaid
graph TD
    Start((Start)) --> Origin{1. Task Origin}
    Origin -- Chat --> CreateTodo[Create TODO file]
    Origin -- TODO File --> GitSetup["2. Git Feature Branch Setup<br/><small>[Implementer]</small>"]
    CreateTodo --> GitSetup
    GitSetup --> VersionUpdate["3. Version Update<br/><small>[Implementer]</small>"]
    VersionUpdate --> Execution[Task Execution Loop]
    subgraph ExecutionProcess [4. Task Execution]
        Execution --> FECheck1{Front-end task?}
        FECheck1 -- Yes --> FESpec["4.1a Front-end Spec<br/><small>[Frontend Specialist]</small>"]
        FECheck1 -- No --> ImplPlan
        FESpec --> ImplPlan["4.1b Implementation Plan<br/><small>[Architector]</small>"]
        ImplPlan --> Implementation["4.2 Implementation<br/><small>[Implementer]</small>"]
        Implementation --> CodeReview["4.3 Code Review<br/><small>[Code Reviewer & Simplifier]</small>"]
        CodeReview -- Fixes Needed --> Fixes["4.3-fix Apply Fixes<br/><small>[Implementer]</small>"]
        Fixes -- Re-review --> CodeReview
        CodeReview -- Approved --> Documentation["4.4 Documentation<br/><small>[Docs Specialist]</small>"]
        Documentation --> FECheck2{Front-end task?}
        FECheck2 -- Yes --> FEVerify["4.5a Front-end Verify<br/><small>[Frontend Specialist]</small>"]
        FECheck2 -- No --> OverallCheck
        FEVerify --> OverallCheck["4.5b Overall Adherence<br/><small>[Architector]</small>"]
        OverallCheck --> TaskCompletion["4.6 Task Completion<br/><small>[Implementer]</small>"]
    end
    TaskCompletion -- More Items --> Execution
    TaskCompletion -- All Items Done --> TodoCompletion["5. TODO File Completion<br/><small>[Implementer]</small>"]
```

For full details, see [`critical-workflow.md`](.kilo/commands/critical-workflow.md).

## How to Start a Task

To initiate work with an AI agent, use one of the following copy-paste friendly commands in the chat.

> **Note on Project Info:** When cloning this template for a new project, the Project Info initialization workflow will trigger automatically. The file `.agent/project-info/brief.md` defines the project's core requirements and scope — AI agents rely on this for context across sessions. To initialize, run `/critical-workflow` and ask to "initialize project info". See [`.kilo/commands/project-info-init.md`](.kilo/commands/project-info-init.md) for details. If the project brief is not defined, agents may produce work that does not align with your goals.

### Option 1: Using a TODO File (Recommended)

1. Create a new file named `YYYYMMDD-todo-X.md` inside a date-specific subdirectory under `.agent/todos/` (e.g., `.agent/todos/20260602/20260602-todo-1.md`).
2. Populate it using one of the [recommended TODO file formats](docs/how-to-write-todo-files.md).
3. Paste the following into the chat:

```text
full read @AGENTS.md & follow /critical-workflow
do @/.agent/todos/<YYYYMMDD>/<YYYYMMDD>-todo-<number>.md
```

### Option 2: Direct Chat Request

If you have a quick request, use this template:

```text
full read @AGENTS.md & follow /critical-workflow
do [Your specific task or request here]
```

## AI Agent Plans

The critical workflow requires the AI to generate detailed implementation plans for each task. The [Architector sub-agent](.kilo/agents/architector.md) handles analysis and planning (step 4.1b), while the [Implementer sub-agent](.kilo/agents/implementer.md) executes the plan (step 4.2). A [Code Reviewer](.kilo/agents/code-reviewer.md) and [Code Simplifier](.kilo/agents/code-simplifier.md) validate quality, a [Docs Specialist](.kilo/agents/docs-specialist.md) maintains documentation, and a [Frontend Specialist](.kilo/agents/frontend-specialist.md) produces front-end specs (4.1a) and verifies front-end implementations (4.5a) for front-end related tasks.

The AI agent will ask for your approval before proceeding with plans. To skip approval prompts, include in the TODO file or chat request:

```text
"Don't request me to approve plans"
```

---

*Note: This workflow is actively maintained and updated to improve stability and introduce new features.*
