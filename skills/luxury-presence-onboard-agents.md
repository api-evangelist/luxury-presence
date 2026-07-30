---
name: Onboard agents to Luxury Presence
description: Bulk-create agent records in Luxury Presence and verify them, handling uniqueness and rate limits.
api: openapi/luxury-presence-cms-openapi.json
operations:
  - AgentsController_handleBulkCreateAgents
  - AgentsController_handleGetAgents
  - AgentsController_handleGetAgent
  - AgentsController_handlePatchAgent
---

# Onboard agents to Luxury Presence

Use the Public API (base `https://api.luxurypresence.com`) to add agents in bulk.

## Auth
Send your key in the `x-api-key` header on every request. Keys are generated in the
dashboard (Settings > API Keys, admin role required).

## Steps
1. **Bulk create** — `POST /cms/v1/agents/bulkCreate` (`AgentsController_handleBulkCreateAgents`)
   with an array of agent objects. Each agent must have a **unique email** and a
   **unique license number** — duplicates are rejected with `400`. A partial upstream
   failure surfaces as `502`.
2. **Verify** — `GET /cms/v1/agents` (`AgentsController_handleGetAgents`) with
   `limit`/`offset` pagination, or `GET /cms/v1/agents/{id}`
   (`AgentsController_handleGetAgent`) to confirm each record.
3. **Fix up** — `PATCH /cms/v1/agents/{id}` (`AgentsController_handlePatchAgent`) to
   correct fields. Note: `websiteURL` is display-only and does not change an agent's
   subdomain.

## Rules
- Rate limit is **500 requests/min per endpoint**; prefer the bulk endpoint over
  per-agent loops. On `429`, back off and retry.
- No idempotency key exists — do not blindly re-POST a failed bulk batch; GET to
  reconcile first.
- Errors: `400` invalid/duplicate input, `401` missing/inactive key, `404` unknown id.
