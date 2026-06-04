# Kiro Default Files & Directories
[🔙README](/README.md)

Overview of the default file/folder structure used by Kiro for spec-driven development and configuration.

```
.kiro/
├── specs/                          # Spec-driven development artifacts
│   └── <feature-name>/             # One folder per feature/spec
│       ├── requirements.md         # User stories & requirements (auto-generated, editable)
│       ├── design.md               # Architecture & design decisions
│       └── tasks.md                # Incremental implementation task list
│
├── steering/                       # Steering files – persistent context injected into agent prompts
│   ├── product.md                  # Product overview, goals, target audience (always included by default)
│   ├── tech.md                     # Tech stack, libraries, conventions (always included by default)
│   ├── structure.md                # Project layout and code organisation rules (always included by default)
│   └── <custom>.md                 # Any additional steering file; inclusion controlled via front-matter:
│                                   #   inclusion: auto       → always included
│                                   #   inclusion: fileMatch  → included when matched file is in context
│                                   #     fileMatchPattern: 'src/**/*.ts'
│                                   #   inclusion: manual     → included only when user adds '#' context key
│
├── agents/                         # Custom subagent definitions (workspace scope)
│   └── <agent-name>.md             # One Markdown file per agent; prompt in body, config in YAML front-matter
│                                   #   name, description, tools, mcpServers, etc.
│
├── skills/                         # Skill files – portable instruction packages (workspace scope)
│   └── <skill-name>.md             # Markdown file with YAML front-matter (name, description)
│                                   # Only metadata loaded at startup; full content loaded on demand
│
├── settings/                       # Workspace-level Kiro settings
│   └── mcp.json                    # MCP server configuration (workspace scope)
│                                   # Merged with user-level config; workspace takes precedence
│
└── hooks/                          # Agent automation hooks (event → action)
    └── <hook-name>.json            # One JSON file per hook; triggers on IDE/agent events
                                    # Events: fileEdited, fileCreated, fileDeleted,
                                    #         promptSubmit, agentStop,
                                    #         preToolUse, postToolUse,
                                    #         preTaskExecution, postTaskExecution,
                                    #         userTriggered
                                    # Actions: askAgent (prompt the agent)
                                    #          runCommand (run a shell command)

~/.kiro/                            # User-level (global) Kiro config – applies across all workspaces
├── agents/                         # Global custom subagent definitions (available in all workspaces)
│   └── <agent-name>.md             # Same format as workspace agents; workspace agents take precedence
├── skills/                         # Global skill files (available in all workspaces)
│   └── <skill-name>.md             # Same format as workspace skills
└── settings/
    └── mcp.json                    # User-level MCP server configuration
                                    # Lowest precedence; overridden by workspace mcp.json
```

## Key File Details

### Spec files (`.kiro/specs/<feature>/`)

| File | Purpose |
|---|---|
| `requirements.md` | Captures *what* to build – user stories in "As a … I want … so that …" format with acceptance criteria |
| `design.md` | Captures *how* to build it – component design, data models, API contracts |
| `tasks.md` | Ordered checklist of implementation tasks; Kiro works through these step by step |

### Steering files (`.kiro/steering/`)

Markdown files with an optional YAML front-matter block that controls when the file is injected:

```md
---
inclusion: fileMatch
fileMatchPattern: 'src/**/*.ts'
---
# TypeScript conventions
...
```

Steering files may reference other project files using `#[[file:<relative-path>]]` to pull in specs, OpenAPI docs, GraphQL schemas, etc.

### Agent files (`.kiro/agents/<agent-name>.md`)

Markdown files that define custom subagents. The agent prompt goes in the file body; configuration is in YAML front-matter:

```md
---
name: Code Reviewer
description: Reviews code changes for quality and standards compliance
tools:
  - fs_read
  - execute_bash
---
You are a code review specialist. Focus on correctness, security, and readability.
Always check for...
```

Workspace agents (`.kiro/agents/`) take precedence over global agents (`~/.kiro/agents/`).

### Skill files (`.kiro/skills/<skill-name>.md`)

Portable instruction packages for specific workflows. Only the metadata (name, description) is loaded at startup; the full content is fetched on demand when the agent needs it.

```md
---
name: pull-request-review
description: Step-by-step workflow for reviewing pull requests
---
## PR Review Workflow
1. Check that the PR description explains *why* the change is needed
2. Verify test coverage for all new code paths
...
```

Workspace skills (`.kiro/skills/`) are available only in the current workspace; global skills (`~/.kiro/skills/`) are available everywhere.

### MCP configuration (`.kiro/settings/mcp.json`)

```json
{
  "mcpServers": {
    "<server-id>": {
      "command": "uvx",
      "args": ["<package>@latest"],
      "env": { "SOME_KEY": "value" },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Precedence order (lowest → highest): `~/.kiro/settings/mcp.json` → `.kiro/settings/mcp.json` (workspace).

### Hook file schema (`.kiro/hooks/<hook-name>.json`)

```json
{
  "name": "Run tests after task",
  "version": "1.0.0",
  "description": "Runs the test suite whenever a spec task completes",
  "when": {
    "type": "postTaskExecution"
  },
  "then": {
    "type": "runCommand",
    "command": "mvn test"
  }
}
```
