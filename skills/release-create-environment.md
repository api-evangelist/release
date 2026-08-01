---
name: Create an app and spin up an ephemeral environment
description: Create a Release application, then create and deploy an on-demand ephemeral environment (Space) for it.
api: openapi/release-openapi-original.json
base_url: https://api.release.com
auth: X-User-Email + X-User-Token headers (from ~/.release.yaml)
operations: [apps_create, spaces_create, pipelines_create, spaces_show, instances_index]
---

# Create an app and spin up an ephemeral environment

On Release, an environment is called a **Space**. This skill creates an app and a Space, deploys it, then confirms it is running.

## Prerequisites
- Authenticate with `X-User-Email` and `X-User-Token` headers on every request (run `release auth login` to populate `~/.release.yaml`).
- Set `Content-Type: application/json` and `Accept: application/json`.
- IDs are hashid strings; timestamps are ISO 8601.

## Steps
1. **Create the application** — `POST /apps` (`apps_create`). Capture the returned app `id`.
2. **Create the environment** — `POST /apps/{app_id}/spaces` (`spaces_create`) using the app id. Capture the Space `id`.
3. **Deploy it** — `POST /apps/{app_id}/spaces/{space_id}/pipelines` (`pipelines_create`) to kick off the deploy pipeline.
4. **Poll the Space** — `GET /apps/{app_id}/spaces/{id}` (`spaces_show`) and watch `aasm_state` / `dependencies_status` until the environment is up.
5. **Confirm running instances** — `GET /apps/{app_id}/spaces/{space_id}/instances` (`instances_index`) to list the running containers.

## Notes
- On `401`, re-check the auth headers. On `404`, verify the `app_id`/`space_id` hashids.
- List endpoints paginate with `?page=&per_page=`.
- Errors come back as `{"error": "..."}` or `{"errors": [...]}` — not RFC 9457.
