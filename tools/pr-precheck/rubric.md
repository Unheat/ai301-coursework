# Rubric: is this pull request ready to submit?

A candidate pull request is evaluated against the plan it claims to implement, the issue that motivated it, and the standards of the repository.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | Diff and commit hunks read against the plan's Scope, Files to touch, Approach, and Deviations | Every file touched and functional change in the diff falls within the plan's stated scope or is explicitly accounted for by a recorded deviation note. Deliberate, honestly disclosed shortfalls recorded in the plan's deviations pass; silent additions, new unrequested flags, or unnoted scope growth fail. | required |
| description-fidelity | PR description (Summary, Changes, Notes) read against the diff and plan | The description accurately reflects the diff and plan. Honestly disclosed shortfalls, deliberately deferred tasks, or out-of-scope notes cited in both plan and description pass. Fail when the description falsely claims changes that do not exist (e.g. claiming docs or tests were updated when absent) or claims exact fidelity while unannounced extra features or hunks are present. | required |
| decisive-testing | Test evidence and Testing section read against the plan's test plan and issue repro | Concrete, observable before-and-after evidence (terminal transcripts, row counts, error transitions, or output diffs) is shown for the specific defect and reproduction scenario. Vague assertions ("tests pass", "verified locally") without any before/after proof fail. | required |
| repo-checks | Test evidence read against repo standards and PR template checks | Standard repository verification checks or test suites (such as unit tests, linter, typecheck) have their execution outcomes reported, noted as passing, or any honest failure/skipping clearly explained. Do not fail packages with decisive before/after repro evidence that state their test suite passed. | required |
| clean-diff | Unified diff and commit history | The diff contains only the purposeful fix and is free of review debris: no debug print statements (console.log, eprintln, print), no dead code or unused functions (allow(dead_code), _unused_), no commented-out code experiments, and no unrelated formatting or import churn. | required |
| template-compliance | PR description and checklists read against the repository's stated PR template asks in repo-facts | The sections, checklists, or entry asks specifically required by the repository's PR template (as stated in repo-facts) are present and completed with substantive content; fail when an ask or checklist stated in the repo's template is visibly omitted, blank, or left as an unedited placeholder. Do not require sections beyond what the repository's stated template specifies. | required |
| ai-disclosure | PR description read against the repository's stated AI policy in repo-facts (and course standards in live mode) | When the repository's stated contribution policy in repo-facts explicitly requires disclosure of AI tools (or in live mode under course standards), the PR description includes an honest disclosure in the contributor's own words describing how AI was used. If the repository states no AI policy in repo-facts, this check passes. | required |

## Verdict rule

A pull request package receives a verdict of `accept` (ready to submit) if and only if every required check receives a grade of `pass`.

If any required check receives a grade of `fail` or `unclear`, the verdict is `reject` (hold). An unverifiable claim or missing evidence is treated as `unclear` and defaults to `fail`. Preferred checks provide advisory feedback and never alter the binary verdict.
