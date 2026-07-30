---
name: Subscribe to lead-activity webhooks
description: Register a webhook subscription for Luxury Presence lead events and verify inbound signatures.
api: openapi/luxury-presence-cms-openapi.json
operations:
  - WebhookController_createWebhook
  - WebhookController_listWebhooks
  - WebhookController_getWebhook
  - WebhookController_deleteWebhook
---

# Subscribe to lead-activity webhooks

## Auth
Send your key in the `x-api-key` header on every management request.

## Steps
1. **Create a subscription** — `POST /crm/v1/webhooks`
   (`WebhookController_createWebhook`) with your callback URL. Store the returned
   shared secret.
2. **List / confirm** — `GET /crm/v1/webhooks`
   (`WebhookController_listWebhooks`) or `GET /crm/v1/webhooks/{id}`
   (`WebhookController_getWebhook`).
3. **Delete when done** — `DELETE /crm/v1/webhooks/{id}`
   (`WebhookController_deleteWebhook`).

## Receiving and verifying events
- The only supported event is `leads` (lead-activity events; see
  `asyncapi/luxury-presence-leads-asyncapi.yml`).
- Each POST to your callback carries `x-lp-signature` (HMAC-SHA256, hex),
  `x-lp-timestamp` (Unix ms), and `x-lp-signature-version` (`v1`).
- Verify by recomputing HMAC-SHA256 of `timestamp + url + JSON.stringify(payload)`
  with your shared secret and comparing with a timing-safe equality check before
  trusting the payload.

## Rules
- Rate limit **500/min per endpoint**; handle `429` with backoff.
- `404` means the webhook id does not exist.
