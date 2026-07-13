# PR-Oriented Superplex Workflow Test Plan

**Implementation plan:** `docs/superplex/plans/2026-07-12-pr-oriented-superplex-workflow.md`

## Goal

Verify that Superplex plans and executes dependency-ordered pull requests, creates traceable test coverage, delegates review feedback to subagents, and refuses to merge until the current PR satisfies the complete remote gate.

## Requirement-to-Test Matrix

| Requirement | Verification |
|---|---|
| Plans produce a PR map | Pressure scenario: cross-layer feature must yield dependency-ordered PR entries before tasks |
| PRs carry durable context | Inspect generated plan and PR body requirements for spec, implementation-plan, and test-plan paths |
| Non-trivial changes get a test plan | Pressure scenario with multiple flows, database work, and deferred E2E |
| Tasks are not review gates | SDD scenario with multiple tasks inside one PR; exactly one whole-PR review is required |
| Reviews cover spec, quality, and tests | Reviewer prompt contains three independent verdicts and file/line evidence requirements |
| Valid feedback is implemented by a subagent | Simulated actionable inline comment; feedback agent changes code, adds tests, pushes, replies, and re-reviews |
| Invalid feedback is answered | Simulated out-of-scope comment; feedback agent replies with technical reasoning and makes no code change |
| Threads are resolved | Feedback scenario verifies thread resolution occurs only after a fix or answer |
| New feedback is not missed | Add a comment after a push; next sweep must process its unique ID |
| Eyes reaction blocks merge | PR state includes eyes reaction; merge gate remains blocked |
| Stale thumbs-up is rejected | Push a new head after thumbs-up; sign-off must be renewed for the new SHA |
| Failed checks block merge | Required check fails; feedback agent investigates and does not merge |
| Fresh green state permits squash merge | Current-head thumbs-up, no eyes, no pending review, resolved threads, no new feedback, and green checks trigger squash merge |
| Cloud provisioning exception works | Add a required Vercel/GitHub/Trigger.dev secret; agent stops with a precise provisioning request |
| Fourplex test traceability is preserved | Test plan maps cases to `tests/manifest/`, `pnpm test:cases:validate`, generated docs, and the final E2E PR |

## Fourplex Test Layers

- Unit/component: Vitest package and app tests.
- Integration/API/database: Vitest integration tests and Supabase SQL tests.
- E2E: Playwright under `apps/web/e2e/`, with fixtures, page objects, and Allure IDs.
- Test-case tracking: `tests/manifest/` is authoritative; `Docs/test-cases/` is generated.
- Verification: use repository-native commands such as `pnpm verify`, `pnpm db:test`, `pnpm test:cases:validate`, and targeted smoke/release-gate commands.

No numeric coverage threshold is introduced. Coverage is behavioral and traceability-based because Fourplex currently has no coverage instrumentation configuration.

## RED Baseline

Before the skill changes, run subagents against the current skills and record the failures:

- Writing plans produces tasks and commits, not PR boundaries.
- Reviewers operate per task and lack a PR feedback lifecycle.
- Invalid feedback has no disposition path.
- Reactions, stale approvals, failed checks, and unresolved threads are not merge gates.
- Fourplex stops at manual Human Review and lacks required plan links.

## GREEN Verification

Re-run the same pressure scenarios after the changes. The updated skills must explicitly produce the required artifacts, delegate feedback fixes, record durable state, process each new comment once, and apply all reaction/check/thread gates before squash-merge.

## REFACTOR Checks

Search the updated skills for these loopholes and close any that remain:

- “Review after each task.”
- “Ready to merge” without current remote evidence.
- Merge while eyes reaction remains.
- Treat any thumbs-up as valid after a new push.
- Reply without resolving the thread.
- Ignore top-level comments or review-state APIs.
- Create an E2E-only plan without unit/integration coverage.
- Hand-edit generated Fourplex test-case documentation.
