# AI Agent Workflow

## 1. Task Reception

- **Orchestrator Agent**: Receives the initial task from the user.

## 2. Analysis and Planning

- **Architect Agent**:
  - Identifies ambiguities and areas needing user clarification.
  - Researches required technologies, frameworks, or APIs.
  - Analyzes the current project status.
  - Defines a high-level approach for the solution.
  - Creates a detailed, step-by-step plan.
  - Refines the plan in tiny steps.
  - Review the plan for any necessary changes.
  - **File Storage**: the plan must be saved to a file in `.kilocode/_generated/plans/` with a unique name (e.g., `<datetime>-<plan-name>.md`) in almost all cases. So, the Coder Agent must receive this file to work on.

## 3. User Interaction

- **Asker Agent**: Manages communication with the user, when asking for clarifications and providing updates is required.

## 4. Implementation

- **Coder Agent**: Receives and implements individual extremely tiny and detailed steps from the plan.

## 5. Code Review

- **Architect/Code Reviewer/Code Simplifier Agent**:
  - Reviews the implemented code for errors or deviations from the plan.
  - Requests necessary changes from the Coder Agent.

## 6. Documentation

- **Documentator Agent**:
  - Adds comments to the code where necessary.
  - Updates the project's documentation (e.g., README, `/docs` files).
  - Suggests and implements automated documentation tools.
