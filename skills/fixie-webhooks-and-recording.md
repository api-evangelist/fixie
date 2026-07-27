---
name: Subscribe to call webhooks and fetch recordings
description: Register a webhook to receive call lifecycle events, then fetch the recording and events for a completed call.
api: openapi/fixie-ultravox-openapi-original.yml
operations: [webhooks_create, webhooks_list, calls_events_list, calls_recording_retrieve]
---

# Subscribe to call webhooks and fetch recordings

Use the Ultravox Realtime REST API at `https://api.ultravox.ai/api` with
`X-API-Key` auth.

## Steps
1. **Register a webhook** — `POST /api/webhooks` (`webhooks_create`) with a
   destination `url`, the `events` to subscribe to
   (`call.started`, `call.joined`, `call.ended`, `call.billed`), and per-webhook
   `secrets` used to verify signatures.
2. **Confirm registration** — `GET /api/webhooks` (`webhooks_list`) and check
   `status` and `recentFailures`.
3. **Receive events** — Ultravox POSTs `{"event": "...", "call": {...}}` to your
   URL; verify the signature using your secret (see docs: securing-webhooks).
   Failed deliveries are retried with exponential backoff.
4. **On `call.ended`, fetch artifacts** — `GET /api/calls/{call_id}/events`
   (`calls_events_list`) for logged events, and
   `GET /api/calls/{call_id}/recording` (`calls_recording_retrieve`) for a link
   to the recording (available only after the call completes; otherwise `425`).

## Conventions
- Verify every inbound webhook signature; never trust the payload unverified.
- Recording/log endpoints return `425 Too Early` before processing completes.
