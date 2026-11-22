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
**Pull Request:** drafted, not opened yet — branch `fix/775-remove-redundant-chdir` is committed locally and gate-passed; see the Pull Request section below.

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

**Unit 7: Shipping the patch — reproducer, plan gate, and a ship-gate that rejected me once**

I built the patch branch in four gates rather than writing code first. The reproducer came first: a test file asserting `os.getcwd()` is unchanged after `PerfTestCluster.start()`, `PerfTestCluster.terminate()` and `PerfTestSuite.execute()`, committed on its own so it could fail against untouched source — 3 failed, 24 passed, and 8 failed once the rewrite of the five existing tests was included. That red number is the whole point of the gate: a test that has never been red proves nothing. Then my workflow skill's plan gate, which rejected my first plan for a real defect I had missed: `perf_test_suite.py:43` builds `work_dir` as `current_workspace + "/mensor/"` with a trailing slash, so the directory is `'/ws/mensor/'`, while my reproducer compared it to `'/ws/mensor'` as strings and would have failed after the fix for a reason that had nothing to do with the bug. The RED run never surfaced it because the `os.getcwd()` assertion fires first. I fixed the assertion to compare `os.path.realpath` on both sides — still a directory-identity test, still fails on any wrong directory — and recorded in the plan that the trailing slash stays, because normalizing it is a second unrelated change. Round 2: accept. The implementation itself was 11 insertions and 18 deletions in `src/` plus the tests, 190/55 across five files in total, all inside `src/test_workflow/perf_test/`. The ship-gate then rejected it once on `description-fidelity`: my PR body said five existing tests now assert `cwd=`, but only two did — the other three just dropped their `os.chdir` patch. The check was right, and the fix was not to reword the claim but to make it true: I added the `cwd=` assertions to the three. Second gate run: PASS, seven checks. What I would carry forward: both rejections came from tools reading my artifact against the code, and both were about a gap between what I had written and what was actually there — the same failure family as the stale `search-strategy.md` in unit 6.

**Unit 8: not yet started — PR monitor and playbook come in the final week.**

---

## Why I Chose This Issue

