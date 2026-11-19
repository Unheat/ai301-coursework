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
**Pull Request:** not opened yet — planned for unit 7, after the patch and the ship-gate.

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every section from Why I Chose This Issue through Pull Request ends with a Toolkit block: which tool did that phase's work, what it produced, what fought it, and what changed because of it. Below the blocks, one dated entry per week of tool work.

All four entries are graded. Keep every heading, fill each in the week it belongs to.

**Unit 5: Building and tuning the issue-scout across GitHub and tracker candidates**

I built the issue-scout skill by pairing broad GitHub CLI searches with deterministic ranking filters and Claude-driven rubric grading. During the first wild scan, passing `stars:>1000` to `gh search issues` broke because GitHub's issue search has no repository-stars filter and treated "1000" as a literal keyword — four of the top five results were literally issue `#1000` in repos with 1–80 stars. The fix is in `search-strategy.md` and it is visible there: every star term is gone, replaced by `--owner`-scoped queries (`pytorch`, `vllm-project`, `opensearch-project`, `backstage`) plus one keyword fallback, and star count moved out of search entirely into grading time, because the search result object has no star field. Direct grading on tracker picks caught unmerged pull requests on three seemingly open Backstage issues, preventing duplicate work. Grading the Issue Selection candidates then exposed a real gap: the scout accepted a closed issue (`pytorch/pytorch#97469`) because the rubric had no state check, so I added a required "Issue still open and unfixed" check plus an "Issue state" row to the evidence guide, kept the 20-bundle eval at 20/20, and re-graded to leave `pytorch/pytorch#113040` accepted.

**Unit 6: Voice guard, repo memory, and a scope check that stopped rejecting the wild**

I installed the voice guard as a PreToolUse hook over every outgoing `gh` body, promoted the three carried-forward voice guides into one canonical guide at `~/.claude/voice-guard/voice-guide.md`, and wrote the upstream section myself: claim, question and ping rules, each with a wrong/right pair. The wiring test passed in the way it is supposed to - the guard stopped a body-less `gh issue comment --help` with "no body the guard can read" - and the manual check on my real claim draft came back pass, which is how I knew the guide was binding rather than decorative. The memory file was the smaller job: I read `CONTRIBUTING.md` end to end first, then distilled only the conventions and the chdir/execute subsystem into `CLAUDE.local.md` (34 lines) and hid it with `.git/info/exclude` so it never enters the PR diff. The real correction was in the scout: my required `Issue scope` check rejected anything with an open edge, which is most honest wild work, so I replaced it with `scope-feasible` and moved the ambiguity into a difficulty note - `#775` came out **ambitious** because of one open design question (`WorkingDirectory`), and that question is now the first thing my claim asks.

**Unit 7: not yet started — patch branch, ship-gate and gate manifest come next week.**

**Unit 8: not yet started — PR monitor and playbook come in the final week.**

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

`opensearch-build` changes the process's working directory with `os.chdir()` in its build, test and CI scripts. That directory is global to the process, so any code running in another thread sees the change, and a helper that saves and restores the directory still leaks while it is open. The issue asks to stop doing that and pass the directory explicitly to the command being executed instead.

**Expected Behavior**

A command runs in the directory it is given, and the process's own working directory is never mutated. `execute(command, dir)` already behaves this way: it calls `subprocess.run(command, cwd=dir, ...)` and leaves `os.getcwd()` untouched.

**Current Behavior**

On `main` at `619c091b`, `os.chdir` is still called 31 times across 18 files — 9 occurrences in 6 files under `src/`, and 12 files under `tests/` that use it for fixtures. A thread started inside `WorkingDirectory(path)` reads that path as its cwd while the block is open, even though it never asked for it. Several call sites are redundant: `perf_test_cluster.start()`/`terminate()` `chdir` and then pass `cwd=os.getcwd()` to the same subprocess call.

**Affected Components**

`src/system/working_directory.py` and `src/system/temporary_directory.py` (the helpers built on `chdir`), `src/system/execute.py` (the explicit-cwd API that already exists), `src/test_workflow/perf_test/perf_test_cluster.py` and `perf_test_suite.py`, `src/test_workflow/integ_test/integ_test_suite_opensearch.py` and `..._dashboards.py`, and the 12 test modules whose fixtures call `os.chdir`.

