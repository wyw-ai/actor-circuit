# Open Multi-Actor Protocol

> When Actors become circuits.

One Agent can plan, write code, search, or operate tools. Putting several Agents together, however, does not automatically create a system.

Imagine a small research team:

```text
Understand the question
  → split it into several branches
  → give each branch to a different participant
  → wait for their results
  → converge
  → review
  → either revise or stop
```

Replace research with coding, incident response, approval, or customer support and much of that structure remains unchanged. The same small set of collaboration operations appears repeatedly: continue, branch, fan out, converge, call, delegate, transfer, wait, resume, and stop.

We call these operations **collaboration logic gates**. Connect them and Actors begin to form a circuit.

## Not only Agents

Real AI-native systems include more than language-model Agents:

```text
Human → AI Agent → Script → Service → Human
```

This project uses **Actor** for any independently identifiable collaboration boundary with state, capabilities, and a lifecycle. A Human, Agent, Service instance, Script execution, Team, or Workflow instance may be an Actor.

Actor is the foundation, not L0.

## Three layers

```text
Actor foundation
  Who participates

L0 — Collaboration Logic Circuit
  How collaboration state changes

L1 — Multi-Actor Communication Protocol
  How those changes cross Actor boundaries reliably

L2 — Multi-Agent Systems
  How Actors, circuits, and protocol form complete systems
```

The first release defines the Actor foundation and L0. L1 and L2 are intentionally left as previews for future work.

## Skill

[Actor Circuit](skills/actor-circuit/SKILL.md) gives a capable AI Agent this domain model without prescribing a step-by-step workflow.

Its references contain:

- the [Actor foundation](skills/actor-circuit/references/actor.md);
- the [L0 professional specification](skills/actor-circuit/references/l0.md);
- the [L0/L1/L2 boundaries](skills/actor-circuit/references/layers.md).

There is no script, CLI, or machine schema in this release. The model needs to become stable through real use before deterministic tooling is justified.

## Provenance

The underlying workflow and interaction structures are not claimed as original inventions. This project reorganizes established research, existing framework designs, and Multi-Actor engineering experience around a common Actor foundation and a clear layer boundary.
