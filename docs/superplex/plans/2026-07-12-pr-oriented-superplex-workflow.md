# PR-Oriented Superplex Workflow Implementation Plan

> **For agentic workers:** Use `superplex:subagent-driven-development` to execute the PRs sequentially. Review the complete PR, not individual tasks.

**Goal:** Make Superplex plan work as dependency-ordered pull requests with traceable testing and a persistent review-feedback merge loop.

**Spec:** User-approved PR-Oriented Superplex Workflow plan in the task request.

**Test plan:** `docs/superplex/plans/2026-07-12-pr-oriented-superplex-workflow-test-plan.md`

## Global Constraints

- PRs are sequential and independently testable by default.
- Every PR links to the spec, implementation plan, and test plan.
- Tasks inside a PR are not review gates.
- E2E may be deferred to a final dedicated PR.
- Merge requires current-head sign-off, no eyes reaction, resolved feedback, and passing checks.
- Branches and worktrees remain available through merge, post-merge validation, and all dependent/final PRs; cleanup is a separate finalization step.
- New cloud secrets or variables block automatic merge until provisioned.

## PR Map

### PR 1: Planning and test-plan contracts

Add the dependency-ordered PR map, non-goals, PR metadata, and optional companion test-plan requirements to `writing-plans`. Add the Fourplex test-manifest and deferred-E2E guidance.

### PR 2: PR-level review lifecycle

Change execution and review skills from task-level review to whole-PR review. Add the PR reviewer contract, durable PR ledger, persistent feedback subagent, reaction/check/thread gates, stale-signoff handling, and automatic squash-merge policy.

### PR 3: Fourplex workflow alignment

Require planning links in the PR template and update Fourplex’s workflow to execute sequential PRs, handle PR feedback continuously, defer E2E when planned, and squash-merge after the complete gate.

Each PR entry must carry the spec, implementation-plan, and test-plan links, exact verification commands, owned paths, non-goals, and rollback notes.