**Toolkit**

- Tool: `grep -rn "os\\.chdir" src tests` plus direct reads of the five `src/` files, and `gh` for the issue and linked-PR history.
- What it produced: the 31 / 6 / 12 counts, the seven raw site paths, and the fact that `execute()` already takes `dir` — which is what makes this a migration rather than new plumbing.
- Difficulties: the issue text is from 2021 and its paths predate the move of `test_workflow/` under `src/`, so the issue body could not be used as a map; every path had to come from the current tree.
- What changed: my understanding went from "remove all chdir" (a refactor) to "three kinds of site, one of which is a design question" — which is what I put in the claim instead of guessing.

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

The root cause is not one bug but one pattern repeated: the process's working directory is used as an implicit parameter. `execute()` in `src/system/execute.py` already takes that parameter explicitly — `execute(command: str, dir: str, ...)` runs `subprocess.run(command, cwd=dir, ...)` — so a `chdir` before a call is redundant, and a `chdir` anywhere else silently changes the directory for every other thread in the process. My reproduction shows exactly that: a second thread inside a `WorkingDirectory` block reads the changed directory, not the one the process started in (`src/system/working_directory.py:17`).

Reading the seven raw sites, they fall into three kinds:

1. **Redundant**: `perf_test_cluster.py:75` and `:100` call `os.chdir(self.work_dir)` and then `subprocess.check_call(command, cwd=os.getcwd(), ...)` — the directory is already being passed, so the `chdir` only adds global state. `perf_test_suite.py:72` does the same with `WorkingDirectory` plus `cwd=current_workspace`.
2. **Load-bearing**: `integ_test_suite_opensearch.py:100` and `integ_test_suite_opensearch_dashboards.py:103` `chdir` before `multi_execute_integtest_sh`, which resolves `ScriptFinder.find_integ_test_script(...)` and calls `execute(cmd, self.repo_work_dir, ...)`. `ScriptFinder` builds paths through `os.path.realpath`, so the `chdir` is likely redundant here too, but this is the one I will verify with a test before deleting it.
3. **The contract itself**: `WorkingDirectory` and `TemporaryDirectory` exist to `chdir` (`working_directory.py:17,20`, `temporary_directory.py:51,64`). Removing those is a design decision, not a cleanup, which is why my claim asks the maintainer instead of assuming.

**Proposed Solution**

Convert call sites from implicit to explicit working directories, in separate small PRs, and leave the helper contract alone until the maintainer answers:

- PR 1 (mechanical): delete the redundant `chdir` calls in `perf_test_cluster.py` and `perf_test_suite.py`, passing `cwd=` where it is currently `cwd=os.getcwd()`.
- PR 2 (needs proof): `integ_test` suites, after a test shows `ScriptFinder` returns an absolute path so `os.path.exists(script)` does not depend on cwd.
- PR 3 (only if the maintainer wants it): `WorkingDirectory`/`TemporaryDirectory` removal or narrowing, with the four runners that use `WorkingDirectory` migrated in the same PR.

Deliberately **not** changing: the end-state of `perf_test_suite.execute()`'s `finally: os.chdir(self.current_workspace)` until I know which caller relies on the process ending in the workspace root. That line is a post-condition, not an accident, and deleting it in a "cleanup" PR would be silent behaviour change.

**Implementation Plan**

1. `src/test_workflow/perf_test/perf_test_cluster.py` — remove `os.chdir(self.work_dir)` from `start()` and `terminate()`; both already run the command with `cwd=`, so pass `cwd=self.work_dir`.
2. `src/test_workflow/perf_test/perf_test_suite.py` — replace the `with WorkingDirectory(current_workspace)` wrapper with the `cwd=current_workspace` the calls already pass; keep the `finally` line and document why.
3. `src/test_workflow/integ_test/integ_test_suite_opensearch.py` and `..._dashboards.py` — add a test proving the script path is absolute, then drop the `chdir` at lines 100/103.
4. `tests/` — update the affected fixtures to pass paths explicitly (or use `monkeypatch.chdir` where the fixture genuinely needs a directory), so no test depends on leaked cwd.
5. Run the targeted suites, then the full suite, plus `flake8`/`mypy`; open PR 1 first, and reference this issue plus the maintainer question.

