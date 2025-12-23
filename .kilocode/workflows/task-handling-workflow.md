# Task Handling Workflow

## Prerequisites

Before proceeding with this workflow, it is essential to be familiar with the **Git Flow Workflow**, which is an integral part of the task management process. This workflow will be referenced at various stages.

## 1. Task Origin

- **Chat**: If a task (ie. almost any work to do) is asked/given in the chat (except the user is indicating an existing TODO file), create a new TODO file in `.ai-agent/todos/<YYYYMMDD>/<YYYYMMDD>-todo-<number>.md`. The content of the file should be the user's request.
- **TODO File**: The primary source of tasks is the `.ai-agent/todos` directory, which contains TODO files that need to be processed, organized by date and numbered sequentially.

## 2. Task Selection

- Process TODO files in chronological and numerical order from the `.ai-agent/todos` directory.
- Files ending in `-DONE` are considered finished and should be skipped.

## 3. Initial Setup

- Create a new branch following the **Git Flow Workflow** before starting work on the task, ensuring the branch name reflects the task's purpose or TODO file's name.
- If the project has a version number (e.g., in `package.json`), increment it following the `x.y.z` format.

## 4. TODO File Format

- **Line Items**: Each line is a separate task.
- **Section Items**: Each markdown section is a separate task, potentially with additional details.
- **Other Formats**: If the format is unclear, ask the user for clarification.

## 5. Task Execution

- Process items within the TODO file in the order they are defined.
- Before starting a new item, commit any pending changes to the current branch with a meaningful message.
- For each item:
  - Ask the user for clarifications or to confirm implementation plans if necessary.
  - Adhere to all other defined rules and workflows (e.g., creating unit tests for new features).

## 6. Item Completion

- Mark the item as done in the TODO file:
  - **Line Item**: Add `[DONE]` at the beginning of the line.
  - **Section Item**: Add `[DONE]` to the section title.
- Commit all changes to the current branch with a meaningful message.

## 7. TODO File Completion

- When all items in a TODO file are resolved, rename the file with a `-DONE` suffix (e.g., `<YYYYMMDD>-todo-<number>-DONE.md`).
- Commit any final pending changes.
- Merge the current branch into the master branch, following the **Git Flow Workflow**.

## 8. Continuation

- After a TODO file is completed, check for any remaining TODO files.
- If other TODO files exist, ask the user whether to proceed with the next one.
- If no TODO files remain, the work is finished.
