# Issue Scout Scan Notes

## 2026-11-01: First Full Scan & Wild Hunting Lessons

### 1. `stars:>1000` Bug in `gh search issues`
- **Friction observed:** `gh search issues` has no `--stars` flag; passing `"stars:>1000"` treated the number `1000` as a literal search keyword. Four of the top 5 results were literally issue `#1000` in obscure repositories with 1–80 stars (`chaoss/8Knot`, `pyqtgraph`, `INGInious`).
- **Correction made:** Filtered by repository organizations directly (`vllm-project`, `pytorch`, `opensearch-project`, `backstage`) instead of relying on broken text-based star search terms.

### 2. Tracker Evaluation & Direct Grading
- **Evaluated:** Graded top 3 TypeScript issues from `backstage/community-plugins` (#10991, #10802, #10998).
- **Outcome:** All three received **`reject`** on `Nobody already working on it`.
- **Key Insight:** `gh search issues` and tracker sites only show issue metadata (`assignee: none`), but do not expose unmerged pull requests. Claude's rubric check searched linked pull requests and caught open PRs on all three issues (PR #11121, PR #10892, PR #11114), saving days of duplicate wasted work.
- **Next candidates:** Evaluating uncontested Python and TypeScript candidates with 0 open PRs (`opensearch-project/opensearch-build#775`, `opensearch-project/OpenSearch-Dashboards#6670`).

## 2026-11-11: Issue-state gap and re-grade

- **Friction observed:** direct grading accepted a closed issue (`pytorch/pytorch#97469`) because the rubric had no check for issue state. The skill's own evidence line said "issue itself closed 'completed' (no rubric check for state)".
- **Correction made:** added a required **Issue still open and unfixed** check to `rubric.md` (pass if state is open and no merged linked PR declares it fixes/closes the issue) and an **Issue state** row to `references/evidence-guide.md`.
- **Verification:** re-ran the 20-bundle eval with the patched rubric — 20/20, all categories pass. Re-graded `pytorch/pytorch#113040` — still `accept`; `pytorch/pytorch#97469` now correctly `reject` on the new check.
- **Selected issue:** `pytorch/pytorch#113040` — open, unassigned, no open PR, one closed unmerged PR (#185782, stale). The prior PR is a map, not a merged fix.

## 2026-11-16: Stale verdict caught, pick moved to OpenSearch

- **Friction observed:** `pytorch/pytorch#113040` was re-graded `accept` on 2026-11-11 with no assignee and no open PR, but by 2026-11-12 an open PR (#204547, 205+/19−, 6 files) implemented the same scope. A grading verdict has a shelf life; the issue metadata alone cannot show unmerged work.
- **Correction made:** re-checked the linked-PR state immediately before claiming instead of trusting the grading date, and added the re-check as the last step before a claim. Moved the pick to `opensearch-project/opensearch-build#775` (open since 2021, no assignee, zero claim comments, no open PR, `good first issue`, repo pushed 2026-11-16, `os.chdir` still in 18 files). Correction to my first pass on it: the one linked PR (#772) is **merged**, not closed-unmerged — it cross-referenced #775 as adjacent context-manager work, so it is not an abandoned attempt at this issue.
- **Also found while re-checking the OpenSearch candidates:** `OpenSearch-Dashboards#7141` is still OPEN but already fixed in main by merged PR #12315 (`uuid` 3.3.2 → 11.1.1, merged 2026-07-06) and has 4 closed unmerged PRs — so it fails both "still unfixed" and "no abandoned prior attempts". `OpenSearch-Dashboards#6670` remains open with one closed unmerged PR (#12532). `opensearch-build#2614` remains open but the thread shows maintainer doubts about `ruamel.yaml`, so it is a poor first pick.
- **Selected issue:** `opensearch-project/opensearch-build#775`.

## 2026-11-16: Scope check replaced with scope-feasible (unit 6 patch)

- **Patch applied:** replaced my required **Issue scope** row with **scope-feasible**, and added the difficulty-note section under the checks table. The old row failed an issue whose direction was still debated; the new row only fails when there is no single piece of work at all (codebase-wide checklist, "improve X everywhere", container issue), and an open design edge now raises a difficulty note instead of rejecting.
- **Driven by:** `opensearch-build#775` passed the old row only because its direction happened to be settled. The two OpenSearch candidates that came closest to being rejected for having several files in play (`#2614`, `OpenSearch-Dashboards#6670`) are the kind of honest wild work the old check would have filtered out.
- **Verified:** the 20-bundle eval was not re-run this week (it is a unit-8/9 regression step); the patch adds a check name and a reporting line, so eval output shape is unchanged apart from the renamed check.
