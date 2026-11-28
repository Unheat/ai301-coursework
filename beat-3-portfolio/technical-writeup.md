# Removing three os.chdir call sites from opensearch-build, with a test that failed first (PR #6573)

## Overview

opensearch-build is the repository that builds, tests, and releases OpenSearch, and its issue [#775](https://github.com/opensearch-project/opensearch-build/issues/775), open since 2021, asks it to stop calling `os.chdir()`, which moves the working directory of the whole process and every thread in it, and to pass directories to each command explicitly instead. I claimed the issue on 2026-11-18 with the one design question that keeps it from being pure cleanup, posted a reproduction report against `main` at `619c091` on 2026-11-22, and opened [PR #6573](https://github.com/opensearch-project/opensearch-build/pull/6573) the same day. The PR removes the three redundant call sites in the perf-test workflow: a test committed on its own fails against the unfixed code, then `PerfTestCluster.start()`, `PerfTestCluster.terminate()` and `PerfTestSuite.execute()` pass `cwd=` to their subprocesses instead of touching the process directory, and five existing tests are rewritten to assert the explicit directory. It is open and awaiting review, with no maintainer response yet. The tooling around it is mine as well: the issue-scout search strategy and rubric that picked and graded the issue, the repro-check and plan-check rubric runs against my report and plan, the ship-manifest rows the ship-gate executed before I opened the PR, and the monitor playbook that watches the thread now.

## What happened

### Finding an issue that was still free

My issue-scout skill pairs GitHub CLI searches with ranking filters and a grading rubric I wrote. Its first accepted pick this term, `pytorch/pytorch#113040`, was contested within a day, when an open PR implementing the same scope appeared and the "nobody already working on it" check stopped passing. The re-pick was opensearch-build#775: open since 2021, no assignee, no claim comment, no linked PR, a `good first issue` label, and a repository pushed to daily. The scout graded it **accept** with the difficulty note **ambitious**, because one design question is open: `WorkingDirectory`, a context manager whose job is to chdir and restore, predates the issue by seven weeks, and "remove all chdir" does not say whether it stays. I asked that question in my [claim](https://github.com/opensearch-project/opensearch-build/issues/775#issuecomment-6999931973) instead of guessing.

The scout itself needed a fix that week. Direct grading had accepted a closed issue, `pytorch/pytorch#97469`, because my rubric had no check for issue state. From my scan notes, dated 2026-11-11:

> **Friction observed:** direct grading accepted a closed issue (`pytorch/pytorch#97469`) because the rubric had no check for issue state.

I added a required "Issue still open and unfixed" check to `rubric.md`, re-ran the 20-bundle eval (20/20), and re-graded: `#97469` now rejects on the new check, and `#113040` stays accepted. That re-run is the tool catching, one run after the fix, exactly what it had missed before.

### Reproducing it on main at 619c091

The reproduction ran on a shallow clone of `main` at `619c091b`, macOS arm64, Python 3.12. A thread started inside a `WorkingDirectory` block printed the directory the block had changed into, not the one the process started in, which is the leak the issue describes. I posted the [report](https://github.com/opensearch-project/opensearch-build/issues/775#issuecomment-7076311129) as the thread's second comment on 2026-11-22.

The report's attribution took three tries, and both corrections came from my own repro-check rubric. I first wrote that PR #772 added `WorkingDirectory`; a shallow clone (`--depth 1`, three commits) made `git log --follow` blame the squashed tip. I corrected to #678; the second pass caught that too, because the PR's file list shows `working_directory.py` at `status: renamed`, `+0/-0`. A rename is not an addition. `git log --all --diff-filter=A` gives one hit: #314, 2021-09-03. Two graders, two wrong answers from me, one lesson: ask for the creation event, not the last change. My voice guide now carries a rule about verifying an attribution with `--diff-filter=A` before naming a PR.

### The red test and two gate rejections

I built the branch in gates rather than in one pass. The reproducer, `test_perf_cwd_isolation.py`, went in alone at `a8f360c` and ran against untouched source:

```text
$ git checkout a8f360c && pipenv run pytest tests/tests_test_workflow/test_perf_workflow/perf_test/ --tb=no
3 failed, 24 passed, 1 warning in 0.26s
FAILED test_perf_cwd_isolation.py::TestPerfTestCwdIsolation::test_cluster_start_does_not_change_process_cwd
FAILED test_perf_cwd_isolation.py::TestPerfTestCwdIsolation::test_cluster_terminate_does_not_change_process_cwd
FAILED test_perf_cwd_isolation.py::TestPerfTestCwdIsolation::test_suite_execute_does_not_change_process_cwd
```

Three failures, all the bug, before any source line changed. With `main`'s `src/` under the branch's final test files, the five pre-existing tests whose expectations the fix rewrites go red too: `8 failed, 19 passed`. On the branch at `a40a1f6`, the same directory reports `27 passed`, and the repo's own checks come back clean: full suite 1159 tests exit 0 against 1156 on `main`, with isort, flake8 and mypy clean. That before-and-after is the contribution's proof, and it shows no more than it claims: the tests patch `subprocess.check_call`, so what they prove is the arguments each command received and the process state left behind.

Two gates rejected my own work that night, and both rejections were about a gap between what I had written and what was there. The plan gate flagged a defect I had missed: `perf_test_suite.py:43` builds `work_dir` as `current_workspace + "/mensor/"`, so the directory is `/ws/mensor/` with a trailing slash, while my reproducer compared `/ws/mensor` as a string. The red run never reached that assertion because the `os.getcwd()` check fires first, so my "3 failed, then all passed" claim would not have held after the fix. I changed the assertion to compare `os.path.realpath` on both sides, which keeps the directory-identity guarantee without depending on spelling, and recorded that the trailing slash stays, because normalizing it would smuggle an unrelated change into a chdir PR.

The ship-gate rejected the PR itself. My body said five existing tests now assert the explicit `cwd=`; only the two cluster tests did:

```text
## 2026-11-21 18:34 [manual] opensearch-project/opensearch-build @ fix/775-remove-redundant-chdir (base main)
- verdict: BLOCK (block)
- pr-precheck checks:
  - description-fidelity [fail]: Description says 'five existing tests ... now assert the explicit cwd=',
    but only the two cluster tests do; the three test_perf_test_suite tests just drop the os.chdir patch
    and gain no cwd= assertion.

## 2026-11-21 18:39 [manual] opensearch-project/opensearch-build @ fix/775-remove-redundant-chdir (base main)
- verdict: PASS
- pr-precheck checks:
  - description-fidelity [pass]: Every bullet maps to a hunk ...
  - decisive-testing [pass]: Before (src from main): '8 failed, 19 passed' ... after: '27 passed' ...
```

Rewording the sentence would have made the body true and the tests weaker, so I added the `cwd=` assertions to the three suite tests instead, and the second gate run passed all seven pr-precheck checks. That gate log is the tool's proof: the same seven checks, run twice, once failing on a real mismatch and once passing after the tests moved to match the description.

### Opening the PR and watching it

PR #6573 opened on 2026-11-22 against `main` from `fix/775-remove-redundant-chdir`: five files, +190/−55, two commits (`a8f360c` reproducer, `a40a1f6` fix), DCO pass, both Code-Diff checks green. The body says `Refs #775` rather than `Fixes #775`, because two call sites in `integ_test` are still open and the issue should outlive this PR.

Where it stands today, checked 2026-11-27: open, review decision `REVIEW_REQUIRED`, zero human comments and zero reviews. The workflow runs a first-time contributor triggers (`python-tests`, license and link checks) sit in `action_required` until a maintainer approves them. PR-Agent commented within a minute with one "possible issue", claiming the removed chdir changes what `os.getcwd()` resolves to at `perf_test_suite.py:55`. I checked it against the diff instead of taking it: the line is outside both hunks and byte-identical on both branches, and the old chdir ran in `execute()`'s `finally`, after `__init__` had already captured `os.getcwd()` while building the command. No behavior change, so no code change and no reply; the `os.getcwd()` fallback it pointed at is a third instance of the same pattern the issue is about, and it is written down as scope for the follow-up.

The PR monitor's first run the same day read the thread correctly (0 comments, 0 reviews, one pending check) and drafted a wait note instead of a nudge, which exposed a hole in the playbook I wrote: the nudge rule said to tag the reviewer who last engaged, and this PR has no reviewer, which is the state every PR starts in. The rule now tags nobody when nobody has engaged, and the playbook names the date the wait threshold is next met, 2026-11-29.

## Limitations and what's next

The tests patch `subprocess.check_call`, so what they prove is the arguments each command received and the process state left behind. They do not prove that a real `cdk deploy` and benchmark run still work; that needs AWS credentials and is what the repository's own CI runs. The PR also removes 3 of the 5 raw `os.chdir` call sites in `src/` on purpose: the two `integ_test` sites stay until a test shows `ScriptFinder.find_integ_test_script()` returns an absolute path, and the `WorkingDirectory`/`TemporaryDirectory` contract stays until a maintainer answers the question my claim asked.

Next follows from those limits. If the maintainer confirms the helper's fate, the follow-up PR proves the script path is absolute, converts the two `integ_test` sites, and takes the `os.getcwd()` fallback with it. If the thread is still silent on 2026-11-29, the revised nudge rule applies, once, with the reproduction report already in the thread as the artifact to point at. And the next attribution I make gets `--diff-filter=A` on the first try, not the third.
