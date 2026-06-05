# Antigravity Default Files & Directories
[🔙README](/README.md)

Overview of the default file/folder structure used by Antigravity for agent-first development, configuration, and runtime state.

```
<workspace-root>/                   # Workspace scope
├── agents.md                       # Define custom agent personas (YAML front-matter + Markdown body)
├── .agents/                        # Workspace-level agent resources
│   └── skills/                     # Workspace-level skill definitions (modular task instructions)
│       └── <skill-name>/           # Individual skill package folder
│           └── SKILL.md            # Skill instructions and metadata
└── workflows/                      # Custom workflow automation templates / slash commands

~/.gemini/config/                   # User-level (global) configuration - applies across all workspaces
├── config.json                     # General platform settings (model selection, execution policies, etc.)
├── mcp_config.json                 # Global Model Context Protocol (MCP) server configurations
├── hooks.json                      # Global automation hooks (event -> action)
├── projects/                       # Workspace projects registry
│   └── <project-id>.json           # Pre-project settings and permission policies
├── plugins/                        # Bundles of custom skills, subagents, and configurations
│   └── <plugin-name>/
│       ├── plugin.json             # Plugin metadata (name, version, description)
│       ├── skills/                 # Plugin-provided skills
│       └── agents/                 # Plugin-provided subagents
├── skills/                         # Global skill definitions (.md files/directories)
├── agents/                         # Global agent definitions (.md files/directories)
└── sidecars/                       # Sidecar extensions configuration

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

### Workspace Skills (`.agents/skills/<skill-name>/SKILL.md`)

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
