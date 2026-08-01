---
name: Trigger a build and monitor a deploy
description: Build a Docker image for a Release app, watch the build logs, then run and monitor a deploy pipeline.
api: openapi/release-openapi-original.json
base_url: https://api.release.com
auth: X-User-Email + X-User-Token headers (from ~/.release.yaml)
operations: [builds_create, builds_show, builds_log, pipelines_create, pipelines_show]
---

# Trigger a build and monitor a deploy

## Steps
1. **Start a build** — `POST /apps/{app_id}/builds` (`builds_create`). Capture the build `id`.
2. **Poll build status** — `GET /apps/{app_id}/builds/{id}` (`builds_show`) until the build completes.
3. **Stream build logs** — `GET /apps/{app_id}/builds/{id}/log` (`builds_log`) to surface build output for debugging.
4. **Deploy** — `POST /apps/{app_id}/spaces/{space_id}/pipelines` (`pipelines_create`) to run the deploy pipeline into the target Space.
5. **Monitor the deploy** — `GET /apps/{app_id}/spaces/{space_id}/pipelines/{id}` (`pipelines_show`) until the pipeline reaches a terminal state.

## Notes
- If a build must be stopped, `PUT /apps/{app_id}/builds/{id}/cancel` (`builds_cancel`); to retry, `POST /apps/{app_id}/builds/{id}/rebuild` (`builds_rebuild`).
- To abort an in-flight deploy: `PUT /apps/{app_id}/spaces/{space_id}/pipelines/{id}/abort` (`pipelines_abort`).
- Errors are `{"error": "..."}` / `{"errors": [...]}`; auth failures return `401`.
