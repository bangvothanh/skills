---
name: plane-ticket-reader
description: "Read and search Plane.so projects and work items (tickets) from a self-hosted Plane instance using the `plane` CLI. List projects, search issues, get issue details, and read comments."
metadata:
  requires:
    bins: ["plane"]
    env: ["PLANE_API_KEY", "PLANE_WORKSPACE", "PLANE_BASE_URL"]
  primaryEnv: "PLANE_API_KEY"
  emoji: "🎫"
  permissions:
    - command: "plane"
---

# Plane Ticket Reader Skill

Read and search tickets from your self-hosted [Plane](https://plane.so) project management instance via the `plane` CLI.

## Setup

Since you are using a self-hosted instance, you must configure the base URL alongside your workspace and API key.

The `plane` script automatically reads from a `.env` file in your current directory or workspace root if one is present. You can define your variables there:

```env
PLANE_API_KEY="your-api-key"
PLANE_WORKSPACE="your-workspace-slug"
PLANE_BASE_URL="https://your-self-hosted-plane.com" # Required for self-hosted
```

Alternatively, you can export them directly:

```bash
export PLANE_API_KEY="your-api-key"
export PLANE_WORKSPACE="your-workspace-slug"
export PLANE_BASE_URL="https://your-self-hosted-plane.com" # Required for self-hosted
```

- **API Key**: Get your API key from **Plane → Profile Settings → Personal Access Tokens**.
- **Workspace**: The workspace slug is the URL path segment (e.g., `my-team`).
- **Base URL**: The root URL of your self-hosted Plane instance.

Ensure the bundled `plane` script is in your PATH before using it, or run it directly from the skill's `scripts/` directory:

```bash
# Add to PATH for the current session
export PATH="/home/bangvt/projects/tad/.agents/skills/plane-ticket-reader/scripts:$PATH"
```

## Commands (Read-Only Focus)

This skill focuses on reading and searching tickets.

### Finding Projects

```bash
plane projects list                                      # List all projects to find PROJECT_ID
plane projects get PROJECT_ID                            # Get project details
```

### Searching and Reading Tickets (Issues)

```bash
# List all work items in a project
plane issues list -p PROJECT_ID

# Filter tickets
plane issues list -p PROJECT_ID --priority high --assignee USER_ID

# Get full details of a specific ticket
plane issues get -p PROJECT_ID ISSUE_ID

# Search for tickets across the workspace
plane issues search "login bug"
```

### Reading Comments

```bash
plane comments list -p PROJECT_ID -i ISSUE_ID            # List comments on a work item
plane comments list -p PROJECT_ID -i ISSUE_ID --all      # Show all activity and comments
```

### Reference Data (Users, States, Labels)

```bash
plane me                      # Show your authenticated user info
plane members                 # List all workspace members (find USER_IDs)
plane states -p PROJECT_ID    # List workflow states
plane labels -p PROJECT_ID    # List labels
```

## Output Formats

Default output is a formatted table. Add `-f json` for raw JSON if you need to parse the output programmatically:

```bash
plane projects list -f json
plane issues get -p PROJECT_ID ISSUE_ID -f json
```
