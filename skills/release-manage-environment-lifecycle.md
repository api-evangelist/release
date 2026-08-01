---
name: Pause, resume, extend, and tear down an environment
description: Manage the lifecycle of an existing Release environment (Space) — pause to save cost, resume, extend its expiration, or destroy it.
api: openapi/release-openapi-original.json
base_url: https://api.release.com
auth: X-User-Email + X-User-Token headers (from ~/.release.yaml)
operations: [spaces_index, spaces_pause, spaces_resume, spaces_extend_expiration, spaces_destroy]
---

# Pause, resume, extend, and tear down an environment

Ephemeral environments (Spaces) can be paused to save compute, resumed on demand, extended before they auto-expire, or destroyed.

## Steps
1. **Find the environment** — `GET /apps/{app_id}/spaces` (`spaces_index`) to list Spaces for the app and select the target `space_id`.
2. **Pause** — `POST /apps/{app_id}/spaces/{id}/pause` (`spaces_pause`) to stop the environment's workloads.
3. **Resume** — `POST /apps/{app_id}/spaces/{id}/resume` (`spaces_resume`) to bring it back up.
4. **Extend expiration** — `PUT /apps/{app_id}/spaces/{id}/extend_expiration` (`spaces_extend_expiration`) to push back an ephemeral environment's auto-teardown time.
5. **Destroy** — `DELETE /apps/{app_id}/spaces/{id}` (`spaces_destroy`) to tear the environment down permanently.

## Notes
- Destroy is irreversible; confirm the `space_id` (hashid) before calling it.
- `spaces_pause` / `spaces_resume` are safe to retry; `spaces_destroy` is not idempotent-guarded, so guard it at the caller.
- On `404`, the Space may already be destroyed.
