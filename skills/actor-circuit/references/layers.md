# The L0/L1/L2 Multi-Actor Model

## Status

This is a working model for organizing Multi-Actor engineering questions. It is not an established industry standard.

- **L0 is defined in the current release.**
- **L1 and L2 are boundary definitions and previews.** Their detailed specifications remain future work.

The model applies to collaboration among Humans, AI Agents, Services, Scripts, Teams, Workflow instances, and External Systems. It does not assume that every participant uses a language model.

## L0: Collaboration Logic Circuits

L0 describes how collaboration state changes.

Its vocabulary includes gates such as Sequence, Branch, Route, Fan-out, Fan-in, Call, Delegate, Transfer, Wait, Loop, and Stop. Gates can be connected into circuits.

L0 answers questions such as:

- How many work paths exist before and after this transition?
- Which path or Actor can continue?
- Did execution ownership or continuation ownership change?
- Is the work active, waiting, resumed, or terminal?
- When may independent branches converge?

L0 does not define message formats, delivery guarantees, Actor discovery, persistence, or a complete Multi-Agent application.

Use [l0.md](l0.md) as the normative vocabulary for the current release.

## L1: Multi-Actor Communication Protocol

L1 carries L0 state transitions across independent Actor boundaries.

Its expected responsibilities include:

- identity and addressing;
- message and artifact envelopes;
- delivery and acknowledgement;
- task, branch, round, correlation, and causation identity;
- context and result carriage;
- responsibility-transfer acknowledgement;
- offline delivery, persistence, resumption, and recovery;
- cancellation, duplication, idempotency, and late-result handling;
- permissions and observable lifecycle events.

L0 may reveal an L1 requirement without defining its mechanism. For example, Fan-out followed by Fan-in requires branch correlation; Transfer requires an observable acceptance boundary; Wait requires a durable resumption trigger.

The current release may surface these requirements, but must not present a particular wire format or state machine as the finished Open Multi-Actor Protocol.

## L2: Multi-Agent Systems

L2 composes L0 circuits over L1 communication into complete operating structures.

Common names at this level include Orchestrator–Worker, Group Chat, Evaluator–Optimizer, recursive delegation trees, and other Multi-Agent arrangements. The name alone does not define the full system. A complete description also needs its Actors, state ownership, circuit, protocol assumptions, convergence rules, failure behavior, and termination conditions.

The current release may recognize or compare candidate L2 structures, but its taxonomy and design guidance remain future work.

## Circuits Are Not Automatically Patterns

A collaboration design pattern addresses a recurring tension by arranging roles, knowledge, authority, commitment, feedback, and exit behavior. It normally needs at least:

- Context
- Forces
- Structure
- Protocol
- Consequences

Fan-out/Fan-in is a circuit shape. Orchestrator–Worker is a system composition. Either may participate in a design pattern, but neither becomes one merely by receiving a name.

Patterns can span L0, L1, and L2; they are not defined as an additional layer in this release.
