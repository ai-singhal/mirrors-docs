---
title: "API reference: the tool plane"
description: "Wake a twin, open a session, call tools, read the transcript."
---

The routes an agent uses to call a twin. Env routes take a workspace
key; session routes take the session token they returned (the key that
opened the session may also call them).

| Route | Answer |
|---|---|
| `GET /v1/envs` | the workspace's mirrors with their derived status and headline |
| `GET /v1/usage` | the current period's meters |
| `GET /v1/envs/:slug` | one mirror: active build, twin state, headline |
| `GET /v1/envs/:slug/tools?format=native\|openai\|anthropic` | the active build's tools in a dialect ([tools and calls](/reference/tools)) |
| `POST /v1/envs/:slug/wake` | 200 when the twin is ready, 202 while it boots (poll again) |
| `POST /v1/envs/:slug/sessions` | 201 with the session and its token; starts the twin when needed |
| `POST /v1/sessions/:sid/call` | one tool call inside the session |
| `GET /v1/sessions/:sid` | calls, errors, fidelity counts, the world's state hash, the receipts |
| `GET /v1/sessions/:sid/transcript` | every call with its arguments, result and fidelity, in order |
| `DELETE /v1/sessions/:sid` | closes the session; `{ "existed": true }` |

## Opening a session

```json
POST /v1/envs/support-agent/sessions
{ "seedInstructions": "one customer with an open order and one delivered", "seed": 7, "recordOutputs": true }
```

or `{ "scenario": "returns-week" }` for a named scenario the mirror's
config defines; never both. `clock` fixes the world's time. The answer:

```json
{ "sessionId": "...", "token": "ms_...", "receipts": { "seeded": true, "seed": 7, "scenario": null, "worldHash": "...", "note": null }, "expiresAt": "..." }
```

`receipts.seeded` false with a `note` means the words could not be
turned into rows and the session opened on the unseeded world.

Sessions expire after an hour idle; a twin stops after its idle time
and wakes on the next session or call (202 while it boots).
