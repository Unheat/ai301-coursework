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
| Issue still open and unfixed | Issue state and linked-PR state in repo-facts | Pass if the issue state is open and no merged linked PR declares it fixes/closes the issue. Fail if the issue is closed (including "closed as completed"), or if a merged PR explicitly fixes it. A partial merged PR that leaves remaining work in the thread does not fail this check | required |
| scope-feasible | Issue body: the work it asks for | The issue describes one identifiable piece of work someone could start: a behavior, a gap, or a task you can point at. It fails only when there is no single piece of work in it: a codebase-wide checklist, an "improve X everywhere", or a container issue tracking many sub-tasks. An open design edge, an "etc.", or several files involved never fails this check; that raises the difficulty note instead. | required |
| Nobody already working on it | Assignees, linked PRs, and claim comments | Pass if there is no current assignee, no open linked PR, and no active claim in the comments | required |
| AI contribution policy | Contribution-policy field in repo-facts | Pass unless the repository explicitly bans AI-generated or AI-assisted contributions | required |
| Good-first-issue signal | Issue labels | Pass if the issue has a good-first-issue/beginner-friendly label | preferred |
| No abandoned prior attempts | Linked PRs (open or closed) in repo-facts | Pass if there are fewer than 2 closed linked PRs. Fail if there are 2 or more, since that signals the issue has already defeated multiple contributors | required |

## Difficulty note

Every accepted candidate gets a one-line difficulty note in the read-out
summary, so the chooser knows what they are taking on before they claim it.
This is information, never a verdict input: it does not appear in the fenced
JSON block and it never changes accept or reject. In eval mode it is skipped.

- **straightforward**: the issue names where the change lives and the change is bounded.
- **moderate**: the location is yours to find, or several files are involved.
- **ambitious**: an open design edge, or an unfamiliar subsystem; name the edge,
  because claiming the issue means raising it with the maintainer early.

## Verdict rule

Accept only if every required check passes.
If a required check is unclear, treat it as fail.
Preferred checks do not change accept/reject; they only help rank accepted issues.
The difficulty note is reported alongside an accepted verdict and is not a check.
