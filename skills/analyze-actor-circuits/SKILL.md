---
name: analyze-actor-circuits
description: Analyze, explain, design, or review collaboration among humans, AI agents, services, scripts, teams, and workflow instances using the L0/L1/L2 Multi-Actor model. Use for Multi-Agent architecture, orchestration, delegation, handoffs, routing, fan-out/fan-in, waits, retries, termination, responsibility flow, or when deciding whether a named pattern is a primitive gate, a composed circuit, or a complete system.
---

# Analyze Actor Circuits

Use the L0/L1/L2 model as a vocabulary for reasoning about collaboration. Preserve the user's problem and choose the representation that makes the relevant relationships clearest.

Read [references/layers.md](references/layers.md) for the layer boundaries and maturity of the model.

Read [references/l0.md](references/l0.md) whenever the task involves identifying Actors, decomposing or composing collaboration logic, distinguishing gates, reviewing responsibility and continuation flow, or assessing whether a circuit is semantically complete.

Treat L0 as the current defined scope. Treat L1 and L2 as previews: identify dependencies on them, but do not invent a finished protocol or system taxonomy on behalf of this project.

Keep these distinctions explicit:

- An Actor is an active collaboration boundary, not a technology category.
- A gate is a stable collaboration-state transition; a circuit is a composition of gates.
- A topology is not complete until its continuation, ownership, waiting, convergence, and termination semantics are clear.
- A circuit or named orchestration structure is not automatically a collaboration design pattern.
- The underlying gates predate this project. The contribution here is their Actor-centered abstraction, organization, and layering.

Do not force a fixed output format. Produce prose, a table, a diagram, a review, or a structured artifact according to the user's purpose.
