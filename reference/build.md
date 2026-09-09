---
title: "API reference: build"
description: "The multipart build route, rebuild, and job polling."
---

Three plain routes for scripts and pipelines; the same intake the web
app, the CLI and MCP use.

## `POST /api/mirrors/build` (multipart)

A workspace key as bearer. Fields:

| Field | Meaning |
|---|---|
| `name` | the mirror by name (created when new) |
| `traces` | one or more trace export files ([formats](/reference/formats)) |
| `agent` | the agent's source or an archive, read for tool identity |
| `tools` | tool code (Python, TypeScript, Go) bound as provided tools when it loads |
| `schema` | a DDL file the world model is reconciled against |
| `openapi` | a spec whose operations become tool candidates |
| `toolDefs` | provider tool definitions, JSON |
| `docs` | PDF, DOCX or text turned into context sources |

Repeat a field for several files (`traces[]` works too). At least one
identity source (traces, tools, an OpenAPI spec, tool definitions or a
schema) is required. Code and archives pass a malware scan; a finding
refuses the file by name.

Answers 202:

```json
{ "jobId": "...", "mirrorId": "...", "status": "queued" }
```

Large files: reserve an upload (`mirrors.uploads.reserve` over tRPC or
MCP), PUT the bytes to the returned URL, then pass the upload id to
`mirrors.builds.intake`; the multipart route is for files that fit one
request.

## `POST /api/mirrors/:ref/rebuild`

Snapshot the mirror's current inputs and build again; `ref` is an id,
a slug or a name. Answers 202 with the build as the job.

## `GET /api/ingest-jobs/:id`

The job's status (`queued`, `running`, `succeeded`, `failed`), the
mirror, the headline when it succeeded, the error (stage and message)
when it failed, and the log lines so far. Poll every few seconds; a
build takes minutes and ends within its wall clock (thirty minutes by
default).