Deviations: none yet — code work starts in unit 7. I will record each change here as it happens rather than batching at the end.

**Toolkit**

- Tool: plan-check (wild scope, installed in unit 6) — not run yet; the plan above is the draft it will grade before I post it on the issue in unit 7.
- What it produced: nothing yet — the plan is written from the reproduction evidence and the call-site reading, not from the tool.
- Difficulties: none yet; the honest gap is that plan-check has not seen this plan, so its rubric rows (bounded scope, traced analysis) are unverified.
- What changed: nothing yet; when plan-check runs, any row it fails changes this section rather than being argued with.

---

## Testing Strategy

Two halves, because "the fix works" and "nothing else broke" are different claims.

**Half 1: the reproduction, re-run before and after.** The check is the same script from the Reproduction Process section: open a `WorkingDirectory` block, read `os.getcwd()` from a second thread, and assert the thread still sees the process's original directory after the change. Before: `thread sees chdir'd dir? True`. After: expected `False`, plus `grep -rc "os\.chdir" src` dropping from **9 occurrences in 6 files** to whatever remains (the helpers, if the maintainer keeps them). That is observable in the PR diff and does not depend on anyone trusting me.

**Half 2: the repo's own checks.** From `CLAUDE.local.md` (CONTRIBUTING/DEVELOPER_GUIDE):

```bash
pipenv run pytest tests/tests_test_workflow/test_perf_workflow -q   # the suites I touch first
pipenv run pytest                                                    # full suite
pipenv run flake8 && pipenv run isort --check . && pipenv run mypy . # pre-commit's checks
```

The perf suites spawn `pipenv install` and real subprocesses, so I will run the two touched modules directly too, and paste the actual pass/fail summary rather than the word "tests pass".

Honest status: **none of this has been run yet** — there is no patched branch, and a strategy section that shows green numbers today would be invented. The outputs land here and in the PR body when the patch exists.

**Toolkit**

- Tool: none yet — the commands come from the repo's `DEVELOPER_GUIDE.md` via my `CLAUDE.local.md`, not from a skill run.
- What it produced: the command list above, copied from the guide rather than from memory.
- Difficulties: the perf/integ suites are heavyweight (pipenv install, CDK, real clusters), so "run the tests" is not one command; I expect to run the touched modules first and the full suite last.
- What changed: nothing yet; if a suite cannot run in this environment (no Docker/CDK), I will say which and why instead of claiming it passed.

---

## Implementation Notes

**[2026-11-01]**

First wild scan. `stars:>1000` treated `1000` as a keyword, so four of the top five results were literally issue `#1000`. Three Backstage picks were rejected on unmerged PRs that no search result showed. Wrote the scan notes; did not touch the query file that day, which is the mistake I fix on 2026-11-19.

**[2026-11-11]**

Direct grading accepted a closed issue (`pytorch/pytorch#97469`) because the rubric had no state row. Added **Issue still open and unfixed**, added the **Issue state** row to the evidence guide, and re-graded: `#113040` accept, `#97469` reject.

**[2026-11-16]**

`#113040` was gone — open PR #204547 opened the day after I graded it. Re-picked `opensearch-build#775`; graded it plus `#6670` and `#2614`; replaced the strict `Issue scope` row with `scope-feasible` and moved ambiguity into a difficulty note. `#775` came out **ambitious** on one open question (`WorkingDirectory`).

**[2026-11-18]**

Posted the claim (guard pass). Reproduced on `619c091`: 31 `os.chdir` occurrences, 6 files under `src/`, 12 under `tests/`; a second thread sees the `chdir`'d directory; `execute()` already takes `dir`. Wrote `CLAUDE.local.md` (34 lines) so the repo's conventions and the chdir subsystem stop being re-learned each session.

**[2026-11-19]**

