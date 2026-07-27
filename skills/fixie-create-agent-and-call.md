---
name: Create a voice agent and start a call
description: Create a reusable Ultravox agent from a call template, then start a call with it and retrieve the transcript.
api: openapi/fixie-ultravox-openapi-original.yml
operations: [agents_create, agents_calls_create, calls_retrieve, calls_messages_list]
---

# Create a voice agent and start a call

Use the Ultravox Realtime REST API at `https://api.ultravox.ai/api`.

## Auth
Send `X-API-Key: <your 41-char key>` on every request. Keys are created in the
console (app.ultravox.ai) or via the API keys endpoints.

## Steps
1. **Create the agent** — `POST /api/agents` (`agents_create`) with a `name` and
   a `callTemplate` (system prompt, voice, tools, model). Save the returned
   `agentId`.
2. **Start a call with the agent** — `POST /api/agents/{agent_id}/calls`
   (`agents_calls_create`). The response includes the `callId` and a
   `joinUrl`/WebSocket join target the client SDK uses to connect audio.
3. **Poll call details** — `GET /api/calls/{call_id}` (`calls_retrieve`) to read
   `created`/`joined`/`ended` and `endReason`. If you request artifacts before
   the call finishes you may get `425 Too Early` — retry after it ends.
4. **Read the transcript** — `GET /api/calls/{call_id}/messages`
   (`calls_messages_list`), a cursor-paginated list (`next`/`previous`/`results`/`total`).

## Conventions
- Pagination is cursor-based; follow `next` to page.
- Errors return JSON (not problem+json); handle `400`, `404`, `425`, `429`.
- Respect rate limits (429): 200 req/s per key; call-creation limits vary by plan.
