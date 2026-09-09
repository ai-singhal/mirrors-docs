---
title: "The CLI"
description: "The mirrors command: login, setup, build, call, and every verb."
---

`mirrors` is the terminal client. Every command is one service verb
plus rendering; the same verbs back the web app and the MCP server.

```
npm install -g @mirrors/cli
mirrors login --api-key mk_live_...      # or set MIRRORS_API_KEY
mirrors whoami
```

Credentials come from flags (`--api-key`, `--api-url`), then the
environment (`MIRRORS_API_KEY`, `MIRRORS_API_URL`), then the file
`login` wrote (owner-only, under `~/.config/mirrors`). `--dev` talks to
a local control plane with auth off. `--json` prints the answer as JSON;
`--debug` re-raises errors with their stack. Exit code 1 on an error,
130 on Ctrl-C.

## Built-in commands

| Command | What it does |
|---|---|
| `setup [--name N]` | the onboarding conversation: pickers, GitHub connect, file drops, the build, the twin; resumes where it left off |
| `build NAME [--traces F]... [--agent F] [--schema F] [--tools F] [--openapi F] [--tool-defs F] [--docs F] [--watch]` | files to uploads, the shared intake, and a tail of the build log with `--watch` |
| `tools ENV [--format native\|openai\|anthropic]` | the twin's tools in a dialect |
| `call ENV TOOL ['{json}'] [--seed-instructions T] [--scenario S] [--seed N]` | one tool call in a fresh seeded session; prints content, error, fidelity, rung |
| `whoami`, `usage`, `logout` | the account |

`setup` accepts `--say TEXT`, `--choice A,B` and `--attach FILE...` for
one-shot answers, `--reset` to start over, `--browser` to open the
GitHub link.

## Every other verb

A verb's dotted name is its words: `mirrors builds list ENV`,
`runtime twin start ENV`, `improve proposals draft ENV`,
`mirrors config change ENV --change '{...}'`. The first positional fills
the mirror reference (id, slug or name); flags come from the verb's
input schema, typed (numbers, switches, JSON). `mirrors help` lists
them from the server's registry, so a new verb appears without a CLI
update.

The old spellings still work as aliases: `env ls`, `env show`,
`container start`, `context ls`, `context summary distill`,
`proposal new`, `review ls`.
