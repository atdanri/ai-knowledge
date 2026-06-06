# Antigravity Default Files & Directories
[🔙README](/README.md)

Overview of the default file/folder structure used by Antigravity for agent-first development, configuration, and runtime state.

```
<workspace-root>/                   # Workspace scope
├── GEMINI.md                       # ⭐ Project-level context/steering file (instructions, coding style, architecture)
├── <subdir>/GEMINI.md              # ⭐ Component-level context override (scoped to subdirectory)
├── .geminiignore                   # ⭐ Glob patterns for files/dirs the agent should ignore (like .gitignore)
├── .aiexclude                      # ⭐ AI-wide file exclusion list (respected by Gemini Code Assist & CLI)
├── agents.md                       # Define custom agent personas (YAML front-matter + Markdown body)
├── .agent/                         # Workspace-level agent resources
│   ├── skills/                     # Workspace-level skill definitions (modular task instructions)
│   │   └── <skill-name>/           # Individual skill package folder
│   │       └── SKILL.md            # Skill instructions and metadata
│   └── workflows/                  # Custom workflow/slash-command definitions (Markdown + YAML front-matter)
│       └── <workflow-name>.md      # Becomes a /<workflow-name> slash command
└── .antigravity/                   # Project-wide standards and behavior guidelines
    └── rules.md                    # Project rules enforced by the agent

~/.gemini/                          # User-level (global) scope
├── GEMINI.md                       # ⭐ Global context/steering file (personal defaults, universal coding style)
├── settings.json                   # ⭐ Global IDE/CLI settings (model prefs, themes, MCP paths, etc.)
└── config/                         # Global configuration directory — applies across all workspaces
    ├── config.json                 # General platform settings (model selection, execution policies, etc.)
    ├── mcp_config.json             # Global Model Context Protocol (MCP) server configurations
    ├── hooks.json                  # Global automation hooks (event -> action)
    ├── projects/                   # Workspace projects registry
    │   └── <project-id>.json       # Per-project settings and permission policies
    ├── plugins/                    # Bundles of custom skills, subagents, and configurations
    │   └── <plugin-name>/
    │       ├── plugin.json         # Plugin metadata (name, version, description)
    │       ├── skills/             # Plugin-provided skills
    │       └── agents/             # Plugin-provided subagents
    ├── skills/                     # Global skill definitions (.md files/directories)
    ├── agents/                     # Global agent definitions (.md files/directories)
    └── sidecars/                   # Sidecar extensions configuration

<appDataDir>/antigravity-ide/       # App runtime state, logs, and cache (e.g., C:\Users\xyz\.gemini\antigravity-ide)
├── mcp_config.json                 # Workspace-specific MCP config override
├── skills/                         # Cached workspace skill files
├── scratch/                        # Temporary scripts and one-off debug files
├── browser_recordings/             # WebP recordings of browser agent activities
├── html_artifacts/                 # Generated UI preview pages and visual assets
├── worktrees/                      # Managed git worktrees for parallel execution
├── knowledge/                      # Localized knowledge items repository
│   ├── knowledge.lock              # Lock file for concurrency control
│   └── <topic>/                    # Directory containing localized topic information
│       ├── metadata.json           # Summary, timestamps, and references to original sources
│       └── artifacts/              # Related files and implementation details
└── brain/                          # Conversation and task orchestration data
    └── <conversation-id>/          # Specific conversation workspace
        ├── task.md                 # Checklist tracker for ongoing tasks (TODO, in-progress, done)
        ├── implementation_plan.md  # Detailed implementation design presented for user feedback
        ├── walkthrough.md          # Completed task summaries and visual demo recordings
        ├── scratch/                # Conversation-specific scratchpad files
        └── .system_generated/logs/
            └── transcript.jsonl    # Chronological history log of system/agent steps
```

## Key File Details

### Context / Steering Files (`GEMINI.md`)

**Short description**\
The primary mechanism for providing persistent, project-specific instructions to the AI agent. Acts as the agent's "project DNA".

**Details**\
Antigravity uses a **hierarchical** context system. `GEMINI.md` files are automatically discovered and concatenated — more specific files override more general ones:

| Level | Path | Scope |
|---|---|---|
| Global | `~/.gemini/GEMINI.md` | Personal defaults for all projects |
| Project | `<workspace-root>/GEMINI.md` | Architecture, stack, conventions for this project |
| Component | `<subdir>/GEMINI.md` | Module-specific patterns (e.g., DB schemas, API styles) |

- Use `/memory show` to inspect the currently active combined context.
- Use `/memory refresh` to reload after editing a `GEMINI.md` file mid-session.
- Use `@` imports inside `GEMINI.md` to modularize context across files.
- Use `/init` in the CLI to generate a starter `GEMINI.md` for a project.

**Example**

```md
# Project Context

## Tech Stack
- TypeScript + React 19, Vite
- TailwindCSS v4 for styling
- Zustand for state management

## Coding Standards
- Always use JSDoc comments for public APIs
- Prefer functional components over class components
- Use named exports, avoid default exports

## Architecture
@docs/architecture.md
```

---

### Ignore Files (`.geminiignore` / `.aiexclude`)

**Short description**\
Control which files and directories the AI agent is allowed to index, read, or use as context.

**Details**

| File | Scope | Notes |
|---|---|---|
| `.geminiignore` | Gemini CLI / Antigravity only | Native ignore file; supports `.gitignore` syntax. Use `!filename` to un-ignore. |
| `.aiexclude` | All AI tools (Gemini, Code Assist, etc.) | Industry-standard exclusion; protects sensitive data across tooling. |

Both follow glob pattern syntax. The CLI also respects `.gitignore` by default.

