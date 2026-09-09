---
title: "Mirrors documentation"
description: "What a mirror is and where to start."
---

Mirrors builds a runnable digital twin of the backends an AI agent talks
to, from the agent's own traces. Your agent calls the twin instead of
production; recorded conversations replay against it to measure how
faithfully it answers; the numbers say which tool to fix and where.

## Start here

- [Quickstart](/quickstart): traces in, a twin out, your agent calling it.
- [How it works](/how-it-works): the words the product uses and what each measures.
- [Trace exports](/traces): what a good export contains, checked before you upload.

## Ways in

- [Collectors](/collectors): stream traces from a running agent (TypeScript, Python, Go).
- [The MCP server](/mcp): drive everything from an IDE agent.
- [The CLI](/cli): the `mirrors` command.
- [API keys](/api-keys) and [plans and usage](/plans-and-usage).
- [For agents](/for-agents): the pages an IDE agent reads on its own.

## Reference

- [Introduction](/reference/introduction), [collect](/reference/collect), [the tool plane](/reference/envs), [tools and calls](/reference/tools), [build](/reference/build).
- Generated from the product itself: [service verbs](/reference/verbs), [accepted trace formats](/reference/formats), [file inputs](/reference/inputs), [config and overrides](/reference/overrides), [getting started for agents](/reference/getting-started).

A plain-text index for language-model crawlers is at [llms.txt](/llms.txt).
