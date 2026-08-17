---
description: Project info initialization workflow for new projects
agent: planner
---
# Project Info Initialization Workflow

## Trigger Conditions

This workflow MUST be triggered when ALL of the following conditions met:

1. **Not Base Project**: The current project root directory is NOT `base-project-ai-agent-driven`.
2. **Default Brief Detected**: The file `.agent/project-info/.initialized` exists.

## Immediate Actions

When triggered, the AI agent follow next steps:

1. **Notify User**: Immediately display the following message to the user:
    > "The project info brief file is not defined. It is recommended to define it before start working."

2. **Provide Context**: The project is designed as a base template for AI-agent driven development with rules, workflows, and structures optimized for collaboration.

3. **Offer Assistance**: Ask the user:
    > "Do you want to skip this for now, or work on defining the brief now?"

4. **Assist**: If the user chooses to work on it, assist in defining the content for `brief.md` based on project goals.

5. **Final Instruction**: Before ending the communication, AI agent MUST explicitly state:
    > "After you define brief.md file, in a new chat, you must: run `/critical-workflow`, select the best available AI model, then Ask to 'initialize project info'"