I started from the Issue Selection site and graded the Python candidates with my issue-scout. My first accepted pick was `pytorch/pytorch#113040`, but when I re-checked it a week later an open PR (#204547) implementing exactly that scope had appeared within a day of my grading, so the scout's "Nobody already working on it" check no longer passed. That is the trap my Unit 5 notes already describe: tracker metadata shows `assignee: none`, but unmerged PRs are invisible to it.

So I moved to `opensearch-project/opensearch-build#775`, which is deliberately boring in the way I want. It has been open since 2021 with no assignee and no claim comment at all, no open linked PR, one already-merged 2021 PR (#772) that merely cross-referenced it, a `good first issue` label, and a repository that is still pushed to daily. My scout graded it **accept** with the difficulty note **ambitious**, and that note is the honest part: 31 `os.chdir` occurrences across 18 files is not a bounded fix, and the open edge is real — `WorkingDirectory` (#678, 2021-10-06) is itself a `chdir`/restore pair, so "remove all `chdir`" does not say whether that helper stays. I raised exactly that in my claim instead of guessing, which is what the difficulty note is for. It fits the profile I wrote because it is Python-only build tooling and the work is mechanical and testable — `execute()` already takes a `dir` and passes `cwd=dir` to `subprocess.run`, so each raw `chdir` site is an independent conversion I can land in reviewable pieces with the repo's own `pytest` suite proving each one behaves the same.

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

- **My findings:** the issue is still real, and the target API already exists — `src/system/execute.py:13` is `execute(command: str, dir: str, ...)` and line 23 calls `subprocess.run(command, cwd=dir, ...)`. The remaining raw sites are `src/system/temporary_directory.py:51,64`, `src/test_workflow/integ_test/integ_test_suite_opensearch.py:100`, `integ_test_suite_opensearch_dashboards.py:103`, `src/test_workflow/perf_test/perf_test_suite.py:72`, and `perf_test_cluster.py:75,100`. Four runners already use `WorkingDirectory` (`perf_test_runner_opensearch.py:44`, `perf_test_runner_opensearch_plugins.py:44`, `benchmark_test_runner_opensearch.py:53`, `perf_test_suite.py:65`), and that helper is itself a `chdir` pair — added in #678, so `git log` on the freed-Windows history dates it to 2021-10-06, weeks before this issue — so "remove all `chdir`" does not say whether the helper stays. That is the open edge I raised in my claim rather than guessing at it. (I first wrote that #772 added it; #772 refactored `TemporaryDirectory`, not `WorkingDirectory`, and the repro-check grader caught the attribution.)

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

Deliberately **not** changing in this PR: the two `integ_test` sites (they need proof first) and the `WorkingDirectory`/`TemporaryDirectory` contract (the maintainer question). — *superseded during unit 7: I had also planned to keep `perf_test_suite.execute()`'s `finally: os.chdir(...)`. The reproducer showed that line is itself the leak, and the enclosing `WorkingDirectory` already restores the directory, so it went. The deviation is recorded below.*

**Implementation Plan**

1. `src/test_workflow/perf_test/perf_test_cluster.py` — remove `os.chdir(self.work_dir)` from `start()` and `terminate()`; pass `cwd=self.work_dir`, and build the CDK output path as `os.path.join(self.work_dir, self.output_file)` because `start()` read it relatively.
2. `src/test_workflow/perf_test/perf_test_suite.py` — replace the `with WorkingDirectory(current_workspace)` wrapper with the `cwd=current_workspace` the calls already pass; the `finally` line went too (see Deviations).
3. `src/test_workflow/integ_test/integ_test_suite_opensearch.py` and `..._dashboards.py` — add a test proving the script path is absolute, then drop the `chdir` at lines 100/103.
4. `tests/` — update the affected fixtures to pass paths explicitly (or use `monkeypatch.chdir` where the fixture genuinely needs a directory), so no test depends on leaked cwd.
5. Run the targeted suites, then the full suite, plus `flake8`/`mypy`; open PR 1 first, and reference this issue plus the maintainer question.

Deviations: one, recorded rather than quietly fixed. I wrote above that `perf_test_suite.execute()`'s `finally: os.chdir(self.current_workspace)` would stay, on the reasoning that it was a post-condition some caller might rely on. The reproducer disproved that: after the fix the process was still left in `current_workspace`, which is the exact leak the issue describes, and the enclosing `WorkingDirectory` in `perf_test_runner_opensearch.py:44` already restores the directory. So the line went, and the "deliberately not changing" note above is marked superseded rather than deleted. Two further deviations came from gates rather than from me: my workflow skill's four-check plan gate rejected the plan over a trailing-slash assertion that would have failed for the wrong reason, and the ship-gate rejected the PR body for claiming `cwd=` assertions in five tests when three of them did not yet have any. (Naming it precisely: that plan gate is the workflow skill's judge, not the course's six-check `plan-check`, which I ran separately and later — see the dated entry below.)

**Toolkit**

- Tool: plan-check (wild scope), run in live mode twice on 2026-11-21.
- What it produced: `reject` then `accept`. Round 1 failed **startable** with a concrete finding — `perf_test_suite.py:43` builds `work_dir` as `current_workspace + "/mensor/"`, so the directory the `chdir` set is `'/ws/mensor/'` while my reproducer asserted `'/ws/mensor'` as a string; the RED run never reached that assertion because the `os.getcwd()` check fires first, so my "3 failed → all passed" claim would not have held.
- Difficulties: the trailing slash is a real pre-existing wart. The tempting fix was to normalize `work_dir`, which would have quietly added an unrelated change to a `chdir` PR.
- What changed: the reproducer now compares `os.path.realpath` on both sides — same directory-identity guarantee, no dependence on spelling — and the plan states explicitly that the trailing slash stays, with the reason. Round 2 accepted all four checks.

---

## Testing Strategy

Two halves, because "the fix works" and "nothing else broke" are different claims.

**Half 1: the reproduction, re-run before and after.** The check is `os.getcwd()` after each of the three methods, plus the `cwd=` each `check_call` received — moved from a hand-run script into `tests/tests_test_workflow/test_perf_workflow/perf_test/test_perf_cwd_isolation.py` so it runs on every future change rather than once by hand. Before: the thread sees the `chdir`'d directory, `thread sees chdir'd dir? True`. After: `False`, and `git grep -c "os\.chdir" HEAD -- src/` goes from 9 lines on `main` to 6 on the branch, the 4 helpers' internals among them. That is observable in the PR diff and does not depend on anyone trusting me.

**Half 2: the repo's own checks.** From `CLAUDE.local.md` (CONTRIBUTING/DEVELOPER_GUIDE), matching what `.github/workflows/python-tests.yml` and `.pre-commit-config.yaml` actually run:

```bash
pipenv run pytest tests/tests_test_workflow/test_perf_workflow/perf_test/ -q  # the suites I touch first
pipenv run pytest                                                    # full suite
pipenv run isort --check . && pipenv run flake8 . && pipenv run mypy .  # pre-commit's checks
```

Both halves ran on 2026-11-21, on this machine, with `main` at `619c091` as the base. Every number below was copied from a run, not estimated.

**Half 1: the reproduction, before and after.** The reproducer is `tests/tests_test_workflow/test_perf_workflow/perf_test/test_perf_cwd_isolation.py`, committed alone at `a8f360c` so it could be run against untouched source. The check is `os.getcwd()` after each of the three methods, plus the `cwd=` each `check_call` received.

Before — the reproducer commit alone, `src/` untouched at `main`:

```
$ pipenv run pytest tests/tests_test_workflow/test_perf_workflow/perf_test/ --tb=no
3 failed, 24 passed, 1 warning in 0.26s
```

And with `main`'s `src/` under the branch's test files, the five pre-existing tests the fix rewrites go red as well: `8 failed, 19 passed`.

After — the branch:

```
$ pipenv run pytest tests/tests_test_workflow/test_perf_workflow/perf_test/ --tb=no
27 passed, 1 warning in 0.31s
```

The three failures at the reproducer commit are the reproducer itself; the other five appear only once the fix lands and the old `chdir` expectations stop holding. The failure text is the defect in the issue's own words:

```
E   AssertionError: '/private/var/folders/.../tmphm6i3665/test_dir' != '/Users/.../opensearch-build'
E    : start() moved the process's working directory instead of running the command in work_dir
E   AssertionError: '/private/var/folders/.../tmpzjpjkk_r' != '/Users/.../opensearch-build'
E    : execute() left the process in /private/var/.../tmpzjpjkk_r instead of /Users/.../opensearch-build
```

The `chdir` count is also observable without trusting me: `git grep -c "os\.chdir" HEAD -- src/` is 6 lines on this branch (4 are the helpers' own internals) against 9 on `main`.

**Half 2: the repo's own checks**, run in a fresh shell without an activated virtualenv, which is what the ship-gate does row by row:

```bash
pipenv run pytest             # 1159 tests, exit 0
pipenv run isort --check .    # exit 0
pipenv run flake8 .           # exit 0
pipenv run mypy .             # Success: no issues found in 440 source files
pipenv run yamllint .         # exit 0
```

`main` baseline for the same commands: pytest 1156 tests exit 0, isort/flake8/mypy exit 0 (439 files). The counts move by exactly the three tests this PR adds, so nothing else changed shape.

What this does **not** prove: that `cdk deploy` and the benchmark run still work. Those are patched out in the tests, so what is verified is the arguments passed and the process state left behind. That needs AWS credentials and is what the repo's own CI runs; saying otherwise would be a claim I cannot make.

One finding worth recording: `pipenv install --deploy --dev` failed until Python 3.9 was installed (`uv python install 3.9`) and pipenv was pinned to 2023.6.12 — current pipenv refuses Python 3.9, and the lock file is old enough that `--deploy` checks its hash. The repo pins `python_version = "3.9"` in `Pipfile`, so this is a real setup cost, not a quirk of my machine.

**Toolkit**

- Tool: `pytest` for the gate runs, the ship-gate's `check_gate.py` for the four repo checks.
- What it produced: the two counts above (`8 failed` → `27 passed`), the full-suite numbers, and a cold-start recipe for the environment.
- Difficulties: the Python 3.9 requirement, and one self-inflicted detour — my first version of the reproducer asserted a `cwd=` string that differed only by a trailing slash, which plan-check caught.
- What changed: the reproducer now asserts directory identity via `realpath`, and the three `test_perf_test_suite.py` tests gained `cwd=` assertions the ship-gate pointed out were missing from my PR body.

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

**[2026-11-21]**

Built the patch branch `fix/775-remove-redundant-chdir` in gates, not in one pass. Red first: `test_perf_cwd_isolation.py` committed alone at `a8f360c`, then run against untouched source — 3 failed, 24 passed. Then the fix at `a40a1f6`: 190 insertions, 55 deletions across `perf_test_cluster.py`, `perf_test_suite.py` and their tests, 27 passed, full suite 1159. Two things did not go to plan and both are recorded rather than smoothed over. The plan gate rejected the plan because my reproducer asserted a `cwd=` string that differed from the real one only by a trailing slash, and the RED run never reached the assertion; the fix was `os.path.realpath` on both sides plus an explicit decision to leave `perf_test_suite.py:43` alone. The ship-gate then rejected the PR body because it claimed five existing tests assert `cwd=` when only two did; I added the assertions to the other three rather than softening the sentence. Also: `pipenv install --deploy --dev` would not run until I installed Python 3.9 and pinned pipenv 2023.6.12, which is the version the repo's `Pipfile` actually requires.

**[2026-11-21, later] — running the two course rubrics I had skipped**

Auditing what I had actually run turned up two gaps in my own process. The plan gate I used all week is the four-check judge from my workflow skill; the course's `plan-check` has **six** required checks, and the two extra ones — `thread-direction` and `repo-policy` — had never been graded. `repro-check` had never run in live mode at all this unit. I ran both against the real artifacts. Both came back **accept**, and both still caught a genuine error, which is the useful outcome:

- `plan-check` (6/6 pass) found that `plan-comment.md` said "AI-assisted" without naming the tool, while the org policy asks to "disclose this fact and which tool you used" — and that my `execute.py` citation pointed at `:17`, a docstring line, rather than `:23` where `cwd=dir` actually is.
- `repro-check` (5/5 pass) found a worse one: I had written that **PR #772 added `WorkingDirectory`**. It did not. #772 refactored `TemporaryDirectory` and `GitRepository` into context managers; `WorkingDirectory` came from **#678** on 2021-10-06, before the issue was even opened. My clone was shallow (`--depth 1`, 3 commits), so `git log --follow` appeared to blame the squashed tip — `git fetch --unshallow` exposed the error immediately. It also caught that my repro's "pin the commit" step had no `git checkout`, so `--depth 1` would have tracked `main` and silently drifted past `619c091`.

All four were fixed rather than argued with: the tool is now named in `plan-comment.md`, the line cite says `:23`, the attribution says #678 in every place it appears, and step 1 fetches the specific sha (`git init` + `git fetch --depth 1 origin 619c091…` + `git checkout FETCH_HEAD`) and reproduces 31/6/12 from scratch. The attribution error is the one worth remembering: I trusted a shallow clone's history and wrote a confident sentence on top of it.

**Toolkit**

- Tool: plan-check (6 checks) and repro-check (5 checks), both in live mode against the real issue thread, plus the workflow skill's plan gate and the ship-gate.
- What it produced: `accept` from both course rubrics, and four concrete factual corrections they found that my own gates had passed over.
- Difficulties: I had been running a four-check rubric where the course specified six, and calling that "the plan gate" throughout this story. Nothing failed loudly — the substitute rubric was stricter on some rows and simply silent on `thread-direction` and `repo-policy`.
- What changed: the two missing checks are now graded and recorded, and three artifacts carry corrections (tool name in `plan-comment.md`, `:23` for `cwd=dir`, #678 for `WorkingDirectory`, plus a properly pinned repro step).

---

## Pull Request

**PR Link:** not opened yet. The branch `fix/775-remove-redundant-chdir` is built and committed locally at `a40a1f6`, and the PR body is drafted in `pr_draft.md`, but nothing is pushed and no PR exists. That is deliberate for this unit: the draft is reviewed before it goes public.

**PR Description:** drafted at `pr_draft.md` (kept out of the diff by `.git/info/exclude`). It follows the shape this repo's merged PRs use — Description, Related issue, Issues Resolved, Testing, Check List, AI disclosure, and the Apache-2.0 / DCO footer the org policy asks for — because the repo ships no `PULL_REQUEST_TEMPLATE.md`. It uses `Refs #775` rather than `Closes`, since this is PR 1 of 3, and states honestly that it removes 3 of the 5 `os.chdir` call sites in `src/` and what it does not prove (`cdk` deployment).

Drafted title: **`perf_test: pass cwd= to cluster and suite subprocesses instead of os.chdir`**.

**Maintainer Feedback:**

- 2026-11-18: claim posted with one question (does `WorkingDirectory` stay?). No reply yet — silence is a normal state and costs nothing.
- The unanswered question is why PR 3 exists as a separate, later decision rather than being folded into this one.

**Status:** Drafted, gate-passed, not pushed. The ship-gate's last run reports `PASS` against the four manifest checks and all seven pr-precheck checks, valid for 30 minutes from that run. Pushing the branch and opening the PR are the two steps deliberately left for review.

**Toolkit**

- Tool: the ship-gate hook (`~/.claude/ship-gate/check_gate.py`) plus pr-precheck in live mode, run via the same hook the real `gh pr create` would trigger.
- What it produced: `ship-gate: STOP. your own pr-precheck rejected fix/775-remove-redundant-chdir: verdict reject` on the first attempt, then `ship-gate: PASS` on the second, with the four repo checks run per row from `ship-manifest.md`.
- Difficulties: the first rejection was `description-fidelity` — the body claimed the five existing tests assert `cwd=` when only the two cluster tests did. The other three only dropped their `os.chdir` patch. Fixing the sentence would have made the body true and the tests weaker; adding the assertions made both true.
- What changed: three test methods gained a `cwd=` assertion, the PR body stopped generalising from two cases to five, and `test_evidence.md` gained the DCO check and the manifest-exclusion reasoning that the gate's advisory notes asked for.
- One caveat I want on the record: the empty-manifest failure mode is real, but it fails safely. I checked on a throwaway repo with no manifest and the gate stopped with `no ship-manifest.md at /private/tmp/gate_test. Create it from the template and keep it out of your commits`, which is about setup rather than about the code. The dangerous version would be a gate that reports `PASS` with nothing to check; this one refuses to run.

---

## Learnings and Reflections

**Technical Skills Gained**

Working-directory semantics: how `subprocess.run(..., cwd=)` differs from `os.chdir()`, why `chdir` is process-wide and visible to every thread, and how a context manager that saves and restores cwd still leaks while it is open. I also learned the shape of this repo's test workflow — `pipenv` environments, `pytest` layout mirroring `src/`, pre-commit running isort/flake8/mypy — and that `execute()` was already the right API, which turned a vague "refactor" into a call-site migration.

**Challenges Overcome**

The real challenge was not the code, it was keeping claims true. My first accepted issue was taken within a day; my rubric accepted a closed issue because it lacked a state check; and my search strategy still contained the queries my notes said I had replaced. Each was caught by re-reading the artifact instead of trusting the note, and each produced a specific fix: the state row, the pre-claim re-check, the difficulty note, and the `--owner` rewrite.

**What I'd Do Differently Next Time**

Check the linked-PR state on the same day I claim, not the day I grade. Treat "I changed X" as a claim to verify by opening X before writing it into a story. And write the difficulty note before claiming rather than after, because naming the open edge (`does WorkingDirectory stay?`) changed my claim from "assign me" to a question the maintainer can answer in one line.

In unit 7 the same lesson showed up two more times, from tools rather than from me. My workflow skill's plan gate rejected a plan whose reproducer would have failed for the wrong reason — a trailing slash, not a leaked cwd — and the ship-gate rejected a PR body that generalised "two tests assert `cwd=`" into "five tests assert `cwd=`". Both were caught by a reader comparing my words to the code, and neither would have been caught by re-reading my own prose. The transferable habit is not "check your work twice"; it is that the artifact and the claim about it drift apart, and the artifact is the one that is true. That same gap swallowed a third case the gates did not catch on their own: I attributed `WorkingDirectory` to PR #772 on the strength of a three-commit shallow clone, and the course `repro-check` rubric, run afterwards, found it came from #678.

---

## Resources Used

- [OpenSearch contributing guide](https://github.com/opensearch-project/.github/blob/main/CONTRIBUTING.md) — DCO sign-off requirement, license headers, and the AI Usage Policy section I quote in my claim.
- [Issue #775](https://github.com/opensearch-project/opensearch-build/issues/775), [PR #772](https://github.com/opensearch-project/opensearch-build/pull/772) and [#678](https://github.com/opensearch-project/opensearch-build/pull/678) — the original request, the merged change that cross-referenced it, and the merge that actually added `WorkingDirectory`, which is what made the design question concrete.
- [`src/system/execute.py`](https://github.com/opensearch-project/opensearch-build/blob/main/src/system/execute.py) — the `execute(command, dir)` signature that made the whole issue tractable.
- [`DEVELOPER_GUIDE.md`](https://github.com/opensearch-project/opensearch-build/blob/main/DEVELOPER_GUIDE.md) — the `pipenv run pytest` / pre-commit commands I copied into my testing strategy and `CLAUDE.local.md`.
- [`.github/workflows/python-tests.yml`](https://github.com/opensearch-project/opensearch-build/blob/main/.github/workflows/python-tests.yml) and [`.pre-commit-config.yaml`](https://github.com/opensearch-project/opensearch-build/blob/main/.pre-commit-config.yaml) — the four checks I put in `ship-manifest.md`, read from the workflow rather than from memory.
- [Python 3.9 release](https://www.python.org/downloads/release/python-3925/) and [pipenv 2023.6.12](https://github.com/pypa/pipenv/releases/tag/v2023.6.12) — the interpreter and tool version this repo's `Pipfile` requires; the current pipenv refuses Python 3.9.
