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
