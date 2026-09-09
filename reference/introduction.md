---
title: "API reference: introduction"
description: "Base URL, authentication, the error envelope, rate limits, the registry."
---

## Base URL and transports

`https://api.runmirrors.com`. Three doors:

- **tRPC** at `/trpc/<verb>`: the web app and the CLI; queries for
  reads (`GET` with `?input=`), mutations for writes (`POST` with a JSON
  body). Every procedure is a verb from the [registry](/reference/verbs).
- **Plain HTTP** routes for what is not a verb: [collect](/reference/collect),
  the [tool plane](/reference/envs), the multipart [build](/reference/build), uploads,
  webhooks, OAuth discovery, health.
- **MCP** at `/mcp` ([the MCP server](/mcp)).

`GET /api/registry` returns every verb definition and each verb's real
input as JSON Schema; the CLI derives its commands from it.

## Authentication

`Authorization: Bearer <credential>`: a workspace key (`mk_live_`), a
session token (`ms_`, session routes only), an MCP access token
(`mcp_at_`), or the web session's JWT. See [API keys](/api-keys).

## Errors

Every error answers with one JSON envelope:

```json
{ "detail": "No such mirror.", "code": "notFound", "requestId": "..." }
```

Quota errors add `meter` (which meter, used and limit) and
`cardWouldLift` (whether a card on file would lift it); access errors
add `access` (`pending` or `denied`). tRPC carries the same envelope
under `error.data.envelope` with the HTTP status in `error.data.httpStatus`.

| Status | Meaning |
|---|---|
| 401 | no or invalid credential |
| 402 | a meter is over its limit |
| 403 | access not granted, or an admin verb |
| 404 | no such mirror, build, session |
| 409 | not ready yet (a twin booting, a build running) or a conflict |
| 413 | a collect batch or upload over the size cap |
| 422 | the input did not validate; `detail` names the field |
| 429 | rate limited; retry after a moment |

## Rate limits and sizes

Collect batches are capped by bytes per request and by collected bytes
per mirror; uploads by bytes per file; the contact form and the collect
route by address. Long operations (a build, a twin start) answer with
an id to poll rather than holding the connection.

## Versioning

The API is unversioned; verbs are added, never changed in place. The
registry is the record of what exists.
