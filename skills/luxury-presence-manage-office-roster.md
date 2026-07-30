---
name: Manage an office roster
description: Create an office and add or remove agent members in Luxury Presence.
api: openapi/luxury-presence-cms-openapi.json
operations:
  - OfficesController_handlePostOffice
  - OfficesController_handleGetOfficeAgents
  - OfficesController_handlePostOfficeAgents
  - OfficesController_handleDeleteOfficeAgents
---

# Manage an office roster

## Auth
Send your key in the `x-api-key` header on every request.

## Steps
1. **Create the office** — `POST /cms/v1/offices`
   (`OfficesController_handlePostOffice`). Capture the returned office `id`.
2. **List current members** — `GET /cms/v1/offices/{id}/agents`
   (`OfficesController_handleGetOfficeAgents`).
3. **Add members** — `POST /cms/v1/offices/{id}/agents`
   (`OfficesController_handlePostOfficeAgents`) with the agent id(s).
4. **Remove members** — `DELETE /cms/v1/offices/{id}/agents`
   (`OfficesController_handleDeleteOfficeAgents`).

## Rules
- Agents must already exist (see the onboard-agents skill) before adding them.
- Rate limit **500/min per endpoint**; handle `429` with backoff.
- `404` means the office or agent id does not exist — re-fetch to reconcile.
