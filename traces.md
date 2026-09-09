---
title: "Trace exports"
description: "What a good export contains and the checks to run before uploading."
---

Send what your framework or tracing tool exports; the transpiler library
reads the [accepted formats](/reference/formats) by their shape, and
an unknown shape is accepted and analysed. The raw upload is always
kept. Before uploading, check four things; each one costs fidelity when
it is missing, and the build cannot recover it.

## 1. All four roles

A conversation has system, user, assistant and tool messages. Exports
that drop the system prompt or flatten tool results into assistant text
leave the build guessing what the tools returned.

## 2. The real system prompt

The prompt the agent actually ran with, not a placeholder. The build
reads tool descriptions and the agent's instructions from it; the
simulator reads it for what the backend is for.

## 3. Calls paired with results

Every tool call carries an id, a name and its arguments; the tool
message that answers it carries the same id. Exports that log calls
and results in separate streams need the ids to line up; a result that
cannot be paired is kept but scores nothing.

## 4. Failures preserved

When a tool returned an error, the export must say so (an error flag,
a status, or the error text as the result). The twin reproduces error
behaviour only from evidence of it; a corpus scrubbed of failures
produces a twin that never fails.

## Also worth keeping

- Tool definitions (the schemas the model saw), when the export has
  them; they give tool identity without code.
- Conversation boundaries: one trace per conversation. A collector
  names conversations for you; an export that concatenates them is
  split by the transpiler when it can.
- Extended thinking is dropped; images and files become `[image]` and
  `[file]`; content is never truncated.

## The canonical shape

One JSON object per trace: `traceId`, `domain`, `messages` (role,
content, tool calls with `id`, `name`, `arguments`; tool messages with
`toolCallId` and `error`), optional `toolDefs`. The formats page lists
what maps onto it.
