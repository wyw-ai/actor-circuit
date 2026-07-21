---
name: actor-circuit
description: Actor-centered vocabulary for understanding, explaining, designing, or reviewing collaboration among humans, AI agents, services, scripts, teams, and workflow instances through the L0/L1/L2 Multi-Actor model. Use for Multi-Agent architecture, orchestration, delegation, handoffs, routing, fan-out/fan-in, waits, retries, termination, responsibility flow, or when distinguishing a primitive gate, a composed circuit, and a complete system.
---

# Actor Circuit

Use the L0/L1/L2 model as a vocabulary for reasoning about collaboration. Preserve the user's problem and choose the representation that makes the relevant relationships clearest.

Read [references/layers.md](references/layers.md) for the layer boundaries and maturity of the model.

Read [references/actor.md](references/actor.md) whenever the task depends on deciding what participates as an Actor or where an Actor boundary lies.

Read [references/l0.md](references/l0.md) whenever the task involves collaboration-state transitions, gates, state elements, circuits, path shape, responsibility flow, waiting, convergence, repetition, or termination.

Treat L0 as the current defined scope. Treat L1 and L2 as previews: identify dependencies on them, but do not invent a finished protocol or system taxonomy on behalf of this project.

Keep these distinctions explicit:

- The Actor model is an unnumbered foundation, not part of L0.
- A state element records a collaboration fact that later transitions need to observe. In the circuit analogy, it plays a role similar to a register or latch.
- A gate changes collaboration state. A circuit connects gates, state elements, and possible continuations.
- A topology is not complete until its continuation, ownership, waiting, convergence, and termination semantics are clear.
- A circuit or named orchestration structure is not automatically a collaboration design pattern.
- The underlying gates predate this project. The contribution here is their Actor-centered abstraction, organization, and layering.

Do not force a fixed output format. Produce prose, a table, a diagram, a review, or a structured artifact according to the user's purpose.
