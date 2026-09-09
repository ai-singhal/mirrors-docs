---
title: "API reference: collect"
description: "The endpoint collectors post to."
---

`POST /api/collect`, a workspace key as bearer. The route the
[collectors](/collectors) post to; anything else that can produce
canonical traces or capture envelopes may post too.

## Request

```json
{
  "environment": "support-agent",
  "items": [ { "...": "a canonical trace or a capture envelope" } ]
}
```

`environment` names the mirror (created in the collecting state when
new). Each item is either a canonical trace or a capture envelope
(`provider`, `captureKind`, `conversationId`, `sequence`, `request`,
`response`, `meta`); the two may be mixed. `project` and `traces` are
accepted as older spellings of the two keys.

## Response

```json
{
  "accepted": 12,
  "superseded": 1,
  "skipped": [ { "index": 3, "reason": "noConversation" } ],
  "pending": 0
}
```

- `accepted`: items read into traces.
- `superseded`: items that replaced an earlier version of the same
  conversation (a later capture with more turns).
- `skipped`: items no transpiler could read, with the closed list of
  reasons (`undecodable`, `malformedJson`, `noFormat`, `noConversation`,
  `noMessages`, `emptyAfterFilter`, `notARecord`).
- `pending`: raw items of an unrecognised shape held for format
  analysis; the discovery agent writes a transpiler and they are read
  later.

## Errors

- 401 with `detail` "Sign in again." when the key is missing or
  revoked; a collector stops for the process on it.
- 413 when the batch or the mirror's collected bytes pass the cap;
  `detail` names the cap.
- 422 when the body is not the shape above.
