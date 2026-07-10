---
name: ticket-committer
description: Automates the commit process across multiple worktrees for given ticket IDs, following the /commit skill rules.
---
# Ticket Committer

Use this skill to automate committing changes after implementing or fixing bugs across multiple repositories or worktrees based on one or more ticket IDs. 

## When to Use
- The user provides one or multiple ticket IDs and wants to commit their changes.
- You need to perform batch commits across different worktrees belonging to specific tickets.
- The workflow follows a prior implementation step (like `/ticket-implementer`).

## Workflow

1. **Parse Input Ticket IDs**
   - Identify all ticket IDs provided by the user in the prompt.

2. **Locate Target Worktrees**
   - For each ticket ID, look inside the `backend/` and `frontend/` directories to find the corresponding worktrees.
   - Search within these specific folders to find the paths of the worktrees matching the ticket ID (ví dụ: worktree có chứa ID ticket trong tên nhánh/thư mục).
   - Đảm bảo quét và xử lý cả `backend/` và `frontend/` cho mỗi ticket ID nếu có worktree tồn tại ở cả hai nơi.

3. **Iterate and Commit**
   - For each located worktree, perform the following:
     1. Change the working directory (`Cwd`) to the worktree path.
     2. Review the `git status` to identify modified files.
     3. Apply the strict rules from the `/commit` skill (Sentry Commit Messages format):
        - Check the current branch to ensure it's not `main` or `master`.
        - Determine the appropriate commit type (`feat`, `fix`, `chore`, etc.).
        - Write a commit message following the conventional commit structure.
        - Reference the ticket ID in the footer (e.g., `Fixes GH-<ticket_id>` or `Fixes #<ticket_id>`).
        - Run `git add` and `git commit`.

4. **Summarize**
   - After processing all worktrees for all ticket IDs, provide a comprehensive summary to the user.
   - List the worktrees successfully committed, the commit messages used, and any worktrees that were skipped (e.g., due to no changes).

## Limitations
- This skill requires the `/commit` skill guidelines to be strictly followed (e.g., branch safety checks, Co-Authored-By footers).
- Do not commit to `main` or `master` directly.
- Stop and ask for clarification if the worktree paths cannot be determined or if there are unexpected conflicts.
