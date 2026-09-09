---
title: "API reference: tools and calls"
description: "Tool dialects, the call request and result, seeding."
---

## Tool dialects

`GET /v1/envs/:slug/tools?format=`:

- `native`: `{ name, description, inputSchema, rung, attention, scores }`
  per tool, with what the twin knows about it.
- `openai`: `{ type: "function", function: { name, description, parameters } }`
  entries, ready for a chat completions or Responses request.
- `anthropic`: `{ name, description, input_schema }` entries, ready for
  a messages request.

The names are the production tools' names; your agent calls them as it
does in production.

## A call

```json
POST /v1/sessions/:sid/call
{ "name": "lookup_order", "arguments": { "id": "o_1" }, "requestor": "assistant" }
```

`requestor` is `assistant` (the model asked) or `user` (a person or a
harness asked); it is recorded, not enforced. The result:

```json
{
  "content": "{\"id\":\"o_1\",\"status\":\"open\",...}",
  "error": false,
  "fidelity": "A_executed",
  "rung": "world",
  "downgraded": false,
  "callId": "c12",
  "ms": 41
}
```

- `content`: the tool's answer as text, exactly as the agent should see it.
- `error`: the tool answered with an error (the way production would).
- `fidelity`: the grade of this answer: `A_executed` (provided or
  custom code ran), `B_twin` (the world model), `B_replayed` (a recorded
  answer), `B_inferred`, `B_simulated` (a model imagined it).
- `rung`: which rung answered.
- `downgraded`: the bound rung could not answer and a lower one did
  (the tool page says why).
- `callId`: the call's id in the transcript.

A call the twin cannot route answers 404 with the tool name; a session
that expired answers 404; a twin still booting answers 409 with
`notReady`, retry after a moment.

## Seeding

Words seed a session on the fly: the twin turns them into rows for the
entities they mention. A named scenario is a seed spec the mirror's
config defines (rows per entity, files, a clock), reproducible by name.
`seed` makes generated values deterministic.
