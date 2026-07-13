# Restore Skill Context Test Plan

**Implementation plan:** `docs/superplex/plans/2026-07-13-restore-skill-context.md`

## Traceability Matrix

| Requirement | Scenario | Expected result | Owner PR |
| --- | --- | --- | --- |
| No task-level review gates | Multi-task PR execution | Implementers self-review per task; one complete PR reviewer runs after the PR test matrix | PR 1 |
| Detailed implementer orchestration | `DONE_WITH_CONCERNS`, `NEEDS_CONTEXT`, and `BLOCKED` reports | Controller follows the explicit status-specific action rather than continuing blindly | PR 1 |
| Review evidence and calibration | PR reviewer receives a range, reports, linked plans, and a named doubt | Reviewer is read-only, uses focused inspection, verifies claims, and reports three verdicts with file/line evidence | PR 1 |
| Technically rigorous feedback loop | One valid, one invalid, and one ambiguous PR comment | Valid comment is fixed and tested; invalid comment receives reasoned pushback; ambiguous item blocks until clarified | PR 1 |
| Current PR merge policy | Current-head sign-off, no eyes, green checks, resolved threads | Squash merge is allowed without deleting branch/worktree | PR 1 |
| Cleanup safety | PR merged but post-merge validation or a dependent PR remains | Cleanup is forbidden; provenance and harness protections remain explicit | PR 1 |
| Brainstorming contract preserved | Multiple choice, open-ended, approval, and visual offer | Every question includes a recommendation and rationale | PR 1 |

## Static Checks

- `git diff --check`
- `rtk bash tests/shell-lint/test-lint-shell.sh`
- `rtk bash tests/codex/test-package-codex-plugin.sh`
- `rtk bash tests/codex/test-marketplace-manifest.sh`

## Skill Pressure Scenarios

Run fresh subagents against the restored skill text. Record the baseline omissions from the pre-restoration audit and verify that the restored skill produces the expected PR-level behavior without reviving a task-review gate.

## Deferred Work

No runtime application tests or Fourplex test-manifest updates are required; this PR changes Superplex skill behavior and its documentation only.
