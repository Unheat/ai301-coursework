## Environment

- OS: macOS 27.0.0 (Darwin), arm64
- Python: 3.12.12
- Commit: `2f4e82f52efbcfcc57d65b3fa5348672163ca088` (upstream `main`, `codepath/pathreview-ai301-fa26-s3`)
- Key packages (installed via `pip install -e .` into a fresh venv): `redis` 8.1.0, `pydantic-settings` 2.15.0, `fastapi` 0.142.2

Docker wasn't available in this environment, so I couldn't bring up the full `docker-compose` stack (Postgres + Redis) to hit `GET /health` end-to-end. Instead I exercised the exact Redis-check block from `api/routes/health.py` (lines 40-58) in isolation, importing the real `core.config.settings` singleton and running the same `redis.Redis(...)` construction the route uses. This reproduces the identical code path and exception, independent of whether Redis itself is reachable, since the crash happens while building the client before any network call.

## Steps to reproduce

1. `git clone https://github.com/codepath/pathreview-ai301-fa26-s3.git && cd pathreview-ai301-fa26-s3`
2. `git checkout 2f4e82f52efbcfcc57d65b3fa5348672163ca088`
3. `python3 -m venv .venv && .venv/bin/pip install -e .`
4. Run the Redis-check block exactly as it appears in `api/routes/health.py`:

```bash
.venv/bin/python -c "
import redis
from core.config import settings

try:
    r = redis.Redis(
        host=settings.redis_host,
        port=settings.redis_port,
        db=0,
        decode_responses=True,
    )
    r.ping()
    print('redis_health_check_passed')
except Exception as exc:
    import traceback
    traceback.print_exc()
    print('redis_health_check_failed error=' + str(exc))
"
```

## Expected vs. actual

- **Expected** (per issue): the probe should build its Redis client from `settings.redis_url` and report `redis: healthy` when Redis is reachable.
- **Actual**: `Settings` only defines `redis_url` (see `core/config.py:14`), not `redis_host`/`redis_port`. Accessing either attribute raises `AttributeError`, which `health.py`'s broad `except Exception` swallows and reports as `redis: unhealthy` — producing a 503 even when Redis itself is up and reachable.

## Actual terminal output

```
Traceback (most recent call last):
  File "<string>", line 7, in <module>
  File ".../pathreview-ai301-fa26-s3/.venv/lib/python3.12/site-packages/pydantic/main.py", line 1042, in __getattr__
    raise AttributeError(f'{type(self).__name__!r} object has no attribute {item!r}')
AttributeError: 'Settings' object has no attribute 'redis_host'
redis_health_check_failed error='Settings' object has no attribute 'redis_host'
```

This matches the issue's reported log event (`redis_health_check_failed`) and traceback (`AttributeError: 'Settings' object has no attribute 'redis_host'`) exactly. The fix is to build the client from `settings.redis_url` (e.g. `redis.Redis.from_url(settings.redis_url, decode_responses=True)`) instead of the nonexistent `redis_host`/`redis_port` fields.
