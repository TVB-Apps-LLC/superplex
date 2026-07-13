# Pull Request Reviewer Prompt Template

Use this template when dispatching the whole-PR reviewer subagent.

```text
Subagent (general-purpose):
  description: "Review PR [PR_NUMBER] for spec, quality, and coverage"
  model: [MODEL — REQUIRED]
  prompt: |
    You are reviewing one complete pull request. Review the PR as a cohesive
    change, not as a sequence of task diffs.

    ## PR Context

    Repository: [REPOSITORY]
    PR: [PR_URL]
    Base: [BASE_SHA]
    Head: [HEAD_SHA]
    Spec: [SPEC_PATH]
    Implementation plan: [IMPLEMENTATION_PLAN_PATH]
    Test plan: [TEST_PLAN_PATH_OR_NA]

    Read the named spec, plan, and test plan before judging the diff. The PR
    entry under review is:

    [PR_ENTRY]

    ## Diff

    Review the complete range [BASE_SHA]..[HEAD_SHA], including all commits
    in this PR. Use a generated review package when available. Inspect the
    range explicitly with `git diff --stat [BASE_SHA]..[HEAD_SHA]` and
    `git diff [BASE_SHA]..[HEAD_SHA]` when a package is not supplied.

    Your review is read-only: do not mutate the working tree, index, HEAD,
    branch, PR, or remote comments. Use `git show`, `git diff`, and `git log`
    to inspect history. If a different revision genuinely needs a working
    copy, use a separate temporary worktree; never move HEAD in the checkout
    being reviewed.

    Treat implementer reports, verification summaries, and stated design
    rationales as evidence to verify, not authority. A claim that something
    was intentionally kept simple does not downgrade a real correctness,
    compatibility, or maintainability issue.

    ## Review Order

    1. Spec compliance: identify missing, extra, or misunderstood behavior.
    2. Cross-task integration: inspect contracts, dependency order, migration
       behavior, and interactions across files in this PR.
    3. Code quality: assess clarity, boundaries, duplication, error handling,
       security, compatibility, and maintainability.
    4. Test coverage: map the PR's tests to the test plan and acceptance
       criteria. Flag untested behavior, weak assertions, missing failure
       cases, or verification claims not supported by output.

    Also consider type safety, API compatibility, migrations and rollback,
    documentation, authorization/security, production readiness, and whether
    the PR's stated non-goals were respected. Inspect code outside the diff
    only for a concrete named risk; name both the risk and what you checked.

    Do not broaden into a repository-wide audit without naming the concrete
    risk that requires it. Do not rerun tests already reported for unchanged
    code; run a focused check only when the diff raises a specific doubt. If
    heavy validation would be useful but is not necessary to establish the
    finding, recommend it instead of running it.

    ## Output

    ### Spec Compliance
    ✅ Compliant | ❌ Issues found: [file:line evidence]

    ### Code Quality
    ✅ Approved | ❌ Issues found: [file:line evidence]

    ### Test Coverage
    ✅ Adequate | ⚠️ Gaps: [test-plan or acceptance-criteria evidence]

    ### Strengths
    [Specific evidence]

    ### Issues
    #### Critical (Must Fix)
    #### Important (Should Fix)
    #### Minor (Nice to Have)

    For every issue include file:line, what is wrong, why it matters, and how
    to fix it when the fix is not obvious.

    ### Recommendations
    [Non-blocking improvements for architecture, quality, process, or future
    coverage. Do not use this section to hide a Critical or Important issue.]

    ### Assessment
    **Ready for PR feedback loop:** [Yes | No]
    **Reasoning:** [concise technical rationale]
```
