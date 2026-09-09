---
title: "For agents"
description: "Markdown twins: what an IDE agent reads to use Mirrors without a person."
---

An IDE agent can use Mirrors without a person: the product is a set of
verbs, and its documentation is written to be read by a model as well as
a person.

## Markdown twins

The four MCP resources are the authorities on the contracts that fail
late: the flow, the accepted formats, how a file is passed, and the
config levers. They are served under `mirrors://` to MCP clients and
published as pages here, from the same text:

- [Getting started](/reference/getting-started)
- [Accepted trace formats](/reference/formats)
- [File inputs](/reference/inputs)
- [Config and overrides](/reference/overrides)

The [verb reference](/reference/verbs) is generated from the live
registry: every verb with its input and output schema, its MCP tool
name and its CLI words.

## llms.txt

[llms.txt](/llms.txt) is the plain-text index of this documentation for
language-model crawlers; the web site serves its own at
`https://runmirrors.com/llms.txt` with the reference pages' text.

## A working pattern

1. `identity_whoami`, then `mirrors_list`.
2. Read `mirrors://formats/inputs`; upload with `mirrors_uploads_reserve`
   and a PUT, or pass inline text; `mirrors_builds_intake`.
3. Poll `mirrors_builds_get` until it ends; `mirrors_get` for the headline.
4. `runtime_twin_start`; `runtime_console_open` with seed instructions;
   `runtime_console_invoke` per tool call.
5. `mirrors_builds_drift`, then `improve_proposals_draft`; decide with
   `improve_proposals_decide`; `improve_review_list` shows what other
   agents changed and waits for a person.
