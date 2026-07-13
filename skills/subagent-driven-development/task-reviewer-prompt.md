# Whole Pull Request Reviewer Prompt Template

This file retains its historical path for compatibility. It is a PR-level reviewer, not a task reviewer.

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

    Do not broaden into a repository-wide audit without naming the concrete
    risk. Do not rerun tests already reported for unchanged code; run a
    focused test only for a specific doubt.

    ### Assessment
    **Ready for PR feedback loop:** [Yes | No]
    **Reasoning:** [technical rationale]
```
