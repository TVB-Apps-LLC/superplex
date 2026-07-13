---
name: executing-plans
description: Use when you have a written PR-oriented implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

Load the implementation and test plans, review them critically, and execute the PR map in dependency order. Use `subagent-driven-development` instead when subagents are available in the current session.

**Announce at start:** "I'm using the executing-plans skill to implement this PR-oriented plan."

## Process

### Step 1: Load and Review Plans

1. Read the implementation plan, test plan, approved spec, and repository instructions.
2. Review the PR map for missing prerequisites, broken intermediate states, contradictory constraints, missing test ownership, or missing spec/plan links.
3. If concerns block execution, raise them before starting.
4. If the plan is sound, create progress entries for every PR and its tasks.

### Step 2: Execute One PR at a Time

For each PR in dependency order:

1. Set up the required isolated workspace and branch.
2. Execute its tasks in order, marking each task in progress and complete.
3. Run the PR's test matrix and required verification.
4. Open or update the PR with the spec, implementation-plan, and test-plan paths.
5. Use `requesting-code-review` for one whole-PR review covering spec compliance, code quality, and test coverage.
6. Use `receiving-code-review` to delegate valid fixes, answer invalid feedback, resolve threads, monitor reactions and checks, and enforce the merge gate.
7. Use `finishing-a-development-branch` to squash-merge after the gate passes, without deleting the branch or worktree. Preserve both until the complete plan, post-merge validation, and any final E2E/release PRs are done, except when new cloud secrets or variables require provisioning.
8. Start the next dependent PR only after the current PR merges.

Tasks are execution units inside a PR. Do not create a separate review gate after every task.

### Step 3: Final E2E and Release PR

If the test plan defers E2E work, execute the final E2E/release-validation PR after production behavior is feature-complete. Update the test-case manifest, add or update Playwright coverage, regenerate generated documentation, and run the repository's release checks.

Only after the final PR and all post-merge validation pass, record task completion and perform the separate branch/worktree cleanup step. Never treat an individual PR merge as permission to delete either.

## When to Stop

Stop immediately when a required check fails, a plan prerequisite is missing, a review comment is ambiguous, a required GitHub permission is unavailable, or a cloud secret/variable must be provisioned. Record the blocker and ask for direction rather than guessing.

## Integration

- `superplex:using-git-worktrees` — workspace isolation and baseline verification.
- `superplex:writing-plans` — creates the PR map and test plan.
- `superplex:requesting-code-review` — whole-PR review.
- `superplex:receiving-code-review` — persistent PR feedback loop.
- `superplex:finishing-a-development-branch` — merge gate and cleanup.
