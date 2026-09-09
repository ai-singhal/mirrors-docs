---
title: "The MCP server"
description: "Drive the whole product from an IDE agent over the hosted endpoint."
---

The whole product, as tools an IDE agent calls: build a mirror, read
its numbers, start the twin, call tools, replay, improve, review. It is
hosted; nothing runs on your machine.

## Endpoint

`https://api.runmirrors.com/mcp`, streamable HTTP, stateless.

Two ways to authenticate:

- an API key as a bearer: `Authorization: Bearer mk_live_...`;
- browser OAuth: a client that supports it discovers the authorization
  server from the endpoint (`/.well-known/oauth-protected-resource`),
  registers, sends you to the consent page in the web app, and gets a
  token scoped to your workspace.

Claude Code:

```
claude mcp add --transport http mirrors https://api.runmirrors.com/mcp --header "Authorization: Bearer mk_live_..."
```

Any JSON-configured client:

```json
{ "mcpServers": { "mirrors": { "url": "https://api.runmirrors.com/mcp", "headers": { "Authorization": "Bearer mk_live_..." } } } }
```

## Tools

Every tool is a service verb; the name is the verb's dotted name with
underscores (`mirrors_builds_intake`, `runtime_console_invoke`). The
[verb reference](/reference/verbs) lists them all with their inputs.
Tools that spend compute (a build, a twin start, a proposal, a
distillation) say so in their description; builds are start plus poll
(`mirrors_builds_intake` answers with a job id; `mirrors_builds_get`
reports progress).

Start with `identity_whoami`, then `mirrors_list`. A mirror argument
accepts an id, a slug or a name everywhere.

## Resources

Four `mirrors://` documents, each the single authority on its topic;
read the relevant one instead of inferring a contract from tool
signatures, because inputs fail late (after a build) when they are
shaped wrong:

- `mirrors://guide/getting-started`: concepts and the flow ([page](/reference/getting-started)).
- `mirrors://formats/traces`: the accepted formats ([page](/reference/formats)).
- `mirrors://formats/inputs`: how a file is passed ([page](/reference/inputs)).
- `mirrors://config/overrides`: knobs, levers, world overrides, scenarios ([page](/reference/overrides)).

## Errors

A failed tool answers in the product's words: the reason, the code and
what to do next (sign in, wait for the build, pick another rung). A
quota answer names the meter and whether a card would lift it.
