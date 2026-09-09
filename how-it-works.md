---
title: "How it works"
description: "Mirrors, builds, twins, sessions, fidelity, drift and the levers that fix it."
---

## The words

- **Mirror**: one agent's backends. It holds the traces, the inputs
  (tool code, schema, specs, documents), the config versions and the
  builds. Named after the collector environment or the name you gave it.
- **Build**: an immutable artifact made from a frozen snapshot of the
  inputs. One build is **active** (the twin serves it); one may be
  **pinned** (kept regardless of newer builds). Builds are listed,
  diffed, activated and pinned on the Builds screen.
- **Twin**: the running process for the active build. It starts on
  demand, stops when idle, and answers the tool plane.
- **Session**: one isolated world inside the twin, seeded from a
  scenario or from words ("one customer with two open orders"). Your
  agent calls tools inside a session; sessions never share state.
- **Binding rung**: where a tool's answers come from, highest first:
  provided (your tool code), custom (an implementation you wrote),
  workspace, sink, world (the reconstructed world model), synthesized,
  simulated. The build binds each tool as high as the evidence allows.
- **Fidelity**: how closely the twin answers like production. The
  headline is **trace conformance**, measured by replaying whole
  recorded conversations against the twin; **decision conformance**
  beside it asks whether the agent would have made the same next call.
  The build's own estimate stands in until a replay exists. A number
  that was not measured is shown as unmeasured, never as zero.
- **Drift**: per tool, why the twin differs from production, named by
  kind and paired with the **lever** that fixes it. Attention groups
  tools into healthy, inaccurate, unmeasured, imagined (answered by a
  simulator) and broken.
- **Config version**: knobs, per-tool overrides, world overrides and
  scenarios, immutable and numbered. A person's change is approved at
  once; an agent's waits in the review queue.
- **Proposal**: a drafted change set from the drift, with a predicted
  effect; accept it and a build verifies it.

## The loop

1. Traces arrive (a collector, an upload, an IDE agent). Every format
   is read into one canonical shape; an unknown format goes to the
   discovery agent, which writes a transpiler and shows a preview for
   review.
2. A build reconstructs the world model behind the tools (entities,
   lifecycles, relations, operations), binds each tool on the ladder,
   scores it, packages the artifact and replays a sample of traces.
3. The twin serves the active build; your agent, the console, the
   replay screen and the verification all call it the same way.
4. Drift and proposals point at what to fix; the tool page is where a
   person fixes it; agents can apply levers themselves and a person
   reviews after.

## What is measured, and what is not

Trace conformance and decision conformance come from replays and carry
their sample size. Per-tool structural and exact match rates are
diagnostics. The build estimate is a prediction from the evidence
grades. Anything the product could not measure says so.
