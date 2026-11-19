# Issue Scout Scan Output

Graded against `rubric.md` on 2026-11-01.

## Evaluated Candidates from `tracker-candidates.md` (Top 3 TypeScript Candidates)

### Summary Table

| Issue | Sank on | Evidence |
| :--- | :--- | :--- |
| #10991 Grafana state column | Nobody already working on it | Open PR #11121 by MatheusMartinho, opened 2026-09-21, body references the issue |
| #10802 Tech-radar quadrants | Nobody already working on it | Open PR #10892 by caddydove, opened 2026-09-04; maintainer: "avoid having multiple people working on the same issue" |
| #10998 GraphiQL endpoints | Nobody already working on it | Open PR #11114 by rajin-khan, opened 2026-09-18; maintainer notes fix is a one-line change |

All three pass maintainer activity, repo not archived, and issue scope. All three fail on `Nobody already working on it` due to existing open pull requests.

### Machine-Readable JSON Block

```json
[
  {
    "item": "https://github.com/backstage/community-plugins/issues/10991",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Non-bot commit 2026-10-31 (backstage-service version bump); latest push 2026-11-02"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "Issue scope", "grade": "pass", "evidence": "One bug: Alerts card lacks State column in New Frontend System; maintainer says 'Happy to get a contribution to fix this'"},
      {"name": "Nobody already working on it", "grade": "fail", "evidence": "Open PR #11121 'fix(grafana): expose card table options as extension config in the New Frontend System' by MatheusMartinho, 2026-09-21"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "CONTRIBUTING.md 'AI Use Policy and Guidelines' links the upstream Backstage policy; no outright ban stated in the repo text read (upstream policy not read)"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels include 'good first issue' and 'help wanted'"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "Only linked PR is open; 0 closed linked PRs"}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/backstage/community-plugins/issues/10802",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Non-bot commit 2026-10-31; maintainer awanlin commented 2026-09-03 and 2026-09-08"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "Issue scope", "grade": "pass", "evidence": "One bug: tech-radar errors when fewer than four quadrants have data"},
      {"name": "Nobody already working on it", "grade": "fail", "evidence": "Open PR #10892 'fix(tech-radar): render fewer than four quadrants' by caddydove, 2026-09-04; maintainer: 'avoid having multiple people working on the same issue'"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "Conditions-based policy linked from CONTRIBUTING.md; no ban found in the repo text read (upstream policy not read)"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels include 'good first issue' and 'help wanted'"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "Only linked PR is open; 0 closed linked PRs"}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/backstage/community-plugins/issues/10998",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Non-bot commit 2026-10-31; maintainer awanlin commented 2026-09-18 and 2026-10-01"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "Issue scope", "grade": "pass", "evidence": "One bug: GraphiQLEndpointBlueprint attachTo targets an unregistered extension id; maintainer suggests a one-line change in alpha.tsx"},
      {"name": "Nobody already working on it", "grade": "fail", "evidence": "Open PR #11114 'fix(graphiql): attach endpoints to browse API' by rajin-khan, 2026-09-18"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "Conditions-based policy linked from CONTRIBUTING.md; no ban found in the repo text read (upstream policy not read)"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels include 'good first issue' and 'help wanted'"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "Only linked PR is open; 0 closed linked PRs"}
    ],
    "verdict": "reject"
  }
]
```

---

## 2026-11-11: Direct grading of the selected issue

### `pytorch/pytorch#113040` — accept

