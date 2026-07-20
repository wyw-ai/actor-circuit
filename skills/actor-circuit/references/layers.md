# The Actor Foundation and L0/L1/L2 Model

## Status

This is a working model for organizing Multi-Actor engineering questions, not an established industry standard.

- The **Actor foundation** and **L0** are defined in the current release.
- **L1** and **L2** currently define scope only. Their detailed specifications remain future work.

## Foundation: Actor

Actor answers **who participates**.

An Actor is an independently identifiable collaboration boundary with state, capabilities, and a lifecycle. Human, AI Agent, Service, Script, Team, Workflow instance, and External System may all be Actors when they participate through such a boundary.

Actor is not part of L0. It is the foundation imported by every layer.

Use [actor.md](actor.md) for the Actor model.

## L0: Collaboration Logic Circuit

L0 answers **how collaboration state changes**.

Its three core concepts are:

- **Collaboration State** — the state visible to collaboration logic;
- **Gate** — a stable state transition;
- **Circuit** — a composition of gates.

L0 observes changes in path shape, responsibility, and lifecycle. It describes Sequence, Branch, Fan-out, Fan-in, Route, Call, Delegate, Transfer, Wait, Stop, and related semantics without specifying transport or runtime implementation.

Use [l0.md](l0.md) for the current L0 specification.

## L1: Multi-Actor Communication Protocol

L1 answers **how an L0 transition is expressed and made reliable across Actor boundaries**.

Its expected responsibilities include:

- identity and addressing;
- message and artifact envelopes;
- delivery and acknowledgement;
- task, branch, round, correlation, and causation identity;
- context and result carriage;
- responsibility-transfer acceptance;
- offline delivery, persistence, resumption, and recovery;
- cancellation, duplication, idempotency, and late-result handling;
- permissions and observable lifecycle events.

L0 can reveal an L1 requirement without defining its mechanism. Fan-out followed by Fan-in requires branch correlation. Transfer requires observable acceptance. Wait requires a durable resumption signal.

The current release must not present a particular wire format or state machine as the finished Open Multi-Actor Protocol.

## L2: Multi-Agent Systems

L2 answers **how Actors, L0 circuits, and L1 communication form a complete Multi-Agent system**.

Names such as Orchestrator–Worker, Group Chat, recursive delegation, and Evaluator–Optimizer belong here. A name alone is not a complete description: an L2 system also needs Actor boundaries, state ownership, circuits, protocol assumptions, convergence rules, failure behavior, and termination conditions.

The current release may recognize candidate L2 structures, but their taxonomy and design guidance remain future work.

## Patterns Span the Layers

A collaboration design pattern resolves recurring forces by arranging roles, knowledge, authority, commitment, feedback, and exit behavior. It normally includes Context, Forces, Structure, Protocol, and Consequences.

Fan-out/Fan-in is an L0 circuit. Orchestrator–Worker is an L2 composition. Neither becomes a design pattern merely by receiving a name. A real pattern may span the Actor foundation, L0, L1, and L2.
