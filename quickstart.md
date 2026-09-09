---
title: "Quickstart"
description: "From traces to a twin your agent can call, in four steps."
---

Four steps: get traces in, build, point your agent at the twin, replay.
Every step works from the web app, the CLI, or an IDE agent over MCP;
the CLI is shown here.

## 1. Sign in and get a key

Sign in at the web app (Google or email), then under Settings mint an
API key. Store it once:

```
npm install -g @mirrors/cli
mirrors login --api-key mk_live_...
```

## 2. Get traces in

Either upload an export your framework or tracing tool wrote (any of the
[accepted formats](/reference/formats); [check it first](/traces)):

```
mirrors build support-agent --traces ./traces.jsonl --watch
```

or add a [collector](/collectors) to the running agent and let it
stream. A mirror named after the environment appears once traces arrive;
the first build starts when you ask for it:

```
mirrors mirrors builds request support-agent
```

Tool code, a database schema, an OpenAPI spec, provider tool definitions
and documents are optional inputs that raise fidelity; pass them as
`--tools`, `--schema`, `--openapi`, `--tool-defs`, `--docs`.

`--watch` tails the build log. A build takes minutes; it ends with the
headline: trace conformance (the share of replayed conversations the
twin answered as production did), decision conformance, and the count
of tools by attention (healthy, inaccurate, unmeasured, imagined,
broken).

## 3. Point your agent at the twin

The twin answers the tool plane. From a shell:

```
mirrors tools support-agent --format openai
mirrors call support-agent lookup_order '{"id":"o_1"}' --seed-instructions "one customer with an open order"
```

From code, the agent takes its tools from `/v1/envs/<slug>/tools`
(native, OpenAI or Anthropic dialect), opens a session, and posts each
call to the session; every answer carries its fidelity grade and
whether it was downgraded. The [tool plane reference](/reference/envs)
has the routes; `examples/tool-plane-agent` in the repository is a
complete agent.

## 4. Replay and improve

```
mirrors runtime replay accuracy support-agent
mirrors mirrors builds drift support-agent --build-id <id>
```

Drift names, per tool, why the twin differs and the lever that fixes it:
pin a rung, edit the operation or the template, add examples, describe
the behaviour, or write a custom implementation. Each lever is a button
on the tool's page in the web app and a `mirrors mirrors config change`
from the CLI; saving makes a config version and the next build applies
it. Proposals do this for you: `mirrors improve proposals draft` reads
the drift and drafts a change set you accept or reject.
