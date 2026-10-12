# Plan: fix Redis health probe to use `settings.redis_url` (issue #62)

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/62
Branch: `fix/62-redis-url-health-probe`

## Diagnosis

The Redis probe in `api/routes/health.py` (lines 46–51) builds its
client from fields that do not exist on the settings object:

```python
r = redis.Redis(
    host=settings.redis_host,
    port=settings.redis_port,
    db=0,
    decode_responses=True,
)
```

`Settings` in `core/config.py` defines only `redis_url`
(`redis://localhost:6379/0`); there is no `redis_host` or `redis_port`
field. Touching `settings.redis_host` raises
`AttributeError: 'Settings' object has no attribute 'redis_host'`
before any network call is made. The block's broad
`except Exception` catches it, logs `redis_health_check_failed`, and
marks `redis` as `unhealthy`, so `GET /health` answers 503 even when
Redis itself is up and reachable.

Reproduction evidence (my posted repro comment on the issue, commit
`2f4e82f52efbcfcc57d65b3fa5348672163ca088`, upstream `main`):

> Docker wasn't available in this environment, so I couldn't bring up
> the full `docker-compose` stack (Postgres + Redis) to hit `GET
> /health` end-to-end. Instead I exercised the exact Redis-check block
> from `api/routes/health.py` (lines 40-58) in isolation, importing
> the real `core.config.settings` singleton and running the same
> `redis.Redis(...)` construction the route uses. This reproduces the
> identical code path and exception, independent of whether Redis
> itself is reachable, since the crash happens while building the
> client before any network call.

> ```
> Traceback (most recent call last):
>   File "<string>", line 7, in <module>
>   File ".../pathreview-ai301-fa26-s3/.venv/lib/python3.12/site-packages/pydantic/main.py", line 1042, in __getattr__
>     raise AttributeError(f'{type(self).__name__!r} object has no attribute {item!r}')
> AttributeError: 'Settings' object has no attribute 'redis_host'
> redis_health_check_failed error='Settings' object has no attribute 'redis_host'
> ```

Honest scope of that evidence: the repro exercised the route's exact
Redis-check code path (same `settings` singleton, same
`redis.Redis(...)` construction), not the end-to-end HTTP endpoint, so
the 503 response body itself was not observed directly. The diagnosis
does not depend on the HTTP layer: the AttributeError occurs while
building the client, before any network I/O. The unit test added below
runs the real route function, which closes that gap.

## Scope

**In:**
- The Redis probe's client construction in `api/routes/health.py`.
- Removal of this bug's mypy suppression in `pyproject.toml`
  (`attr-defined` for `api.routes.health`).
- A new unit test pinning the probe to `settings.redis_url` and
  covering both the healthy and unhealthy paths.

**Out:**
- The `call-overload` and `index` codes in the same
  `[[tool.mypy.overrides]]` entry: per the comment in `pyproject.toml`
  and `docs/CONTRIBUTING.md`, they cover the separate seeded bug in
  the Postgres probe (issue #61) and stay until that issue's fix.
- Any change to how the Postgres or Vector DB probes report health.
- Any change to `core/config.py` — `redis_url` is correct as-is.

## Files to touch

- `api/routes/health.py` — Redis block only.
- `pyproject.toml` — remove `attr-defined` from the
  `api.routes.health` override's `disable_error_code` list.
- `tests/unit/test_health_redis.py` — new file.

## Approach

Replace the broken construction with a URL-based one:

```python
r = redis.Redis.from_url(settings.redis_url, decode_responses=True)
```

`redis_url` already carries the db number (`redis://localhost:6379/0`,
matching `REDIS_URL` in `.env.example`), so no separate `db=` argument
is needed. `decode_responses=True` is preserved so callers that expect
`str` responses see no behavior change.

Per `docs/CONTRIBUTING.md` ("The same applies to the baseline
suppressions in `pyproject.toml` … If you fix one of those, remove its
suppression too"), the `attr-defined` code is removed from the
`api.routes.health` override in `pyproject.toml`; `call-overload` and
`index` remain for issue #61.

## Test plan

New `tests/unit/test_health_redis.py` calls the real route function
(`health_check`) directly with a mocked db session, monkeypatching
`redis.Redis.from_url`:

1. **The fix's observable flip:** with `from_url` patched to return a
   client whose `ping()` succeeds, assert that `from_url` received
   `settings.redis_url` and that
   `health_status["dependencies"]["redis"] == "healthy"`. On unfixed
   `main` this fails — the probe never reaches a client call and
   reports `unhealthy`, which is the issue's 503-while-Redis-is-up
   behavior; after the fix it passes.
2. **Failure path guard:** with `ping()` raising
   `redis.ConnectionError`, assert `redis` reports `unhealthy` and the
   route raises `HTTPException` 503. (This can pass on `main` too; it
   pins the unhealthy path so the fix cannot silently swallow real
   outages.)
3. **Suppression removal check:** `mypy api/routes/health.py` with
   `attr-defined` removed errors on `main` (`Settings` has no
   attribute `redis_host`) and is clean after the fix.
4. **Suite stays green:** run the unit test suite; `main` is kept
   green on purpose per `docs/CONTRIBUTING.md`.

Steps 1–3 run against the real route function and the real
`core.config.settings`, so they execute the actual fixed code rather
than a stand-in copy.

## Risks and unknowns

- Deployments that set `REDIS_HOST`/`REDIS_PORT` env vars were never
  being read (`Settings` never defined those fields), so there is no
  working configuration to preserve; `redis_url` is the single source
  of truth. No migration needed.
- If any code elsewhere assumed the health probe's Redis client used
  separate host/port, that code would already be broken today; I will
  grep to confirm nothing else references `redis_host`/`redis_port`.
- Unknown: whether the CI mypy gate surfaces anything beyond the
  removed `attr-defined` for this module; will confirm when the PR
  runs.

## Deviations

The built change matches the posted plan in substance: same three
files, same approach (`redis.Redis.from_url(settings.redis_url,
decode_responses=True)`), only `attr-defined` removed from the
`api.routes.health` override, and the test plan executed as written
(regression test failing on unfixed `main` with the issue's exact
503/`unhealthy` body, passing after; mypy erroring with the
suppression stripped on `main`, clean after; unit suite 377 passed /
53 xfailed). Two small additions beyond the plan's letter, neither
changing the approach: (1) while removing `attr-defined`, I also
updated the explanatory comment block above that override in
`pyproject.toml`, which named the now-removed `attr-defined` -> #62
entry and would have gone stale; (2) I ran the plan's risk-check grep
and confirmed nothing else in the codebase references
`redis_host`/`redis_port`. Re-ran plan-check on this updated plan:
verdict accept.
