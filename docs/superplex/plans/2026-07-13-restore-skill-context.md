# Restore Skill Context Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superplex:subagent-driven-development. Work is reviewed and merged by pull request, not by individual task.

**Goal:** Restore detailed operational guidance removed by the PR-oriented workflow rewrite without reviving policies that the approved PR workflow intentionally replaces.

**Spec:** User-approved restoration request and [PR #1](https://github.com/TVB-Apps-LLC/superplex/pull/1).

**Test plan:** `docs/superplex/plans/2026-07-13-restore-skill-context-test-plan.md`

**Architecture:** Preserve the original skill guidance as additive sections. PR mapping, PR-level review, persistent feedback, automatic squash merge, and delayed cleanup remain the governing workflow; restored text explains how to execute those rules safely.

## Global Constraints

- Restore detailed context rather than replacing skill files wholesale.
- Keep one complete PR review after all tasks in a PR; task self-review remains required, but task-reviewer gates do not return.
- Keep automatic squash merge only after the complete remote gate passes.
- Never delete a branch or worktree until the complete task, post-merge validation, and dependent/final PRs are done.
- Keep the brainstorming recommendation-on-every-question contract unchanged.

## Restoration Matrix

| Surface | Restored with PR adaptation | Intentionally superseded |
| --- | --- | --- |
| `subagent-driven-development` | isolated-context rationale, pre-flight review, model selection, implementer statuses, evidence handoffs, durable progress, controller red flags | task-reviewer gate after every task |
| `receiving-code-review` | technical evaluation, ambiguity handling, source-aware skepticism, YAGNI, reply/pushback discipline, inline-thread mechanics | none |
| `finishing-a-development-branch` | base resolution, PR creation verification, discard confirmation, worktree provenance, safety guidance | manual merge menu, automatic branch/worktree cleanup on PR merge |
| `requesting-code-review` and templates | SHA/range inspection, read-only safety, criteria, evidence, severity calibration, recommendations, workflow integration | task-level ready-to-merge verdict |
| `executing-plans` | plan/task bookkeeping, blockers, revisit rules, subagent routing, branch safety | task-level review checkpoints |

## PR Map

### PR 1: Restore additive skill context

**Purpose:** Reintroduce the operational prompts agents need to execute and review work reliably while retaining the approved PR-centric lifecycle.
**Prerequisites:** PR #1, `feat: make Superplex workflow PR-oriented`.
**Target:** `codex/pr-oriented-superplex-workflow` ← `codex/restore-skill-context`.
**Spec:** User-approved restoration request and PR #1.
**Implementation plan:** This document.
**Test plan:** `docs/superplex/plans/2026-07-13-restore-skill-context-test-plan.md`.
**Owned paths:** `skills/subagent-driven-development/`, `skills/receiving-code-review/`, `skills/finishing-a-development-branch/`, `skills/requesting-code-review/`, `skills/executing-plans/`, and these plan documents.
**Produces:** Detailed, PR-oriented execution, review, and finalization contracts.
**Verification:** Pressure scenarios, `git diff --check`, shell-lint tests, Codex package archive tests, and marketplace-manifest validation.
**Non-goals:** Reinstating task-level reviewer gates, local/manual merge as the normal workflow, or early branch/worktree deletion.
**Risks and rollback:** Excessively restoring old rules could conflict with the PR lifecycle. Keep superseded policies explicit; revert this stacked PR to return to the concise forms.

#### Tasks

1. Add the restoration matrix and behavior scenarios.
2. Extend execution, review, and feedback skills with the missing operational context.
3. Extend reviewer templates with evidence, safety, and calibration guidance.
4. Run pressure scenarios and packaging/static verification.
5. Open a stacked PR with links to PR #1 and both restoration documents.