Grader feedback said the Unit 5 note documented the symptom but never named the corrective action, and that a reader could not tell whether the bug was still live. Checking the file showed something worse than an omission: `search-strategy.md` still contained the four `stars:>1000` queries the story said I had replaced. Rewrote the queries with `--owner` scoping, added a dated "What changed, and why" block to the file, and removed the leftover template scaffolding from this story. Lesson: when a note says "I changed X", open X and check — the file is the evidence, the note is only a claim.

**Toolkit**

- Tool: `grep` and direct file reads, plus `gh` for the issue history.
- What it produced: the five dated entries above, each tied to a file or command rather than to a memory of what I did.
- Difficulties: the 11-01 entry is why this log is worth keeping — the corrective action was written down a week before it was true, and only a later read caught it.
- What changed: the log entry for 2026-11-19 records the correction itself, including that the earlier note was misleading, rather than quietly editing history.

---

## Pull Request

**PR Link:** not opened yet — planned for unit 7, after the patch and the ship-gate.

**PR Description:** not written yet. The planned shape is one small PR (PR 1 from the implementation plan): drop the redundant `chdir` calls in `perf_test_cluster.py` and pass `cwd=self.work_dir`, with the before/after reproduction and the targeted `pytest` output in the body, plus an AI-assistance disclosure required by the OpenSearch policy.

**Maintainer Feedback:**

- 2026-11-18: claim posted with one question (does `WorkingDirectory` stay?). No reply yet — silence is a normal state and costs nothing.

**Status:** Not opened yet. No PR exists, so no review state to record.

**Toolkit**

- Tool: none yet — pr-precheck (installed, wild scope) will grade the title and body before the PR opens, and the ship-gate hook will run it automatically.
- What it produced: nothing yet; there is no branch and no draft body.
- Difficulties: none met yet; the known risk is that the org policy rejects oversized PRs, so this plan keeps PR 1 to two files.
- What changed: nothing yet.

---

## Learnings and Reflections

**Technical Skills Gained**

Working-directory semantics: how `subprocess.run(..., cwd=)` differs from `os.chdir()`, why `chdir` is process-wide and visible to every thread, and how a context manager that saves and restores cwd still leaks while it is open. I also learned the shape of this repo's test workflow — `pipenv` environments, `pytest` layout mirroring `src/`, pre-commit running isort/flake8/mypy — and that `execute()` was already the right API, which turned a vague "refactor" into a call-site migration.

**Challenges Overcome**

The real challenge was not the code, it was keeping claims true. My first accepted issue was taken within a day; my rubric accepted a closed issue because it lacked a state check; and my search strategy still contained the queries my notes said I had replaced. Each was caught by re-reading the artifact instead of trusting the note, and each produced a specific fix: the state row, the pre-claim re-check, the difficulty note, and the `--owner` rewrite.

**What I'd Do Differently Next Time**

Check the linked-PR state on the same day I claim, not the day I grade. Treat "I changed X" as a claim to verify by opening X before writing it into a story. And write the difficulty note before claiming rather than after, because naming the open edge (`does WorkingDirectory stay?`) changed my claim from "assign me" to a question the maintainer can answer in one line.

---

## Resources Used

- [OpenSearch contributing guide](https://github.com/opensearch-project/.github/blob/main/CONTRIBUTING.md) — DCO sign-off requirement, license headers, and the AI Usage Policy section I quote in my claim.
- [Issue #775](https://github.com/opensearch-project/opensearch-build/issues/775) and [PR #772](https://github.com/opensearch-project/opensearch-build/pull/772) — the original request and the merged change that added `WorkingDirectory`, which is what made the design question concrete.
- [`src/system/execute.py`](https://github.com/opensearch-project/opensearch-build/blob/main/src/system/execute.py) — the `execute(command, dir)` signature that made the whole issue tractable.
- [`DEVELOPER_GUIDE.md`](https://github.com/opensearch-project/opensearch-build/blob/main/DEVELOPER_GUIDE.md) — the `pipenv run pytest` / pre-commit commands I copied into my testing strategy and `CLAUDE.local.md`.
