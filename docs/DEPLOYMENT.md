# Deployment

## Render

`render.yaml` provisions a Docker web service and a separate PostgreSQL database, both on the free plan. No secrets are stored in the repository.

1. In Render, choose **New → Blueprint** and select this repository and `render.yaml`.
2. Check that both the web service and the database are on the Free plan.
3. Set `APP_API_KEY` and `TRACKINGMORE_API_KEY`.
4. Deploy. Render supplies `DATABASE_URL` automatically.
5. For voice calls, add `VAPI_PUBLIC_KEY` and `VAPI_ASSISTANT_ID`, redeploy, then run `configure_vapi.py` locally with `PUBLIC_API_URL` set to the service URL. Never upload `VAPI_PRIVATE_KEY`.

Don't use `/health` as Render's HTTP health check, because it requires authentication. The default port check works.

Free-tier limits: web services sleep after 15 minutes idle, so warm the service before a call or tool calls may time out. The free PostgreSQL database expires after 30 days and has no backups. See https://render.com/docs/free.

## Demo access key

`REVIEW_ACCESS_KEY` gives browser-only access for demos: the dashboard, customer calls and the support desk, but not the tool APIs, health check or webhooks. It must be 32 to 200 characters and different from `APP_API_KEY`. Changing or removing it signs out existing browser sessions after the next deploy. Never share `APP_API_KEY` or provider keys.

## Live tracking (TrackingMore)

Register TEST Carrier records in your TrackingMore account with courier code `test-carrier`. In odd months the seeded numbers are `TEST1234123421`, `TEST1234123441`, `TEST1234123461` and `TEST1234123431`; in even months the prefix is `TEST2`. Fetching an unregistered number doesn't create it. The adapter uses TrackingMore v4 with the `Tracking-Api-Key` header.

Test-carrier checkpoints are canned data, so their dates can be older than the mock order dates. The agent reports the carrier's dates as given.

## Local

SQLite is the default. Set `DATABASE_URL` only to use PostgreSQL; otherwise `DATABASE_PATH` is used. Run the tests without `DATABASE_URL` set, since they create their own SQLite databases. `docker compose up --build` keeps local SQLite in a named volume.
