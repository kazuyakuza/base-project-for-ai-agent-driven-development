# Tool Selection Priority Rule

- Before using any tool, evaluate the FULL set of available tools — including any MCP-provided tools — for the operation at hand.
- Prefer tools with semantic understanding of the codebase when they are available (e.g., `vscode-mcp-server_*`, `Bifrost_*` code/semantic tools, `semantic_search`) over raw file or text commands for code operations.
- **Fallback (no semantic/MCP tools available)**: If no semantic/code-aware MCP tools are exposed in the current environment (e.g., when running with built-in tools only), use the built-in tools:
  - Use `glob`/`grep`/`list`/`read` for code reading, searching, and navigation.
  - Use structured `edit`/`write` tools for code changes — do not fall back to raw shell commands for file manipulation.
  - Prefer `edit` over `bash` for file modifications; reserve `bash` for CLI-native operations (git, npm, builds, tests, and similar cmds).
- Never use `vscode-mcp-server_execute_shell_command_code` if the environment exposes it.
- When using bash tool:
  - if "unknown cmd" or similar error arises, try up to 2 more times the same cmd.
  - prevent execute cmds composed by sub-cmds (cmds with &/&&, for example)
- On `bash` tool, only run single cmds, ie. not concurrent nor chained cmds.
- Use `PowerShell` only as last option.