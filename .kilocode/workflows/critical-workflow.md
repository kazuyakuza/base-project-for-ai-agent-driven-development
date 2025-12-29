# CRITICAL WORKFLOW

## Initial Note

It is EXTREMELY IMPORTANT that all ai agents follow this workflow's steps in detail.
The workflow steps organize the task/work receiving, the understanding and analysis, generation of a global plan to work on them, handle correct agent for the exact work and steps, generation of more detailed plans for implementations to work on, and some other things, all that along with a correct git version handle.

## 1. Task Origin

- **Chat**: If a task (ie. almost any work to do) is asked/given in the chat (except the user is indicating an existing TODO file), create a new TODO file in `.ai-agent/todos/<YYYYMMDD>/<YYYYMMDD>-todo-<number>.md`. The content of the file should be the user's request.
- **TODO File**:
  - The primary source of tasks is the `.ai-agent/todos` directory, which contains TODO files that need to be processed, organized by date and numbered sequentially.
  - The user may indicate the TODO file to work on, or just ask to look for the next one undone.
  - Process TODO files in chronological and numerical order from the `.ai-agent/todos` directory.
  - Files ending in `-DONE` are considered finished and should be skipped.
- **TODO File Format**
  - **Line Items**: Each line is a separate task.
  - **Section Items**: Each markdown section is a separate task, potentially with additional details.
  - **Other Formats**: If the format is unclear, ask the user for clarification.
- **Orchestrator Agent**:
  - Receives the initial task from the user, and resolves the above points: TODO file creation if not exists, or find & read the next TODO file to work on.
  - CRITICAL: Generates an overall plan of action to handle the task, following the steps below in this file.
  - The plan of action must be a list of clear steps.
  - Orchestrates the workflow by assigning sub-tasks to the appropriate agents, to handle step by step.
  - The sub-tasks must have a clear description of the expected outcome and the steps to achieve it. It must be specially clear to the agent if it should implement or not code, modify/create files or not, signal completion with a clear response, generate a plan on how to implement something, etc.
  - Its IMPORTANT to prevent that an agent in a sub-task don't follow the work that must handle. For example, prevent that the architect agent type switch to code mode when the sub task is asking for a plan, bot not implementation.
  - Important: the Orchestrator drives the overall process. The analysis and implementation details should be handled by the appropriate agents.
- **Asker Agent**: Manages communication with the user, when asking for clarifications and providing updates is required.

## 2. Git Feature Branch Setup

Orchestrator Agent must include in the plan it generates this section.
It must clear for the designated ai agent to where and how to run the commands of this section.
Must include next steps:

- 1º Run `git status`:
  - If TODO file/s and/or plan file/s from the **1. Task Origin** step are unstaged, stash them, in order to merge them later in the new branch to create in the step 3º below, from this same section. CAUTION: don't remove the files, they have the work definition!
- 2º Switch to the `main` branch:
  - Its important to understand that `main` branch is the master branch of the repo.
  - If already in the main branch, and there are uncommitted changes, ask the user what to do with them.
  - If not in the main branch, and:
    - there are uncommitted changes, commit them. Then ask the user if merge that branch to `main` branch or not.
    - there are not pending changes, then ask the user if merge that branch to `main` branch or not.
- 3º Create and switch to a new branch with a descriptive name:
  - For new features: `feat/<meaning-name>`
  - For bug fixes: `fix/<meaning-name>`
  - Create the new branch before starting work on the task, ensuring the branch name reflects the task's purpose or TODO file's name.
  - All work must be done in the feature branch. The feature branch will be merged to the `main` branch later.
- 4º Add the TODO file/s and/or plan file/s staged in step 1º of this section, if any.

## 3. Version Update

- If the project has a version number (e.g., in `package.json`), increment it following the `x.y.z` format.
- Commit this change.

## 4. Task Execution

### 4.0 Overall Process Management

- This step must be handled by the Orchestrator Agent.
- IMPORTANT: this step indicates that the Orchestrator Agent must drive the overall process. The analysis and implementation details should be handled by the appropriate agents. This is detailed in the below steps.
- Process items within the TODO file in the order they are defined.
- Before starting a new item, commit any pending changes to the current branch with a meaningful message.
- Ask the user for clarifications or to confirm implementation plans when necessary.
- Adhere to all other defined rules and workflows (e.g., creating unit tests for new features).
- For each item follow next steps in current section 4.

