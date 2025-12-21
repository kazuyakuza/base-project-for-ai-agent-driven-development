# Memory Bank Initialization Workflow

## Trigger Conditions

This workflow MUST be triggered when ALL of the following conditions are met:

1. **Not Base Project**: The current project root directory is NOT `base-project-ai-agent-driven`.
2. **Default Brief Detected**: The file `.kilocode/rules/memory-bank/brief.md` contains the exact text: "THIS MARKS THE FILE HAS ITS DEFAULT VALUE".
3. **Kilo Code Active**: The user is interacting with the AI agent via the Kilo Code plugin.

## Immediate Actions

When triggered, the AI agent MUST strictly follow these steps:

1. **Notify User**: Immediately display the following message to the user:
    > "The memory bank brief file is not defined. It is recommended to define it before start working."

2. **Provide Context**: Append the explanation found in the default `brief.md` file (the comment below the marker).

3. **Offer Assistance**: Ask the user:
    > "Do you want to skip this for now, or work on defining the brief now?"

4. **Assist (Optional)**: If the user chooses to work on it, assist them in defining the content for `brief.md` based on their project goals.

5. **Final Instruction**: Before ending the communication (regardless of whether the user updated the brief or skipped), the AI agent MUST explicitly state:
    > "After you define brief.md file, in a new chat, you must: Switch to Architect mode, select the 'best' available AI model, then Ask to 'initialize memory bank'"
