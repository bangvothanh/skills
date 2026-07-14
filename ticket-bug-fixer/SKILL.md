---
name: ticket-bug-fixer
description: Fix bugs for tasks previously implemented by ticket-implementer, utilizing the original task requirements from the @tasks folder and the bug-hunter skill.
---

# Ticket Bug Fixer Skill

Use this skill when tackling bugs, defects, or issues that arise *after* a task has been implemented (e.g., by the `ticket-implementer` skill). 

## Workflow

### 1. Identify Context and Requirements
1. **Target Task**: Identify the original task/ticket ID that the bug relates to (e.g., `TAD-1234`).
2. **Bug Details**: Review the bug description or the specific bug ticket ID provided by the user.
3. **Read Task Context**: Navigate to the task's documentation folder at `/home/bangvt/projects/tad/tasks/<task-id>`. Read the files within this folder to understand the original requirements, implementation plan, and context.
4. **Fallback for Missing Context**: If the task requirements do not exist in the `tasks` folder, invoke the `plane-ticket-reader` skill (read its instructions via `view_file` on its `SKILL.md` if necessary) to fetch the task description and start exploring and investigating the root cause of the bug.

### 2. Isolate the Codebase
1. Before hunting or implementing the fix, create a new git worktree to isolate the bug fix code. 
2. Note that `backend` and `frontend` are separate Git repositories. Create the worktree inside the respective repository's `.worktrees` folder (e.g., `backend/.worktrees/<ticket-id>` or `frontend/.worktrees/<ticket-id>`). (If you need guidance, invoke the `using-git-worktrees` skill).
3. This ensures that any debugging or experimental changes do not affect ongoing work and provides a clean environment for reproducing the issue.

### 3. Hunt for the Bug
1. Invoke the `bug-hunter` skill (read its instructions via `view_file` on its `SKILL.md` if necessary).
2. Use the context gathered from the task folder to reproduce or trace the bug in the codebase. Cross-reference the bug symptoms with the expected behavior defined in the original requirements.
3. Systematically identify the root cause.

### 4. Implement the Fix
1. Formulate a fix that resolves the issue.
2. Ensure the fix does not break or alter the expected behavior outlined in the original task requirements.
3. Apply the code changes.
4. **CRITICAL**: Do NOT commit any code changes (e.g., using `git commit`) unless the user explicitly requests it. Provide the fix in the working directory and wait for user validation. If the user requests to commit, invoke the `ticket-committer` skill to handle the commit process.

### 5. Verification
1. Verify the changes locally if possible, or explain the fix and ask the user to verify.
2. Confirm the issue is resolved and all original task requirements remain intact.

## When to Use
Invoke this skill when the user explicitly requests to fix a bug for an already implemented task, or when a bug report is provided referencing a completed implementation.