### 4.1. Analysis and Planning

- This step must be handled by the Architect Agent.
- Identifies ambiguities and areas needing user clarification.
- Researches required technologies, frameworks, or APIs.
- Analyzes the current project status.
- Defines a high-level approach for the solution/implementation, creating a step-by-step plan including:
  - git handling (check steps below)
  - code writing (check steps below)
  - running console cmds (when required)
  - test build (if exists)
  - code review
  - testing implementation (if set-up in the project)
  - documentation updates (check steps below)
  - mark the item inside the TODO file as DONE (check steps below)
  - etc.
- IMPORTANT: After the high-level approach, redefines the plan in very tiny and very detailed steps, including clear files names/paths, structure, code snippets, where/how run terminal cmds, and any other relevant details.
- Review the plan for any necessary changes.
- CRITICAL **File Storage**: the plan must be saved to a file in `.kilocode/_generated/plans/` with a unique name (e.g., `<datetime>-<plan-name>.md`) in almost all cases. So, the Coder Agent (or any other) can receive this file to work on.
- The plan must be presented to the user for approval before proceeding with the next steps.
- Although the Architect Agent is responsible for creating the plan, the Orchestrator Agent is responsible for ensuring that the plan is followed and that the appropriate agents are assigned to the appropriate tasks.
- Always check the details of the original task before proceeding with the next steps.
- General process must be: the Orchestrator creates a step to generate the plan in a sub-task, that must respond with the plan file path. Then, the Orchestrator Agent can assign the Coder Agent to implement the plan in another sub-task.

### 4.2. Implementation

- This step must be handled by the Coder Agent.
- Receives and implements individual extremely tiny and very detailed steps from the plan.
- Always check the details of the original task before proceeding with the next steps.
- IMPORTANT: Make commits with meaningful messages at the end of the work done to resolve each one of the TODO file items.

### 4.3. Code Review

- This step may be handled by Architect or Code Reviewer or Code Simplifier Agent.
- Reviews the implemented code for errors or deviations from the plan.
- Requests necessary changes to the Coder Agent.

### 4.4 Documentation

- This step must be handled by the Documentator Agent.
- Adds comments to the code where necessary.
- Updates the project's documentation (e.g., README, `/docs` files).
- Suggests and implements automated documentation tools.

### 4.5. Item Completion

- When the Implementation of a plan's item is completed, the item in the TODO file must be clearly marked as done, details below.
- Mark the item as done in the TODO file:
  - **Line Item Format**: Add `[DONE]` at the beginning of the line.
  - **Section Item Format**: Add `[DONE]` to the section title.
  - **Other Format**: Add `[DONE]` to the appropriate section or line as needed.
  **Take care to don't delete the content of the file, or change its original content, except for the addition of the `[DONE]` mark**
- IMPORTANT: Commit all changes to the current branch with a meaningful message.

## 5. TODO File Completion

- When all items in a TODO file are resolved (ie. marked as done as indicates the step 6), rename the file with a `-DONE` suffix (e.g., `<YYYYMMDD>-todo-<number>-DONE.md`), and commit it.
  **Take care to don't delete the file, or changes its content. Only rename it**
- Merge the current feature branch into the master branch:
  - IMPORTANT: Ensure all files are committed in feature branch. If not, stage them and commit them before continue.
  - Switch to the `main` branch, which is the master branch.
  - Merge the feature branch into the `main` branch.
  - Recheck the feature branch was correctly merged into `main` branch.
    - If it was correctly merged, then delete the feature branch. It is IMPORTANT to verify BEFORE deleting the feature branch.
    - If the feature branch was not correctly merged into the `main` branch, then ask the user to resolve the merge conflicts and then retry the merge process.
  - If an `origin` remote repository exists, then push the latest `main` branch commits to the remote repository.

## 6. Continuation

- After a TODO file is completed, check for any remaining TODO files.
- If other TODO files exist, ask the user whether to proceed with the next one or not. If the response is affirmative, then is preferable to start with the next file in a completely new chat, finalizing the current one.
- If no TODO files remain, the work is finished.
