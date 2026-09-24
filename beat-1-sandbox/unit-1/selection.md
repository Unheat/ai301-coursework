# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62

**Verdict output**

All evidence gathered. Now grading against the rubric.

- **Maintainer activity**: recent commits by Andrew Burke on 2026-09-16, well within 90 days → pass.
- **Repo not archived**: `archived: false` → pass.
- **Issue scope**: single specific problem (mismatched `settings.redis_host`/`redis_port` vs `redis_url`), with clear fix location → pass.
- **Nobody already working on it**: no assignees, no comments, no linked PRs (open or closed) → pass.
- **AI contribution policy**: no CONTRIBUTING.md/README text banning AI contributions found → pass.
- **Good-first-issue signal** (preferred): labeled "good first issue" → pass.
- **No abandoned prior attempts**: 0 closed linked PRs (< 2) → pass.

All required checks pass → **accept**.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62",
  "checks": [
    {"name": "Maintainer activity", "grade": "pass", "evidence": "Most recent commit by Andrew Burke on 2026-09-16T21:50:18Z, within 90 days of the 2026-09-23 snapshot date"},
    {"name": "Repo not archived", "grade": "pass", "evidence": "repo-facts: \"archived\": false"},
    {"name": "Issue scope", "grade": "pass", "evidence": "Issue names one specific problem: health check uses `settings.redis_host`/`redis_port` which don't exist, should use `settings.redis_url`"},
    {"name": "Nobody already working on it", "grade": "pass", "evidence": "Assignees: None; Comments: None; Development section states \"No branches or pull requests\""},
    {"name": "AI contribution policy", "grade": "pass", "evidence": "No CONTRIBUTING.md or README text found banning AI-generated/assisted contributions"},
    {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labels include \"good first issue (Good for newcomers)\""},
    {"name": "No abandoned prior attempts", "grade": "pass", "evidence": "0 closed linked PRs found referencing issue #62 (search for is:pr 62 in:body returned no results)"}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

`agreement: 0/1 scored items`
`agreement: 1/1 scored items`
`agreement: 19/20 scored items  (bar: 18/20: PASS)`

**Issue analysis**

Issue: `issue-15`
Decision: `reject`
Gold label: `reject`
Reasoning: `issue-15` (`zulip/zulip#19589`) is an issue with a friendly label, but the comment thread reveals years of unresolved design debate and two abandoned PRs. Our rubric rejected it on `Issue scope` (failing because the direction remains an active architectural debate rather than a bounded problem to fix) and on `No abandoned prior attempts` (failing because there are 2 closed linked PRs that were abandoned after difficulty getting approval).

**Check rationale**

| Issue scope | Issue body and comment thread | Pass if the issue names one specific problem to fix, even if the maintainer lists several possible causes or candidate implementation approaches for that one problem. Fail if it is an umbrella/tracking issue covering multiple unrelated problems, a request whose fundamental direction is still being debated (nobody has settled on whether/how to do it), or a pure usage/support question | required |

Reasoning: The initial draft required the comment thread to converge on a single agreed approach, which caused false rejections on straightforward documentation or bug issues (like `issue-01`) where the author clearly specified the problem and no discussion comments existed yet, or where multiple candidate solutions were discussed for one bounded problem. The revised check targets the core risk: rejecting umbrella/tracking issues and ongoing directional debates while passing well-defined, bounded tasks even if candidate fixes are brainstormed.

**Trade-offs**

By allowing candidate implementation approaches to be discussed as long as one specific problem is named, the check gives up strict design consensus enforcement and can accept issues like `issue-20` (`excalidraw/excalidraw#11811`), which gold labels reject because it is a one-line feature wish hiding unmade product decisions. We accept this trade-off because over-filtering legitimate, well-specified newcomer issues (like `issue-01`) is a worse outcome for first-time contributors than occasionally requiring manual triage on borderline feature requests.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Fit to interests and available time:
Issue #62 is a clear, self-contained Python backend bug in the health check endpoint. It requires updating an attribute reference from non-existent `settings.redis_host` / `settings.redis_port` to `settings.redis_url` and verifying the `/health` endpoint response. This fits my comfort with Python web services and fits neatly into the available timeframe without requiring extensive environment bootstrapping or large-scale architectural refactoring.

2. What the verdict identified correctly vs. what I weighed that the rubric could not:
The rubric correctly verified that the maintainer is actively pushing commits, the repo is not archived, the issue is clean of assignees or active pull requests, and the scope is bounded to a single endpoint bug. What I weighed that the rubric could not is the ease of local reproduction and developer ergonomics: the issue description provides an exact curl command (`curl -i http://localhost:8000/health`) that immediately replicates the 503 error, and the fix requires editing only `app/routers/health.py`, making it low-risk and verifiable with high confidence.

3. Anticipated difficulty in claiming it:
I anticipate very low difficulty in claiming the issue. Under the Path Review house rules, multiple students can work on or claim issues without blocking each other since credit is awarded for submitting the PR rather than merge exclusivity. Additionally, the issue currently has zero comments, zero assignees, and zero linked pull requests, so there is no immediate merge contention or competing branch in progress.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
