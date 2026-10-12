# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

Unheat

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-6115718606

> Plan for #62, built on my repro above (commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`).
>
> **Diagnosis.** The Redis probe in `api/routes/health.py` (lines 46–51) builds its client with `redis.Redis(host=settings.redis_host, port=settings.redis_port, db=0, decode_responses=True)`. `Settings` in `core/config.py` defines only `redis_url` — there is no `redis_host` or `redis_port` field — so reading `settings.redis_host` raises `AttributeError: 'Settings' object has no attribute 'redis_host'` before any network call. The block's broad `except Exception` catches it, logs `redis_health_check_failed`, and marks Redis `unhealthy`, so `GET /health` returns 503 regardless of whether Redis itself is reachable — the crash happens while building the client, before any network call. My repro shows exactly that: the route's own Redis block, run against the real `settings` singleton (commit `2f4e82f`), fails with that AttributeError, independent of Redis's reachability.
>
> **Scope.** In: the Redis probe's client construction in `api/routes/health.py`, removal of the `attr-defined` mypy suppression on `api.routes.health` in `pyproject.toml`, and a new unit test pinning the probe to `settings.redis_url`. Out: the `call-overload` and `index` codes on the same `[[tool.mypy.overrides]]` entry (they cover the separate seeded bug in the Postgres probe, issue #61) and any change to how the other probes report health.
>
> **Files.** `api/routes/health.py` (Redis block), `pyproject.toml` (drop only `attr-defined` from the `api.routes.health` override), `tests/unit/test_health_redis.py` (new).
>
> **Approach.** Replace the `host=`/`port=` construction with `redis.Redis.from_url(settings.redis_url, decode_responses=True)`. The URL already carries the db (`redis://localhost:6379/0`, matching `REDIS_URL` in `.env.example`); `decode_responses=True` is preserved so callers see no change. Per `docs/CONTRIBUTING.md` ("If you fix one of those, remove its suppression too"), I'll drop only `attr-defined` from the `api.routes.health` mypy override, keeping `call-overload` and `index`, which #61 still needs.
>
> **Test plan.** New `tests/unit/test_health_redis.py` calls the real route function with a mocked db session and `redis.Redis.from_url` monkeypatched: (1) with `ping()` succeeding, assert `from_url` received `settings.redis_url` and `dependencies["redis"] == "healthy"` — this fails on unfixed `main` (the probe reports `unhealthy`, the issue's 503-while-Redis-is-up) and passes after; (2) with `ping()` raising `ConnectionError`, assert Redis reports `unhealthy` and the route raises 503, guarding the failure path; (3) `mypy api/routes/health.py` with `attr-defined` removed errors on `main` and is clean after; (4) the unit suite stays green.
>
> **Risks / unknowns.** `REDIS_HOST`/`REDIS_PORT` env vars were never read (`Settings` never defined those fields), so there's no working config to preserve — `redis_url` is the single source of truth, no migration needed. Unknown: whether CI's mypy gate surfaces anything beyond the removed `attr-defined`; I'll confirm on the PR.

---

## Your branch

**Branch**

fix/62-redis-url-health-probe

**Evidence**

My Unit 2 repro was a stand-in snippet that copied the route's Redis block, so per the
assignment I turned its inputs into a check that runs through the real code:
`tests/unit/test_health_redis.py` calls the real `health_check` route function with a
mocked db session. The before is on unfixed `main` (commit `2f4e82f`); the after is on
`fix/62-redis-url-health-probe` (commit `d0fb4eb`).

**Before — mypy with only `attr-defined` stripped from `pyproject.toml` (on `main`):**

```
$ perl -pi -e 's/disable_error_code = \["attr-defined", "call-overload", "index"\]/disable_error_code = ["call-overload", "index"]/' pyproject.toml
$ .venv/bin/python -m mypy api/routes/health.py
api/routes/health.py:47: error: "Settings" has no attribute "redis_host"  [attr-defined]
api/routes/health.py:48: error: "Settings" has no attribute "redis_port"  [attr-defined]
Found 2 errors in 1 file (checked 1 source file)
$ git checkout pyproject.toml
```

**Before — the regression test against the real route function (on `main`):**

```
$ .venv/bin/python -m pytest tests/unit/test_health_redis.py -v
...
FAILED tests/unit/test_health_redis.py::test_redis_probe_uses_settings_url - ...
=========================== short test summary info ============================
FAILED tests/unit/test_health_redis.py::test_redis_probe_uses_settings_url - ...
=================== 1 failed, 1 passed, 3 warnings in 1.73s ====================

failure detail (the issue's exact symptom, through the real route):
E           fastapi.exceptions.HTTPException: 503: {'status': 'unhealthy',
'dependencies': {'postgres': 'healthy', 'redis': 'unhealthy',
'vector_db': 'healthy'}, 'safety_events_last_hour': 0, ...}
```

The route raised 503 with `redis: unhealthy` even though the (mocked) Redis client's
`ping()` was set to succeed — on `main` the probe never reaches a client call, matching
the issue and my Unit 2 repro output (`AttributeError: 'Settings' object has no attribute
'redis_host'`, posted upstream at
https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62#issuecomment-5923444376).

**After — same checks on the fix branch:**

```
$ .venv/bin/python -m pytest tests/unit/test_health_redis.py -v
tests/unit/test_health_redis.py::test_redis_probe_uses_settings_url PASSED [ 50%]
tests/unit/test_health_redis.py::test_redis_probe_reports_unhealthy_on_connection_error PASSED [100%]
======================== 2 passed, 3 warnings in 0.44s =========================

$ .venv/bin/python -m mypy api/routes/health.py
Success: no issues found in 1 source file

$ .venv/bin/python -m pytest tests/unit -q
377 passed, 53 xfailed, 5 warnings in 8.94s
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Run 1 (full, 20 packages): 20/20 scored items (bar: 18/20: PASS), with every category
maxed: clear-accept 7/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3,
wrong-cause 4/4. A single run was sufficient, so no re-grades or canary runs were needed.
This matches the agreement line in the committed `eval-run.txt`:
`agreement: 20/20 scored items  (bar: 18/20: PASS)`. A `--limit 3` smoke run (pkg-01
through pkg-03, 3/3 agreement) preceded the full run; partial runs are never scored and
are not part of the bar.

**Package analysis**

`pkg-15` (source: `laurent22/joplin#16215`). Gold label: `reject`. My rubric's verdict:
`reject` — the run table shows `pkg-15  scope-creep  reject  reject  yes`.

My rubric failed it on `bounded-scope`. The plan contains one correct core fix (the
isolated one-constant change), but bundles it with an undici dependency migration, a
settings panel, UI rework, and a retry framework — several separately reviewable changes
where the issue asked for one, with no deferral lines carrying stated reasons. My check's
pass condition requires that "work beyond it is explicitly deferred with a stated reason
or ruled out by an out-of-scope line", so the bundled rework fails the check even though
the core fix inside it is right — which is exactly the "arguable because the core fix is
right" tension the gold label's note records. The discriminator against honest scoping
(pkg-09, pkg-14, both accepted by my rubric) is the stated reason: those plans defer work
*with* reasons, so they pass; pkg-15 bundles work *without* them, so it fails.

**Check rationale**

From the `rubric.md` uploaded to `tools/plan-check/rubric.md`:

> `bounded-scope` | The plan's change description and any in-scope/out-of-scope lines, read against the issue's reported problem. | Everything the plan changes serves the reported issue; work beyond it is explicitly deferred with a stated reason or ruled out by an out-of-scope line; fail when a fix for the reported issue is bundled with unrelated rework (refactors, migrations, redesigns, new features, while-in-the-area cleanup) into the same change. | required

I wrote the "deferred with a stated reason" clause deliberately, after reading the eval
set's composition: four packages punish bundling (pkg-06, pkg-12, pkg-15, pkg-19), but two
clear-accepts (pkg-09, pkg-14) *also* leave work out — they defer genuinely untestable or
maintainer-unrequested variants and say why. A first draft that failed any plan touching
anything beyond the minimal fix would have rejected those two. I rejected a looser version
that only counted work items ("fail if the plan contains more than one change") because it
cannot tell pkg-09's reasoned deferral from pkg-15's silent bundling; the reason line is
what separates honest scoping from scope creep, and that clause is what let one check
serve both sides.

**Trade-offs**

`bounded-scope` gives up precision on plans that bundle rework *with* plausible-sounding
reasons: a pkg-15 variant whose author wrote "we migrate undici while we're here because
the retry framework needs it" could pass my check where the gold label might still reject
it, because my pass condition accepts a stated reason as clearing the deferral bar and
does not grade whether the reason is *good*. I accept that miss rather than have the
grader second-guess authors' justifications, because the reverse error (rejecting honest
scoping) costs the two clear-accepts and is what the "deferred with a stated reason"
clause exists to prevent. How I know nothing else changed: there were no revisions — the
rubric went into Run 1 as uploaded and came out 20/20, so no check was ever loosened, no
`--only` re-grades or canary runs occurred, and `eval-run.txt` is that single first run.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
