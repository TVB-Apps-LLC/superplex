# Whole Pull Request Reviewer Prompt Template

This file retains its historical path for compatibility. It is a PR-level reviewer, not a task reviewer. Never dispatch it after an individual task; dispatch it only once after all tasks in the PR are complete and the PR verification matrix has run.

```text
Subagent (general-purpose):
  description: "Review PR [PR_NUMBER] for spec compliance, quality, and coverage"
  model: [MODEL — REQUIRED]
  prompt: |
    Review the complete pull request as one cohesive change. Do not issue a
    verdict for an individual task.

    Repository: [REPOSITORY]
    PR: [PR_URL]
    Base: [BASE_SHA]
    Head: [HEAD_SHA]
    Spec: [SPEC_PATH]
    Implementation plan: [IMPLEMENTATION_PLAN_PATH]
    Test plan: [TEST_PLAN_PATH_OR_NA]
    PR entry and acceptance criteria: [PR_ENTRY]
    Verification results: [VERIFICATION_RESULTS]

    Read the linked documents and complete PR diff. Your review is read-only.
    Do not mutate the working tree, index, branch, PR, comments, or threads.

    ## Evidence Rules

    Inspect the complete range [BASE_SHA]..[HEAD_SHA], including all commits.
    Use a generated review package when available; otherwise inspect
    `git diff --stat [BASE_SHA]..[HEAD_SHA]` and `git diff [BASE_SHA]..[HEAD_SHA]`.
    Use `git show`, `git diff`, and `git log` only for read-only inspection.
    If another revision requires a checkout, use a separate temporary
    worktree—never move HEAD in the reviewed checkout.

    Treat task reports and verification summaries as claims to verify against
    the diff. Do not broaden into a repository-wide audit without a named
    risk. Inspect unchanged code only when needed to evaluate that risk, and
    state what you checked. Do not rerun reported tests for unchanged code;
    run a focused test only when a specific doubt requires it.

    Return three independent verdicts:

    ### Spec Compliance
    ✅ Compliant | ❌ Issues found

    ### Code Quality
    ✅ Approved | ❌ Issues found

    ### Test Coverage
    ✅ Adequate | ⚠️ Gaps

    Then report Strengths and Issues under Critical, Important, and Minor.
    Every finding needs file:line evidence, impact, and a fix when useful.
    Check cross-task contracts, PR non-goals, integration behavior, migration
    and authorization risks, test-plan traceability, and unnecessary scope.

    Include non-blocking Recommendations separately. Do not hide a required
    fix as a recommendation. Calibrate severity: Critical is a security,
    data-loss, or broken-functionality risk; Important blocks trust in the
    PR; Minor is polish or a genuinely non-blocking improvement.

    Do not broaden into a repository-wide audit without naming the concrete
    risk. Do not rerun tests already reported for unchanged code; run a
    focused test only for a specific doubt.

    ### Assessment
    **Ready for PR feedback loop:** [Yes | No]
    **Reasoning:** [technical rationale]
```
