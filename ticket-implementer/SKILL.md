---
name: ticket-implementer
description: "Read, plan, and implement tasks or bugs from one or more ticket IDs (e.g. TAD-xxx, TAD-yyy). The workflow processes multiple tickets concurrently, utilizing git worktrees for isolated environments, with tasks orchestrated by delegating to subagents managed by the multi-agent-task-orchestrator skill."
---

# Ticket Implementer

Use this skill to autonomously fetch ticket details, plan the implementation, and execute the task or bug fix within the repository.

## When to Use
Use this skill when you are given one or more ticket IDs (e.g. `TAD-xxx`, `TAD-yyy`) and need to completely implement the requirements (tasks, improvements, or bug fixes) concurrently.

## Limitations
- This skill requires the project slug (e.g. `TAD`) and ticket ID(s) to be provided.
- Ensure you have the necessary subagents available for codebase exploration.
- Requires other specialized skills (like `plane ticket reader`, `bug-hunter`) to delegate parts of the workflow.

## Alternative Workflow: /teamwork-preview
If the `/teamwork-preview` slash command is available and the user wishes to use a fully autonomous multi-agent system, you should follow the `/teamwork-preview` documentation instead of the steps below. This involves its two-phase artifact-based workflow (crafting `prompt_draft.md` through Steps 1-9 and then delegating to the `teamwork_preview` subagent).


## Inputs
- Ticket ID(s) (e.g., `TAD-xxx`, `TAD-yyy`)
- Project slug (e.g., `TAD` can be extracted from the ticket IDs)

## Workflow Steps

**Note: If multiple ticket IDs are provided, orchestrate the following workflow steps concurrently for each ticket to increase parallel workload throughput. EVERY task in this workflow MUST be delegated to subagents and managed centrally using the `multi-agent-task-orchestrator` skill.**

### Step 1: Fetch Ticket Details
1. Use the `multi-agent-task-orchestrator` skill to delegate the ticket setup phase to a subagent.
2. The subagent must invoke the `plane ticket reader` skill using the provided ticket ID to fetch the ticket description, title, and metadata.
3. The subagent must then create a new folder named after the ticket ID (e.g., `tasks/TAD-123`).
4. Inside the new folder, the subagent must create `req.txt` and copy the fetched ticket description, title, and metadata into it.
5. Wait for the subagent to report that setup is complete. Do NOT let it create any planning files yet.

### Step 2: Explore Codebase and Plan
1. Enable the `/planning` mode before proceeding with this step.
2. Use the `multi-agent-task-orchestrator` skill to delegate the codebase exploration phase to **two separate subagents**:
   - One subagent focused on exploring the **backend** repository/codebase.
   - One subagent focused on exploring the **frontend** repository/codebase.
3. Both subagents must read `req.txt` and explore their respective areas to gather context and findings.
4. Wait for both subagents to return their exploration findings to the orchestrator.
5. Use the `multi-agent-task-orchestrator` to delegate the planning phase to a new **planning subagent** equipped with the `planning-with-files` skill.
6. The planning subagent must consolidate the findings and formulate a unified implementation plan. Ensure it creates the 3 required planning files (`task_plan.md`, `findings.md`, and `progress.md`) strictly inside the `tasks/[ticket-id]/` directory.
7. Present the finalized plans to the user for review.
   - **CRITICAL**: You MUST loop this review process until the user explicitly approves the plan.
   - If the user provides feedback or does not approve, read the feedback and restart Step 2 (specifically, the planning phase) incorporating the user's feedback. Do NOT proceed to Step 3.
   - Only when the user explicitly approves (e.g., "OK", "approved"), proceed to Step 3.

### Step 3: Implement Task
1. Check the ticket item type from `req.txt`.
2. Before implementing, instruct a subagent to set up isolated environments using **git worktree** in both the **backend** and **frontend** repositories based on the latest `origin/develop`:
   - If the type is a bug, use the branch name `fix/{ticket-id}`.
   - Otherwise, use the branch name `feat/{ticket-id}`.
   - Create a new git worktree for the task (e.g., `git worktree add ../{repo-name}-{ticket-id} -b {branch-name} origin/develop`).
   - Perform all subsequent implementation work strictly within these isolated worktree directories to prevent conflicts when running multiple tasks simultaneously.
   - **Note**: If the worktree or branch already exists, instruct the subagent to navigate to it, stash any current changes, switch to the existing branch, and rebase it with the latest `origin/develop`.
3. Determine if the plan requires changes in the backend, frontend, or both.
4. Use the `multi-agent-task-orchestrator` skill to delegate and manage the implementation work defined in `tasks/[ticket-id]/task_plan.md` within the newly created worktree directories.
   - **CRITICAL FOR SPEED**: If the plan involves both backend and frontend changes, instruct the orchestrator to launch **two separate subagents concurrently** (one for the backend and one for the frontend) so they can implement their respective parts in parallel.
5. For bug fixes, orchestrate subagents equipped with the `/bug-hunter` skill. For features or improvements, orchestrate subagents equipped with the `full-stack-orchestration-full-stack-feature` skill.
6. Ensure the orchestrator continuously monitors the parallel subagents' progress, handling any cross-dependencies, and has them update `tasks/[ticket-id]/progress.md` and `task_plan.md` until all conditions are matched and the task is fully complete.

### Step 4: Lint, Validate, and Review
1. **Trigger Immediately**: Do not wait for both backend and frontend implementations to finish. As soon as one part (backend or frontend) completes its implementation (Step 3), immediately use the `multi-agent-task-orchestrator` skill to delegate validation for that specific part to a subagent equipped with the `/lint-and-validate` skill.
2. This subagent must ensure the changes are free of syntax errors, formatting issues, and test failures.
3. After validation passes for that part, use the `multi-agent-task-orchestrator` skill to delegate code review to a subagent equipped with the `/code-reviewer` skill.
4. The orchestrator must ensure any issues or feedback reported by the validation and review subagents are resolved. If significant changes are made, the subagents should update the planning files accordingly.

## Output Contract
Return:
1. Summary of the ticket (Title, Type).
2. Files changed or created (including `req.txt`, `task_plan.md`, `findings.md`, and `progress.md`).
3. Validation performed (how it was tested or verified).
4. Open gaps or remaining work if not fully complete.
