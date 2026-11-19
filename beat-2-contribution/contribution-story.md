# Contribution: Remove all os.chdir in favor of cwd/working directory in execute

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** An Dang
**GitHub Username:** Unheat
**Issue:** https://github.com/opensearch-project/opensearch-build/issues/775
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Building and tuning the issue-scout across GitHub and tracker candidates**

I built the issue-scout skill by pairing broad GitHub CLI searches with deterministic ranking filters and Claude-driven rubric grading. During the first wild scan, passing `stars:>1000` to `gh search issues` broke because GitHub's issue search has no repository-stars filter and treated "1000" as a literal keyword, so I replaced it with repository and organization qualifiers in `search-strategy.md`. Direct grading on tracker picks caught unmerged pull requests on three seemingly open Backstage issues, preventing duplicate work. Grading the Issue Selection candidates then exposed a real gap: the scout accepted a closed issue (`pytorch/pytorch#97469`) because the rubric had no state check, so I added a required "Issue still open and unfixed" check plus an "Issue state" row to the evidence guide, kept the 20-bundle eval at 20/20, and re-graded to leave `pytorch/pytorch#113040` accepted.

**Unit 6: Voice guard, repo memory, and a scope check that stopped rejecting the wild**

I installed the voice guard as a PreToolUse hook over every outgoing `gh` body, promoted the three carried-forward voice guides into one canonical guide at `~/.claude/voice-guard/voice-guide.md`, and wrote the upstream section myself: claim, question and ping rules, each with a wrong/right pair. The wiring test passed in the way it is supposed to - the guard stopped a body-less `gh issue comment --help` with "no body the guard can read" - and the manual check on my real claim draft came back pass, which is how I knew the guide was binding rather than decorative. The memory file was the smaller job: I read `CONTRIBUTING.md` end to end first, then distilled only the conventions and the chdir/execute subsystem into `CLAUDE.local.md` (34 lines) and hid it with `.git/info/exclude` so it never enters the PR diff. The real correction was in the scout: my required `Issue scope` check rejected anything with an open edge, which is most honest wild work, so I replaced it with `scope-feasible` and moved the ambiguity into a difficulty note - `#775` came out **ambitious** because of one open design question (`WorkingDirectory`), and that question is now the first thing my claim asks.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I started from the Issue Selection site and graded the Python candidates with my issue-scout. My first accepted pick was `pytorch/pytorch#113040`, but when I re-checked it a week later an open PR (#204547) implementing exactly that scope had appeared within a day of my grading, so the scout's "Nobody already working on it" check no longer passed. That is the trap my Unit 5 notes already describe: tracker metadata shows `assignee: none`, but unmerged PRs are invisible to it.

So I moved to `opensearch-project/opensearch-build#775`, which is deliberately boring in the way I want. It has been open since 2021 with no assignee and no claim comment at all, no open linked PR, one already-merged 2021 PR (#772) that merely cross-referenced it, a `good first issue` label, and a repository that is still pushed to daily. My scout graded it **accept** with the difficulty note **ambitious**, and that note is the honest part: 31 `os.chdir` occurrences across 18 files is not a bounded fix, and the open edge is real — PR #772 added `WorkingDirectory`, which is itself a `chdir`/restore pair, so "remove all `chdir`" does not say whether that helper stays. I raised exactly that in my claim instead of guessing, which is what the difficulty note is for. It fits the profile I wrote because it is Python-only build tooling and the work is mechanical and testable — `execute()` already takes a `dir` and passes `cwd=dir` to `subprocess.run`, so each raw `chdir` site is an independent conversion I can land in reviewable pieces with the repo's own `pytest` suite proving each one behaves the same.

I chose it to learn how a real Python CLI/build codebase threads a working directory through `execute` calls, and how its tests set up and assert against the filesystem, without needing a GPU or a full PyTorch build. The scout was not perfect: it first accepted a closed issue (`pytorch/pytorch#97469`) because my rubric had no issue-state check, it accepted `#113040` while it was still uncontested, and its old scope row would have rejected `#775` precisely for the open edge that makes it interesting — which is why I replaced that row with `scope-feasible` this week. I added the missing required checks, re-ran the 20-bundle eval (20/20), and I now re-check the linked-PR status right before claiming rather than trusting the grading date.

**Claim:** https://github.com/opensearch-project/opensearch-build/issues/775#issuecomment-6999931973

**Toolkit**

- Tool: issue-scout — my search strategy and rubric, used to scan and grade the Issue Selection candidates, then to re-grade after the rubric fix, again when `#113040` was taken, and a third time after I patched the scope check.
- What it produced: a ranked read-out where most candidates were rejected on open PRs, abandoned attempts, or scope; `pytorch/pytorch#113040` was accepted on 2026-11-11 and then failed on a re-check when PR #204547 opened; `opensearch-project/opensearch-build#775` passed every required check — open, no assignee, no claim comment, no open PR, one merged 2021 PR, active repo, `good first issue` — with the difficulty note **ambitious**, naming the `WorkingDirectory` question; `OpenSearch-Dashboards#6670` also accepted (moderate), `opensearch-build#2614` accepted (ambitious, ruamel direction questioned since 2023).
- Difficulties: the rubric had no issue-state check, so the first pass accepted a closed issue (`pytorch/pytorch#97469`); a verdict goes stale fast, since `#113040` was contested within about a day of being graded; and the original scope row rejected issues for having an open edge, which would have failed `#775`'s own design question and both other OpenSearch picks.
- What changed: added the required **Issue still open and unfixed** check to `rubric.md` and the **Issue state** row to `references/evidence-guide.md`; re-ran the 20-bundle eval at 20/20; replaced the **Issue scope** row with **scope-feasible** and added the difficulty-note section, logged as a dated line in `scan-notes.md`; and I now re-verify the open-PR check immediately before claiming instead of relying on the grading run.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

macOS 27.0.1 (Darwin, arm64), CPython 3.12.12, and `opensearch-project/opensearch-build` at `main` = `619c091b16af2345fd1a39c162dd014f817abc88` ("Refresh benchmark nightlies for OS-3.11.0 (#6570)", 2026-11-18); the latest release tag at the time was `3.10.0`. Setup was a shallow clone plus `PYTHONPATH=src` for the two modules involved, because `src/system/working_directory.py` and `src/system/temporary_directory.py` import only the standard library. I did not need the full `pipenv install --dev` environment (boto3, pytest, mypy and friends) to demonstrate the problem, and I say so rather than implying I ran the whole suite here: the full `pipenv run pytest` run belongs to the change, not to this reproduction. The one challenge worth naming is that the issue text predates the repo's move of `test_workflow/` under `src/`, so the paths I quoted had to come from `grep` on today's tree, not from the issue body.

**Steps to Reproduce**

1. `git clone --depth 1 https://github.com/opensearch-project/opensearch-build.git && cd opensearch-build && git rev-parse HEAD` — confirms `619c091b…`.
2. Count what is left: `grep -rn "os\.chdir" src tests | wc -l` → **31** occurrences; `grep -rln` → **6** files under `src/`, **12** under `tests/`.
3. Run the helper the way the perf runners do, and read the working directory from a second thread while the `with` block is open: `PYTHONPATH=src python3` with a `WorkingDirectory(d)` block that spawns a thread printing `os.getcwd()`.
4. Observed result: the second thread prints the directory the block changed to, not the directory the process started in — `chdir` is process-wide, so concurrent work in another directory sees the wrong cwd, and the restore only happens on block exit.

**Reproduction Evidence**

- **Commit showing reproduction:** none — the report is against upstream `main` at `619c091`; the branch and commit come with the first patch.
- **Screenshots/logs:** terminal output, verbatim, macOS 27.0.1 / Python 3.12.12:

```text
inside   os.getcwd() = /private/var/folders/xb/.../T/tmpr7kzgwz8
thread   sees cwd    = /private/var/folders/xb/.../T/tmpr7kzgwz8
thread sees chdir'd dir?  True
after    os.getcwd() = /private/tmp
```

- **My findings:** the issue is still real, and the target API already exists — `src/system/execute.py:6` is `execute(command: str, dir: str, ...)` and line 17 calls `subprocess.run(command, cwd=dir, ...)`. The remaining raw sites are `src/system/temporary_directory.py:51,64`, `src/test_workflow/integ_test/integ_test_suite_opensearch.py:100`, `integ_test_suite_opensearch_dashboards.py:103`, `src/test_workflow/perf_test/perf_test_suite.py:72`, and `perf_test_cluster.py:75,100`. Four runners already use `WorkingDirectory` (`perf_test_runner_opensearch.py:44`, `perf_test_runner_opensearch_plugins.py:44`, `benchmark_test_runner_opensearch.py:53`, `perf_test_suite.py:65`), and that helper is itself a `chdir` pair added by merged PR #772 the same day the issue was opened — so "remove all `chdir`" does not say whether the helper stays. That is the open edge I raised in my claim rather than guessing at it.

**Toolkit**

- Tool: repro-check — installed with the wild scope from Unit 6, then used in live mode against my claim draft and this report, all five rubric rows (`env-recorded`, `steps-followable`, `behavior-matches`, `claim-specific`, `conventions-respected`).
- What it produced: a pass on both files after one revision — the first claim draft said "the last claim comment here is from four years ago" when the thread actually has no claim comments at all, which `claim-specific` and my own accuracy rule caught, and the environment section needed the explicit note that I skipped the full pipenv suite.
- Difficulties: none in the tool; the friction was mine — writing an environment section that sounds complete while it deliberately covers only the stdlib-only path, and resisting the urge to claim I had run `pipenv run pytest` when I had not.
- What changed: corrected the false thread claim, added the "what I did not run" sentence, and added the repo's AI disclosure to the claim because the OpenSearch policy requires it and `conventions-respected` reads policy against the draft.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
