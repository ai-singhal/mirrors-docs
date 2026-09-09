---
title: "API keys"
description: "Minting, revoking, and which key goes where."
---

A workspace key authenticates everything outside the browser: the
collectors, the CLI, the MCP server as a bearer, and the tool plane.

## Minting and revoking

Settings, API keys: mint with a label; the plaintext is shown once and
never again (the list shows the last four characters). Revoke one key or
all of them; a revoked key fails with 401 at once, and a collector that
sees a 401 stops for the process.

## Which credential goes where

| Credential | Prefix | Used by |
|---|---|---|
| workspace key | `mk_live_` | collectors, the CLI, MCP bearer, the tool plane's env routes |
| session token | `ms_` | the tool plane's session routes, returned when a session opens; the key that opened it may also call them |
| MCP access token | `mcp_at_` | an OAuth client that signed in through the browser |
| the web session | | the web app only; it never reaches the console or a third party |

## Where the key is read

`Authorization: Bearer mk_live_...` on every request. The collectors
read `MIRRORS_API_KEY`; the CLI reads the flag, the variable or its
config file.

## Access

New accounts can be held for access; until granted, the only verb that
answers is the access status. An admin grants access per email.
