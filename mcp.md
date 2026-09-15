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

## Codex

### Browser sign-in (recommended for your workstation)

With the Codex CLI installed, run:

```sh
codex mcp add mirrors --url https://api.runmirrors.com/mcp
codex mcp login mirrors
codex mcp list
```

Follow the browser sign-in, use your Mirrors account, and approve the connection.
The add command may start authentication itself; use `login` if authentication is
still needed. Your account must have access to Mirrors.

Alternatively, add a remote HTTP server in Codex's MCP settings with the name
`mirrors` and URL `https://api.runmirrors.com/mcp`, then authenticate. The server
uses browser OAuth with PKCE and dynamic client registration.

Start a new Codex session after connecting. Ask:

> Use Mirrors to call identity_whoami, then mirrors_list. Tell me which workspace
> I am connected to. Do not start a build or twin.

A successful identity response confirms authentication. An empty mirror list is
valid if you have not uploaded data yet. A server appearing in `codex mcp list`
only confirms configuration; it does not prove a tool call succeeded.

### API key (for automation)

Create a workspace API key in Mirrors **Settings → API keys**. Put it in
`MIRRORS_API_KEY` in the environment of the process that launches Codex. Then run:

```sh
codex mcp add mirrors --url https://api.runmirrors.com/mcp --bearer-token-env-var MIRRORS_API_KEY
```

Or add this to `~/.codex/config.toml` (on Windows,
`%USERPROFILE%\\.codex\\config.toml`):

```toml
[mcp_servers.mirrors]
url = "https://api.runmirrors.com/mcp"
bearer_token_env_var = "MIRRORS_API_KEY"
```

The setting contains the variable's name, not the key itself. Do not commit the
key. Setting a variable in a separate terminal does not update an already running
Codex desktop process; restart it with the variable available, or use OAuth.
Choose one authentication method; remove the bearer-token setting when switching
to browser OAuth.

OAuth avoids copying a long-lived key and suits interactive use. API keys make
unattended setup simpler but require secret storage and rotation.

See the [official Codex MCP documentation](https://developers.openai.com/codex/mcp)
for configuration and supported authentication options.

## Other clients

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

| Symptom | What to check |
|---|---|
| No Mirrors tools | Confirm the server is enabled, check `codex mcp list`, then open a new Codex session. |
| 401 or authentication required | Retry OAuth login, or check the API key and the launching process's environment. |
| 403 or access denied | Confirm your Mirrors account, workspace, and access status. |
| Browser sign-in expired | Run `codex mcp login mirrors` again and complete the fresh consent request. |
| Works locally, fails in a remote executor | Configure the server and credentials where that Codex instance runs; local environment variables are not automatically available remotely. |

## MCP, collectors, and twins

MCP gives your coding assistant tools to operate Mirrors. It does not install a
collector or automatically capture that assistant's conversations. A collector
must run inside the agent application whose model calls you want to capture.
See [Collectors](/collectors).

The MCP management endpoint is `/mcp`. The simulated application's tool endpoints
under `/v1/envs/<slug>` are a different interface: they require a built mirror,
a running twin, and a session. Connecting MCP alone does not create those.

A failed tool answers in the product's words: the reason, the code and
what to do next (sign in, wait for the build, pick another rung). A
quota answer names the meter and whether a card would lift it.