| Check | Grade | Evidence |
| :--- | :--- | :--- |
| Maintainer activity | pass | Recent non-bot default-branch commits (2026-11-10 to 2026-11-12) |
| Repo not archived | pass | `isArchived: false` |
| Issue still open and unfixed | pass | state OPEN; linked PR #185782 closed unmerged on 2026-10-09 |
| Issue scope | pass | One problem: surface recompiles next to graph breaks in the CI comparison |
| Nobody already working on it | pass | No assignee, no open linked PR; the 2023 and 2026-06-28 claims are stale and unanswered |
| AI contribution policy | pass | `AI_POLICY.md` allows AI-assisted work with disclosure; bans only fully autonomous agents |
| Good-first-issue signal | pass | Label `good first issue` |
| No abandoned prior attempts | pass | 1 closed linked PR (#185782), fewer than 2 |

Note: the same grading pass caught `pytorch/pytorch#97469` as **closed and completed**. The rubric was patched with the required **Issue still open and unfixed** check, the 20-bundle eval stayed at 20/20, and `#113040` was re-graded as accept.

```json
[
  {
    "item": "https://github.com/pytorch/pytorch/issues/113040",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Recent non-bot default-branch commits, e.g. guangyey 2026-11-10 and leewayx 2026-11-12, within 90 days"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "Issue still open and unfixed", "grade": "pass", "evidence": "state OPEN; closedByPullRequestsReferences empty; only linked PR #185782 closed unmerged"},
      {"name": "Issue scope", "grade": "pass", "evidence": "Single problem: track recompiles alongside graph breaks in CI expected/actual comparison; three examples illustrate it"},
      {"name": "Nobody already working on it", "grade": "pass", "evidence": "No assignees, no open linked PR; only stale unanswered claims (2023-12, 2026-06-28) with no PR"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "AI_POLICY.md: 'We support the use of AI tools'; only fully autonomous agents are rejected"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Label 'good first issue' present"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "1 closed linked PR (#185782, closed unmerged 2026-10-09), fewer than 2"}
    ],
    "verdict": "accept"
  }
]
```

---

## 2026-11-16: Re-check of the previous pick, and the new selection

### `pytorch/pytorch#113040` — reject (was accept on 2026-11-11)

| Check | Grade | Evidence |
| :--- | :--- | :--- |
| Issue still open and unfixed | pass | state OPEN, no assignee |
| Nobody already working on it | **fail** | Open PR #204547 `[Dynamo] Track recompiles in benchmark accuracy CI` by arnavdesai2003, opened 2026-11-12, 205+/19− across 6 files including `benchmarks/dynamo/check_graph_breaks.py`; author commented asking for the `actionable` label |

The verdict went stale within a day of grading: no assignee and no PR at grading time, a full PR the next day. This is the same blind spot as the Backstage rejections — `gh search issues` never shows unmerged pull requests.

### `opensearch-project/opensearch-build#775` — accept (re-graded against all 8 rubric rows, 2026-11-16)

| Check | Grade | Evidence |
| :--- | :--- | :--- |
| Maintainer activity | pass | Last 5 default-branch commits: peterzhuamazon 2026-11-16, rishabhmaurya 2026-11-13, gaiksaya 2026-11-12, opensearch-ci-bot 2026-11-12 (x2) — a non-bot commit well within 90 days of 2026-11-16 |
| Repo not archived | pass | `isArchived: false`; `pushedAt` 2026-11-16T23:41:15Z |
| Issue still open and unfixed | pass | state OPEN since 2021-10-20; `os.chdir` still called in 18 files (e.g. `src/system/temporary_directory.py` x2, `src/test_workflow/perf_test/perf_test_suite.py:72`, `src/test_workflow/integ_test/integ_test_suite_opensearch.py:100`); no merged PR declares it fixes #775 |
| Issue scope | pass | Body names one problem — "Remove all `chdir`", using `cwd=` on execute calls. Not an umbrella (no sub-item list), direction settled (the mechanism already exists: `execute(command, dir)` runs `subprocess.run(..., cwd=dir)` and PR #772 merged the `WorkingDirectory` context manager), not a support question. Caveat logged below |
| Nobody already working on it | pass | No assignee; no open linked PR (`gh pr list --search chdir` and `cwd` return `[]`); 0 claim comments — the only thread activity is label events (dblock 2021-10-20, gaiksaya 2024-08-23) |
| AI contribution policy | pass | Root `CONTRIBUTING.md` links the org policy; `opensearch-project/.github/CONTRIBUTING.md` §"AI Usage Policy" welcomes AI as a productivity tool, requires disclosure of substantial use and personal understanding — conditions, not a ban. Also: "excessively large contributions will be closed and asked to be broken down into smaller pieces" |
| Good-first-issue signal | pass (preferred) | Labels `good first issue`, `enhancement` |
| No abandoned prior attempts | pass | 1 linked PR total: #772, **merged** 2021-10-20 (a context-manager refactor it cross-referenced, not a fix attempt). 0 closed unmerged PRs, fewer than 2 |

Fit-profile note (does not change the verdict, only ranking): the repo has **212 stars**, below the profile's "1,000+ stars" preference, and the issue is a repo-wide refactor rather than a self-contained fix, which brushes against "avoiding massive multi-month refactors". Both are real tensions — taken because every required check passes and the work is mechanically splittable.

```json
[
  {
    "item": "https://github.com/pytorch/pytorch/issues/113040",
    "checks": [
      {"name": "Issue still open and unfixed", "grade": "pass", "evidence": "state OPEN, assignees empty"},
      {"name": "Nobody already working on it", "grade": "fail", "evidence": "Open PR #204547 by arnavdesai2003, opened 2026-11-12, touches benchmarks/dynamo/check_graph_breaks.py and 5 other files"}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/opensearch-project/opensearch-build/issues/775",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Non-bot commit 2026-11-16 (peterzhuamazon); last 5 default-branch commits all within 90 days of 2026-11-16"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false; pushedAt 2026-11-16T23:41:15Z"},
      {"name": "Issue still open and unfixed", "grade": "pass", "evidence": "state OPEN since 2021-10-20; os.chdir still called in 18 files, e.g. src/system/temporary_directory.py and perf_test_suite.py:72; no merged PR claims to fix #775"},
      {"name": "Issue scope", "grade": "pass", "evidence": "One problem: remove os.chdir in favor of cwd= on execute calls; direction settled because execute(command, dir) already passes cwd=dir and PR #772 merged WorkingDirectory"},
      {"name": "Nobody already working on it", "grade": "pass", "evidence": "No assignee, no open linked PR (searches for chdir and cwd return []), zero claim comments; only label events in the timeline"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "org .github/CONTRIBUTING.md 'AI Usage Policy' allows AI with disclosure and understanding; conditions, not a ban"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels: 'good first issue', 'enhancement'"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "1 linked PR total (#772, merged 2021-10-20, a context-manager refactor it cross-referenced); 0 closed unmerged PRs"}
    ],
    "verdict": "accept"
  }
]
```

---

## 2026-11-16: Re-grade of the three live picks under the patched rubric (scope-feasible)

Run in direct mode, one issue at a time. The scope row is now `scope-feasible`, so an open edge raises a difficulty note instead of rejecting. All three grade against the repo evidence gathered today.

### 1. `opensearch-project/opensearch-build#775` — accept, difficulty: **ambitious**

| Check | Grade | Evidence |
| :--- | :--- | :--- |
| Maintainer activity | pass | Last 5 default-branch commits: rishabh6788 2026-11-18, ci-bot ×2, peterzhuamazon 2026-11-16, rishabhmaurya 2026-11-13 |
| Repo not archived | pass | `isArchived: false`, `pushedAt` 2026-11-18T20:11:22Z |
| Issue still open and unfixed | pass | OPEN since 2021-10-20; `os.chdir` still called in 18 files; no merged PR claims to fix it |
| scope-feasible | pass | "Remove all `chdir`" is one identifiable task. Several files is a difficulty signal, not a fail |
| Nobody already working on it | pass | No assignee, no open linked PR, zero claim comments |
| AI contribution policy | pass | Org `AI Usage Policy`: AI allowed with disclosure + understanding; no ban |
| Good-first-issue signal | pass | `good first issue` |
| No abandoned prior attempts | pass | 1 linked PR (#772, merged 2021, adjacent work) — 0 closed unmerged |
| **Difficulty note** | **ambitious** | **Open edge:** `WorkingDirectory` (`src/system/working_directory.py`) is itself a `chdir`/restore pair, added by merged PR #772 the same day the issue was opened, so "remove all `chdir`" does not say whether that helper stays for block-scoped callers. The location is also yours to find (issue paths predate the `src/` reorg) and 31 occurrences span 18 files |

### 2. `opensearch-project/OpenSearch-Dashboards#6670` — accept, difficulty: **moderate**

| Check | Grade | Evidence |
| :--- | :--- | :--- |
| Maintainer activity | pass | Commits 2026-11-14 to 2026-11-18 (wanglam 2026-11-18) |
| Repo not archived | pass | `isArchived: false`, 2146 stars |
| Issue still open and unfixed | pass | OPEN; only linked PR #12532 closed unmerged 2026-09-04; no merged fix |
| scope-feasible | pass | One bug: Enter/click on the multi-value filter combo-boxy does not split comma-separated values |
| Nobody already working on it | pass | No assignee, no open linked PR, no claim comment in the thread |
| AI contribution policy | pass | Same OpenSearch org policy — conditions, not a ban |
| Good-first-issue signal | pass | `good first issue`, `bug` |
| No abandoned prior attempts | pass | 1 closed unmerged PR (#12532) — fewer than 2 |
| **Difficulty note** | **moderate** | Location is yours to find: `AMoo-Miki` points at OSD's wrapper around OUI combo-box rather than OUI itself, so the fix sits in the filter-bar component and its tests |

### 3. `opensearch-project/opensearch-build#2614` — accept, difficulty: **ambitious**

| Check | Grade | Evidence |
| :--- | :--- | :--- |
| Maintainer activity | pass | Same repo, pushed 2026-11-18 |
| Repo not archived | pass | `isArchived: false` |
| Issue still open and unfixed | pass | OPEN since 2022-09-13; `import yaml` still in ~17 files, `ruamel` only in `src/manifests_workflow/input_manifests.py` |
| scope-feasible | pass | One identifiable task: migrate the yaml module to `ruamel.yaml`. Under the old row this failed as "direction still debated" — that is now the difficulty note, not a reject |
| Nobody already working on it | pass | No assignee, no open linked PR, no claim comments |
| AI contribution policy | pass | Same org policy |
| Good-first-issue signal | pass | `good first issue` |
| No abandoned prior attempts | pass | 1 linked PR (#2602, merged 2022, adjacent workflow change) |
| **Difficulty note** | **ambitious** | **Open edge:** dblock asked in-thread whether `ruamel.yaml` is the right choice and nobody answered since 2023-09; claiming this means asking that first |

```json
[
  {
    "item": "https://github.com/opensearch-project/opensearch-build/issues/775",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Non-bot commit 2026-11-18 (rishabh6788) and 2026-11-16 (peterzhuamazon)"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false; pushedAt 2026-11-18T20:11:22Z"},
      {"name": "Issue still open and unfixed", "grade": "pass", "evidence": "OPEN since 2021-10-20; os.chdir still called in 18 files; no merged PR claims a fix"},
      {"name": "scope-feasible", "grade": "pass", "evidence": "One identifiable task: remove os.chdir in favor of cwd= on execute calls"},
      {"name": "Nobody already working on it", "grade": "pass", "evidence": "No assignee, no open linked PR, zero claim comments"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "Org AI Usage Policy allows AI with disclosure and understanding; no ban"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Label 'good first issue'"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "1 linked PR #772, merged 2021, adjacent context-manager work"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/opensearch-project/OpenSearch-Dashboards/issues/6670",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Commits 2026-11-14 to 2026-11-18, e.g. wanglam 2026-11-18"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false; 2146 stars"},
      {"name": "Issue still open and unfixed", "grade": "pass", "evidence": "OPEN; only linked PR #12532 closed unmerged 2026-09-04; no merged fix"},
      {"name": "scope-feasible", "grade": "pass", "evidence": "One bug: Enter or dropdown click does not split comma-separated values in multi-value filters"},
      {"name": "Nobody already working on it", "grade": "pass", "evidence": "No assignee, no open linked PR, no claim comment"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "Same OpenSearch org AI Usage Policy; conditions, not a ban"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels 'good first issue', 'bug'"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "1 closed unmerged PR #12532"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/opensearch-project/opensearch-build/issues/2614",
    "checks": [
      {"name": "Maintainer activity", "grade": "pass", "evidence": "Repo pushed 2026-11-18"},
      {"name": "Repo not archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "Issue still open and unfixed", "grade": "pass", "evidence": "OPEN since 2022-09-13; import yaml still in ~17 files, ruamel only in src/manifests_workflow/input_manifests.py"},
      {"name": "scope-feasible", "grade": "pass", "evidence": "One identifiable task: migrate the yaml module to ruamel.yaml"},
      {"name": "Nobody already working on it", "grade": "pass", "evidence": "No assignee, no open linked PR, no claim comments"},
      {"name": "AI contribution policy", "grade": "pass", "evidence": "Same OpenSearch org AI Usage Policy"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Label 'good first issue'"},
      {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "1 linked PR #2602, merged 2022, adjacent workflow change"}
    ],
    "verdict": "accept"
  }
]
```

Ranking against the fit profile: `#775` first (Python, primary language, mechanical and testable), `#6670` second (TypeScript, but a UI bug in a 2,146-star frontend), `#2614` third (Python, but ambitious and direction-questioned). **Chosen: `#775`.**
