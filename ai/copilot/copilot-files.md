# Copilot Default Files & Directories
[🔙README](/README.md)

Overview of the official file/folder structure for GitHub Copilot in VS Code (as of June 2026).
Sources: [VS Code Docs – Custom Instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions) · [Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files) · [Custom Agents](https://code.visualstudio.com/docs/copilot/chat/chat-modes)

```
.github/
├─ copilot-instructions.md          # Repository-wide always-on instructions (automatically included in every chat request)
├─ instructions/                    # Path-specific instruction files
│  ├─ <name>.instructions.md        # Conditional instructions with YAML frontmatter (applyTo glob)
│  └─ ...
├─ prompts/                         # Reusable prompt files (slash commands)
│  ├─ <name>.prompt.md              # Manually invokable prompt templates (via /name in chat)
│  └─ ...
└─ agents/                          # Custom agent definitions (workspace scope)
   ├─ <name>.agent.md               # Agent definition (YAML frontmatter + Markdown body)
   └─ ...

AGENTS.md                           # Always-on instructions (workspace root); recognized by multiple AI tools
                                    # Can also be placed in subfolders (experimental, chat.useNestedAgentsMdFiles)

CLAUDE.md                           # Always-on instructions for Claude Code / VS Code compatibility
CLAUDE.local.md                     # Local variant (do not commit to VCS)

.claude/                            # Claude-compatible configuration (supported by VS Code)
├─ CLAUDE.md                        # Always-on instructions (Claude format)
├─ agents/                          # Custom agents in Claude format (plain .md files)
│  └─ <name>.md                     # Claude sub-agents format (name, description, tools as CSV)
└─ rules/                           # Path-specific instructions in Claude format
   └─ <name>.md                     # Uses 'paths' array instead of 'applyTo' for glob patterns

~/.copilot/                         # User-level Copilot configuration (applies across all workspaces)
├─ agents/                          # Global custom agents (available in all workspaces)
│  └─ <name>.agent.md               # Same format as workspace agents
├─ instructions/                    # Global instructions (applied in all workspaces)
│  └─ <name>.instructions.md        # Same format as workspace instructions
└─ prompts/                         # Global prompt templates
   └─ <name>.prompt.md              # Same format as workspace prompts

~/.claude/                          # User-level Claude configuration
├─ CLAUDE.md                        # Personal always-on instructions across all projects
└─ rules/                           # Global Claude-format instructions
   └─ <name>.md
```

## Key File Details

### Always-on Instructions

#### `.github/copilot-instructions.md`

**Short description**\
Repository-wide custom instructions that are automatically included in every chat request within the workspace.

**Details**\
Ideal for coding standards, tech stack declarations, architecture patterns, and project-wide conventions. VS Code detects this file automatically when it is placed in the `.github` folder at the workspace root. No frontmatter required.

**Example**

```md
# Repository Copilot Instructions
Project: Payments API
Preferred language: TypeScript
Test command: npm test
Conventions: Use Money type for currency; avoid floating point for amounts.
```

---

#### `AGENTS.md`

**Short description**\
Always-on instructions file in the workspace root, recognized by multiple AI tools (Copilot, Codex, OpenAI Agents, etc.).

**Details**\
Useful when multiple AI agents are used in the same workspace and a shared instruction source is needed. Can also be placed in subfolders for monorepo-specific instructions by enabling the `chat.useNestedAgentsMdFiles` setting.

---

### Path-specific Instructions

#### `.github/instructions/<name>.instructions.md`

**Short description**\
Instruction files that are only applied to files matching an `applyTo` glob pattern (or whose description matches the current task).

**Details**\
Suited for language- or framework-specific conventions. Without `applyTo`, they are not applied automatically but can be manually attached to a chat request. VS Code searches the folder recursively — subdirectories are supported.

**Frontmatter fields**

| Field | Required | Description |
|---|---|---|
| `name` | No | Display name in the UI (defaults to file name) |
| `description` | No | Short description shown on hover |
| `applyTo` | No | Glob pattern relative to the workspace root |

**Example**

```md
---
name: 'TypeScript Standards'
description: 'Coding conventions for TypeScript files'
applyTo: 'src/**/*.ts'
---
# TypeScript conventions
- Use strict mode.
- Always define return types on public functions.
- Prefer `const` over `let`.
```

---

### Prompt Files (Slash Commands)

#### `.github/prompts/<name>.prompt.md`

**Short description**\
Reusable prompt templates that are manually invoked via `/name` in chat.

**Details**\
Unlike instructions, prompt files are not applied automatically — they must be explicitly triggered. Well suited for recurring tasks such as component scaffolding, test generation, or PR descriptions.

**Frontmatter fields**

| Field | Required | Description |
|---|---|---|
| `description` | No | Short description |
| `name` | No | Name for the slash command (defaults to file name) |
| `agent` | No | Agent to use: `ask`, `agent`, `plan`, or a custom agent name |
| `model` | No | Language model to use |
| `tools` | No | List of tools available for this prompt |

**Example**

```md
---
description: Summarize a PR in 3 bullet points
agent: ask
---
Summarize the changes in this pull request in 3 bullet points and list any breaking changes.
```

---

### Custom Agents

#### `.github/agents/<name>.agent.md`

**Short description**\
Workspace-scoped custom agent definitions with YAML frontmatter for metadata and a Markdown body for agent instructions.

**Details**\
Agents are persistent personas with their own tool set, model selection, and optional handoffs to other agents. Workspace agents override user-level agents with the same name. VS Code also detects plain `.md` files (without the `.agent.` suffix) in this folder.

**Frontmatter fields (selection)**

| Field | Description |
|---|---|
| `description` | Short description (shown as placeholder text in the chat input) |
| `name` | Display name (defaults to file name) |
| `tools` | List of allowed tools (built-in, MCP, extension tools) |
| `model` | Preferred language model (string or array with fallback order) |
| `handoffs` | Optional transitions to other agents after completion |
| `user-invocable` | `false` = agent only usable as a subagent, hidden from the dropdown |

**Example**

```md
---
description: Reviews code for correctness, style, and security
tools: ['search', 'fs_read']
---
You are a code review specialist. Focus on correctness, security, readability, and test coverage.
Check for: edge cases, error handling, logging, and performance regressions.
```

---

### Claude Compatibility

#### `CLAUDE.md` / `.claude/CLAUDE.md` / `~/.claude/CLAUDE.md`

**Short description**\
Always-on instructions in Claude format. Recognized by VS Code and support simultaneous use with Claude Code.

**Details**\
VS Code searches in this order: workspace root → `.claude/CLAUDE.md` → `~/.claude/CLAUDE.md`. `CLAUDE.local.md` serves as a local variant that should not be committed to VCS.

---

#### `.claude/agents/<name>.md`

**Short description**\
Custom agents in Claude sub-agents format. Recognized by VS Code in addition to the `.github/agents/` files.

**Details**\
Uses comma-separated strings for `tools` instead of YAML arrays (Claude format). Allows sharing agent definitions between VS Code and Claude Code.

**Frontmatter fields**

| Field | Description |
|---|---|
| `name` | Agent name (required) |
| `description` | Short description |
| `tools` | Comma-separated list of allowed tools, e.g. `"Read, Grep, Bash"` |
| `disallowedTools` | Comma-separated list of blocked tools |

---

#### `.claude/rules/<name>.md`

**Short description**\
Path-specific instructions in Claude format. Equivalent to `.instructions.md` files but use `paths` instead of `applyTo`.

**Details**\
The `paths` array accepts glob patterns and follows the [Claude Rules format](https://code.claude.com/docs/en/memory#basic-structure). When `paths` is omitted, the instructions are applied to all files (`**`).

---

### User-level Configuration

#### `~/.copilot/agents/<name>.agent.md`, `~/.copilot/instructions/<name>.instructions.md`, `~/.copilot/prompts/<name>.prompt.md`

**Short description**\
User-level counterparts to the workspace files — available in all workspaces. Workspace definitions take precedence in case of name conflicts.

---

### Practical Notes

* **File paths:** `.github/instructions/`, `.github/prompts/`, and `.github/agents/` are the official default paths — **not** `.github/copilot/instructions/` or similar.
* **File extensions:** Instructions → `.instructions.md`, Prompts → `.prompt.md`, Agents → `.agent.md`
* **Priority:** User instructions (highest) → repository instructions → organization instructions (lowest)
* **Custom chat modes:** The former `.chatmode.md` extension has been renamed to `.agent.md`. Existing files should be migrated accordingly.
* **Additional locations:** Can be configured via settings (`chat.instructionsFilesLocations`, `chat.promptFilesLocations`, `chat.agentFilesLocations`).
* **Security:** Never store secrets, API keys, or credentials in instruction, agent, or prompt files.
