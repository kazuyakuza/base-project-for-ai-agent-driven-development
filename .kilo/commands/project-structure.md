---
description: Project structure maintenance workflow
agent: planner
---
# Project Structure Maintenance Workflow

## Overview

This workflow manages the creation, updates, and reorganization of the `.agent/project-structure.md` file, which outlines the folder structure inside the `src` directory. The file contains one line per folder path followed by a minimal comment understandable for AI agents.

## Creation Workflow

When the `.agent/project-structure.md` file does not exist:

1. Use `task` tool to assign to Architector sub-agent (`subagent_type: "architector"`)
2. Architector sub-agent analyzes the project requirements and generates a base folder structure for the `src` directory
3. Architector sub-agent presents the proposed structure to the user for confirmation
4. Upon user confirmation, use `task` tool to assign to Implementer sub-agent (`subagent_type: "implementer"`)
5. Implementer sub-agent creates the `.agent/project-structure.md` file with the approved structure

## Update Workflow

When folders are added, removed, or renamed in the `src` directory:

1. Automatically update the `.agent/project-structure.md` file to reflect the current structure
2. Each line contains the folder path relative to `src` followed by a brief AI-agent-understandable comment

## Reorganization Workflow

When the project structure needs reorganization:

1. Use `task` tool to assign to Architector sub-agent (`subagent_type: "architector"`)
2. Architector sub-agent reviews the current structure in `.agent/project-structure.md` and project context
3. Architector sub-agent proposes a new structure based on current needs
4. Architector sub-agent presents the proposed reorganization to the user for confirmation
5. Upon user confirmation, use `task` tool to assign to Implementer sub-agent (`subagent_type: "implementer"`)
6. Implementer sub-agent updates the `.agent/project-structure.md` file with the new structure
7. Implementer sub-agent may also move existing files/folders to match the new structure if requested

## File Format

- Section headers separate categories: `# Folders in src/` and `# Other folders`
- Under `# Folders in src/`: one folder path per line using bullet-point format (`- folder/path/ - brief comment for AI agent`)
- Under `# Other folders`: project-level directories that support development (e.g., `.kilo/modes/`, `docs/`)
- Comments should be minimal and focused on the folder's purpose from an AI agent's perspective
- Only folders are documented (not files)
- Empty sections use `# (no folders yet)` placeholder

## Enforcement

- All AI agents must review `.agent/project-structure.md` before creating new files or folders
- This ensures proper placement within the established structure
