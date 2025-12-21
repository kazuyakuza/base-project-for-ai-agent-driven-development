# Project Structure Maintenance Workflow

## Overview
This workflow manages the creation, updates, and reorganization of the `.ai-agent/project-structure.md` file, which outlines the folder structure inside the `src` directory. The file contains one line per folder path followed by a minimal comment understandable for AI agents.

## Creation Workflow
When the `.ai-agent/project-structure.md` file does not exist:

1. Switch to Architect mode
2. Architect mode analyzes the project requirements and generates a base folder structure for the `src` directory
3. Architect mode presents the proposed structure to the user for confirmation
4. Upon user confirmation, switch to Code mode
5. Code mode creates the `.ai-agent/project-structure.md` file with the approved structure

## Update Workflow
When folders are added, removed, or renamed in the `src` directory:

1. Automatically update the `.ai-agent/project-structure.md` file to reflect the current structure
2. Each line contains the folder path relative to `src` followed by a brief AI-agent-understandable comment

## Reorganization Workflow
When the project structure needs reorganization:

1. Switch to Architect mode
2. Architect mode reviews the current structure in `.ai-agent/project-structure.md` and project context
3. Architect mode proposes a new structure based on current needs
4. Architect mode presents the proposed reorganization to the user for confirmation
5. Upon user confirmation, switch to Code mode
6. Code mode updates the `.ai-agent/project-structure.md` file with the new structure
7. Code mode may also move existing files/folders to match the new structure if requested

## File Format
- One folder path per line
- Format: `folder/path/ - brief comment for AI agent`
- Comments should be minimal and focused on the folder's purpose from an AI agent's perspective
- Only folders inside `src` are documented (not files)

## Enforcement
- All AI agents must review `.ai-agent/project-structure.md` before creating new files or folders
- This ensures proper placement within the established structure