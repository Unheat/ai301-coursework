# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | Repro report's environment line/section compared against issue context and repo facts | Explicitly records the tool version, operating system/platform, and commit or build identifier. If the tested version differs from the issue, the version delta is explicitly acknowledged. Omitting the environment record entirely fails. | required |
| steps-followable | Repro report's steps to reproduce | Steps start from a clean state (such as a fresh clone, isolated environment, or clear minimal input) and provide complete, runnable commands that a stranger can follow without unstated flags, missing drivers, or unshared private configuration. | required |
| behavior-matches | Output artifacts (terminal output, logs, exit code, diffs) in the repro report compared against the issue's reported failure and expected vs actual behavior | The artifact demonstrates the exact failure mode reported in the issue (same error type, exit code, panic, or unexpected output), rather than an adjacent syntax/config error or unverified assumption. Mere assertion of reproduction or root cause without supporting artifacts fails. An evidenced cannot-reproduce passes if it documents exact steps, shows clean output, and explains differences or control runs. | required |
| claim-specific | The candidate claim comment | Names the specific version and behavior being investigated, states a concrete, modest next action (e.g., investigating, submitting a repro report, or testing a patch), and avoids empty "+1 / assign me" boilerplate or promised completion timelines (e.g., "will fix by tomorrow"). | required |
| conventions-respected | The repo-facts block (contribution policy and AI policy) compared against the candidate claim and repro comments | If the repository's stated contribution or AI policy explicitly requires disclosing AI assistance, the comments include an explicit disclosure statement. If no policy mandates disclosure, or policy permits AI without mandatory disclosure, the check passes. | required |

## Verdict rule

Accept if every required check passes. Reject if any required check fails or is unclear. Preferred checks (if any) do not affect the verdict.
