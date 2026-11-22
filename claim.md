Picking this up to investigate: the health check probe in `api/routes/health.py` fails with an AttributeError when trying to read `settings.redis_host` and `redis_port` instead of `settings.redis_url`. 

I am setting up the local environment now to reproduce the 503 response and will post a reproduction report with full environment details and logs shortly.