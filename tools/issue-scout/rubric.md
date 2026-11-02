# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer activity | Last 5 default-branch commit dates in repo-facts | Pass if at least one non-bot commit occurred within 90 days of the snapshot date | required |
| Repo not archived | `archived:` field in repo-facts | Pass if archived is false | required |
| Issue scope | Issue body and comment thread | Pass if the issue names one specific problem to fix, even if the maintainer lists several possible causes or candidate implementation approaches for that one problem. Fail if it is an umbrella/tracking issue covering multiple unrelated problems, a request whose fundamental direction is still being debated (nobody has settled on whether/how to do it), or a pure usage/support question | required |
| Nobody already working on it | Assignees, linked PRs, and claim comments | Pass if there is no current assignee, no open linked PR, and no active claim in the comments | required |
| AI contribution policy | Contribution-policy field in repo-facts | Pass unless the repository explicitly bans AI-generated or AI-assisted contributions | required |
| Good-first-issue signal | Issue labels | Pass if the issue has a good-first-issue/beginner-friendly label | preferred |
| No abandoned prior attempts | Linked PRs (open or closed) in repo-facts | Pass if there are fewer than 2 closed linked PRs. Fail if there are 2 or more, since that signals the issue has already defeated multiple contributors | required |

## Verdict rule

Accept only if every required check passes.
If a required check is unclear, treat it as fail.
Preferred checks do not change accept/reject; they only help rank accepted issues.
