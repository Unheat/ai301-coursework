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
