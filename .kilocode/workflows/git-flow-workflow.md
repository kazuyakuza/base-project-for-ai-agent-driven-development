# Git Flow Workflow

> **Note:** This workflow is a component of the overall **Task Handling Workflow** and is not intended to be used in isolation. It is invoked at specific stages of the task handling process to manage branches for new tasks.

## 1. Start a New Task

- Switch to the `main` branch.
- Create a new branch with a descriptive name:
  - For new features: `feat/<meaning-name>`
  - For bug fixes: `fix/<meaning-name>`

## 2. Development

- Work on the feature branch.
- Make commits with meaningful messages.

## 3. Finish a Task

- Ensure all files are committed.
- Switch to the `main` branch.
- Merge the feature branch into the `main` branch.
- Delete the feature branch.
- If an `origin` remote repository exists, then push the latest `main` branch commits to the remote repository.
