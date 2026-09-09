---
title: "Collectors"
description: "Stream traces from a running agent in TypeScript, Python or Go."
---

A collector captures what your agent says to its model and what the
model answers, and ships it to Mirrors in batches. It converts nothing:
the request and the response travel as your SDK saw them, and the
server reads them. It never blocks your application and never throws
after initialisation. Three packages implement one
[specification](https://github.com/runmirrors/mirrors/blob/main/spec/collector.md)
and pass the same fixtures.

## TypeScript

```
npm install mirrors-collector
```

```ts
import { init } from "mirrors-collector";

init({ apiKey: process.env.MIRRORS_API_KEY, environment: "support-agent" });
```

That taps the Anthropic and OpenAI SDKs found in the process. For the
Vercel AI SDK and LangChain.js, pass the integration in:

```ts
import { wrapLanguageModel } from "ai";
import { collector } from "mirrors-collector";
import { mirrorsMiddleware } from "mirrors-collector/aisdk";
import { mirrorsCallbacks } from "mirrors-collector/langchain";

const model = wrapLanguageModel({ model: anthropic("claude-sonnet-5"), middleware: mirrorsMiddleware(collector()) });
const chain = prompt.pipe(llm).withConfig({ callbacks: [mirrorsCallbacks(collector())] });
```

## Python

```
pip install mirrors-collector
```

```python
import mirrors_collector

mirrors_collector.init(api_key=os.environ["MIRRORS_API_KEY"], environment="support-agent")
```

## Go

```
go get github.com/runmirrors/mirrors-collector-go
```

```go
mirrors.Init(mirrors.Options{APIKey: os.Getenv("MIRRORS_API_KEY"), Environment: "support-agent"})
```

## Configuration

| Variable | Meaning |
|---|---|
| `MIRRORS_API_KEY` | the workspace key (`mk_live_...`) |
| `MIRRORS_ENVIRONMENT` | the mirror's name; one per agent |
| `MIRRORS_ENDPOINT` | the API base when not the default |
| `MIRRORS_DEBUG` | log what is captured and shipped |

## Conversation identity

Wrap a conversation in `withConversation(id, fn)` to name it; otherwise
a framework run id, then a content hash, groups the calls. Batches ship
every two seconds or fifty captures; retries back off; a 401 disables
collection for the process and logs once.

## What arrives

Each capture is one envelope: provider, kind, conversation id, sequence,
the request and the response as sent and received, and metadata. The
server pairs them into traces; a mirror named after the environment
appears in the workspace, and the first build is one click away.
