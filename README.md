# Custom Skills

This repository contains specialized Antigravity AI skills customized for your project workflow.

> **Note**: The skills in this repository are sourced and adapted from the [antigravity-awesome-skills CATALOG](https://github.com/sickn33/antigravity-awesome-skills/blob/main/CATALOG.md).

## Key Features

- **Plane Ticket Reader** (`plane-ticket-reader`): Read and search Plane.so projects and work items (tickets) from a self-hosted Plane instance using the `plane` CLI.
- **Ticket Implementer** (`ticket-implementer`): Read, plan, and implement tasks or bugs from one or more ticket IDs (e.g. PROJECT-123) concurrently using git worktrees and multi-agent orchestration.

## Tech Stack

- **Framework**: Antigravity Skills System
- **Scripts**: Bash

## Prerequisites

- Antigravity CLI installed.
- `plane` CLI installed (for `plane-ticket-reader`).
- Access to a self-hosted Plane.so instance.
- Git (for `ticket-implementer` git worktrees).

## Getting Started

### 1. Installation

There are multiple ways to load these skills into your Antigravity agent, depending on your workflow:

**Option A: Project-level Setup (Recommended)**
The most common and seamless way is to place the skills directly into your project's `.agents/skills` directory. The agent will automatically discover and load them whenever you work in this repository.

```bash
# Assuming you are in your project root
mkdir -p .agents/skills
cp -r path/to/this-repo/skills/* .agents/skills/
```

**Option B: Global Installation**
If you want these skills available across all your projects, copy them to your global Antigravity configuration directory:

```bash
cp -r path/to/this-repo/skills/* ~/.gemini/antigravity-cli/skills/
```

**Option C: Manual / Dynamic Loading**
You can also dynamically load a specific skill into your active session using the `/skill` command:

```text
> /skill load path/to/this-repo/skills/plane-ticket-reader
```

### 2. Environment Setup

For the `plane-ticket-reader` skill, you need to configure your Plane credentials. Ensure you have these environment variables available in your `.env` or exported in your shell:

| Variable          | Description                      | Example                        |
| ----------------- | -------------------------------- | ------------------------------ |
| `PLANE_API_KEY`   | Your Plane Personal Access Token | `plane_api_key_xxx`            |
| `PLANE_WORKSPACE` | Workspace slug                   | `my-workspace`                 |
| `PLANE_BASE_URL`  | Self-hosted Plane instance URL   | `https://plane.yourdomain.com` |

Export them in your terminal:

```bash
export PLANE_API_KEY="your-api-key"
export PLANE_WORKSPACE="your-workspace-slug"
export PLANE_BASE_URL="https://your-self-hosted-plane.com"
```

## Architecture

### Directory Structure

```
skills/
├── plane-ticket-reader/
│   ├── SKILL.md       # Skill definition and instructions
│   └── scripts/       # Helper scripts like the `plane` CLI wrapper
└── ticket-implementer/
    └── SKILL.md       # Workflow orchestration definition
```

### Key Components

**Plane Ticket Reader**

- Focuses on read-only operations to fetch project details, search issues, and read comments.
- Outputs can be formatted as tables or raw JSON for programmatic parsing.

**Ticket Implementer**

- A complex orchestration skill that manages the end-to-end implementation lifecycle.
- Interacts with `multi-agent-task-orchestrator` to delegate tasks to backend and frontend subagents.
- Uses `git worktree` to isolate parallel feature/bug implementations without disrupting your main branch.

## Environment Variables

### Required for Plane Ticket Reader

| Variable          | Description                      | How to Get                                        |
| ----------------- | -------------------------------- | ------------------------------------------------- |
| `PLANE_API_KEY`   | Plane Personal Access Token      | Plane → Profile Settings → Personal Access Tokens |
| `PLANE_WORKSPACE` | Workspace slug                   | The URL path segment (e.g., `my-team`)            |
| `PLANE_BASE_URL`  | Root URL of self-hosted instance | The base URL of your self-hosted Plane instance   |

## Available Scripts

Here are some of the available scripts provided by the `plane` CLI wrapper in the `plane-ticket-reader` skill:

| Command                              | Description                          |
| ------------------------------------ | ------------------------------------ |
| `plane projects list`                | List all projects to find PROJECT_ID |
| `plane issues list -p ID`            | List all work items in a project     |
| `plane issues fast-get ID`           | Retrieve a ticket by its sequence ID |
| `plane comments list -p ID -i ISSUE` | List comments on a work item         |
| `plane me`                           | Show your authenticated user info    |

## Testing

The skills are tested by invoking them through the Antigravity CLI and ensuring the agent correctly parses the `SKILL.md` instructions and executes the commands.

## Troubleshooting

### Plane Ticket Reader Issues

**Error:** `plane command not found`

**Solution:**
Ensure the `scripts` directory of the `plane-ticket-reader` skill is added to your PATH.

```bash
export PATH="/path/to/skills/plane-ticket-reader/scripts:$PATH"
```

**Error:** Authentication failure or API errors

**Solution:**
Verify that `PLANE_API_KEY`, `PLANE_WORKSPACE`, and `PLANE_BASE_URL` are correctly set in your environment and that your self-hosted instance is reachable.

## Usage Examples

Once installed and configured, you can interact with the agent using these skills:

```text
# For Ticket Implementer
> " /ticket-implementer TAD-1816;TAD-1815"

# For Plane Ticket Reader
> "Use the plane-ticket-reader to find any bug tickets assigned to me in the PROJECT workspace."
> "Fetch the details for ticket PROJECT-123 and list its comments."


```

## Creating Custom Skills

You can extend this repository by adding your own skills.

1. **Create a Directory**: Make a new folder in `skills/` (e.g., `skills/my-new-skill/`).
2. **Add a SKILL.md**: This is the required instruction file. It must contain YAML frontmatter (name, description, etc.) and detailed markdown instructions for the agent.
3. **Include Scripts (Optional)**: If your skill needs custom CLI tools, place them in a `scripts/` subdirectory and document how they should be added to the `$PATH`.

Example `SKILL.md` frontmatter:

```yaml
---
name: my-new-skill
description: "A brief description of what this skill does and when to invoke it."
---
# Instructions
...
```

## Skill Best Practices

- **Keep it Focused**: A skill should do one thing well. If it becomes too complex, consider breaking it into multiple skills.
- **Use Orchestrators**: For complex workflows that modify code (like `ticket-implementer`), rely on orchestrator patterns to spawn parallel subagents rather than trying to do everything sequentially in a single context.
- **Read-Only vs Read-Write**: Be explicit about whether your skill reads data (safe) or writes/modifies data (requires care).

## Contributing

Contributions are always welcome! If you have any ideas, bug fixes, or entirely new skills that could benefit this repository, please feel free to contribute. You can open an issue to discuss your ideas or submit a pull request directly.