**Example** (`.geminiignore`)

```gitignore
# Ignore build output and secrets
dist/
node_modules/
.env*
*.secret

# But allow the env example
!.env.example
```

---

### Global Settings (`~/.gemini/settings.json`)

**Short description**\
Global IDE and CLI settings for model preferences, themes, MCP server paths, and general environment configuration.

**Details**\
This file sits at the `~/.gemini/` root level (not inside `config/`). It applies user-wide defaults for the Antigravity IDE and CLI.

---

### Agent Personas (`agents.md` or `~/.gemini/config/agents/`)

**Short description**\
Defines custom AI personas (e.g., Architect, Tester) with dedicated instructions, tools, and preferred models.

**Details**\
Agent definitions contain configuration in a YAML front-matter block, followed by the system prompt/instructions in the Markdown body.

**Example**

```md
---
name: Code Reviewer
description: Reviews code changes for quality and standards compliance
tools:
  - read_file
  - grep_search
model: gemini-3.5-flash
---
You are a code review specialist. Focus on correctness, security, and readability.
Always verify that...
```

---

### Workspace Skills (`.agent/skills/<skill-name>/SKILL.md`)

**Short description**\
Modular task instructions that can be dynamically loaded by agents to perform specific procedures or workflows.

**Details**\
Each skill is organized in a folder containing metadata and step-by-step instructions. Agents can fetch and follow these instructions on demand.

**Example**

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

---

### Workflows (`.agent/workflows/<name>.md`)

**Short description**\
Custom automation recipes stored as Markdown files that become slash commands in the IDE.

**Details**\
Each `.md` file in `.agent/workflows/` is auto-registered as a `/<filename>` slash command. Files require a YAML front-matter with a `description` field.

**Special annotations:**
- `// turbo` before a step → execute that command without confirmation
- `// turbo-all` in the file → auto-execute all commands in the workflow

**Example** (`.agent/workflows/deploy.md`)

```md
---
description: Build and deploy the application to staging
---
## Steps
// turbo
1. Run `npm run build` to create the production bundle
2. Run `npm run test` to verify all tests pass
3. Run `gcloud app deploy --project=my-app --version=staging`
4. Verify the deployment at https://staging.my-app.com
```

---

### Project Rules (`.antigravity/rules.md`)

**Short description**\
Project-wide behavior rules and standards that the agent enforces automatically.

**Details**\
Rules placed in `.antigravity/rules.md` serve as persistent guardrails for the agent, complementing `GEMINI.md` context with enforceable constraints.

---

### Planning & Orchestration Artifacts (`brain/<conversation-id>/`)

**Short description**\
Special Markdown files used by agents during Planning Mode to coordinate work, list tasks, and document progress.

**Details**

| File | Purpose |
|---|---|
| `implementation_plan.md` | Detailed design document outlining proposed changes, open questions, and verification plans to present to the user for feedback and approval. |
| `task.md` | An active checklist showing completed, in-progress, and uncompleted items to track implementation progress. |
| `walkthrough.md` | Summary of completed work, including tests run, validation results, and visual demo attachments (e.g., screenshots or recordings). |

---

### Project Configuration (`~/.gemini/config/projects/<project-id>.json`)

**Short description**\
JSON configuration storing settings, folders, and policy levels for a specific workspace project.

**Details**\
Determines settings like internet policies, command execution levels, and file access policies.

**Example**

```json
{
  "id": "5231e51b-cd36-4539-ba07-aab30b49081f",
  "name": "ai-knowledge",
  "projectResources": {
    "resources": [
      {
        "gitFolder": {
          "folderUri": "file:///d%3A/projects/ai-knowledge"
        }
      }
    ]
  },
  "settings": {
    "fileAccessPolicy": "AGENT_SETTING_POLICY_ASK",
    "internetPolicy": "AGENT_SETTING_POLICY_ASK",
    "autoExecutionPolicy": "CASCADE_COMMANDS_AUTO_EXECUTION_OFF",
    "artifactReviewMode": "ARTIFACT_REVIEW_MODE_ALWAYS"
  }
}
```

---

### Knowledge Items (`knowledge/<topic>/`)

**Short description**\
Curated snapshots of localized context and repository-specific patterns to guide the agent and avoid redundant design research.

**Details**

| File | Purpose |
|---|---|
| `metadata.json` | Contains summaries, timestamps, and references to original sources. |
| `artifacts/` | Subdirectory containing related files, documentation, and specific implementation details. |

---

### Plugin Packages (`~/.gemini/config/plugins/<plugin-name>/`)

**Short description**\
Bundles extending the capabilities of the agent platform with packaged metadata, custom subagents, and modular skills.

**Details**\
Plugins allow packaging skills and subagents together under a single folder structure.

| File/Folder | Purpose |
|---|---|
| `plugin.json` | JSON metadata defining plugin version, name, and description. |
| `skills/` | Subagent skills provided by the plugin. |
| `agents/` | Custom subagents provided by the plugin. |

---

### MCP Configuration (`mcp_config.json`)

**Short description**\
JSON settings to connect local tools, MCP servers, and external services to the IDE.

**Details**\
Defines commands, environment variables, arguments, and auto-approve permissions for MCP servers.

**Example**

```json
{
  "mcpServers": {
    "git-server": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"],
      "env": {
        "PATH": "/usr/bin:/usr/local/bin"
      }
    }
  }
}
```

---

### Automation Hooks (`~/.gemini/config/hooks.json`)

**Short description**\
Automation rules that map triggers (e.g., file edits, prompt submissions) to actions.

**Details**\
Allows registering automated command executions or agent prompt events in response to workspace or lifecycle triggers.

---
> ⚠️ **Attention:** autogenerated
