# When Agents Become Circuits

> From collaboration logic gates to stable Multi-Actor systems

Suppose you want several AI Agents to brainstorm a new product feature together.

The most direct approach is to clarify the problem, ask three Agents to generate ideas from different perspectives, wait for all of them, and then deduplicate, group, and combine the ideas. A Reviewer checks whether the idea set is rich enough. If it is not, the team changes the constraints and starts another bounded round.

It might look like this:

```text
Clarify the brainstorming problem
   ↓
Explore from different perspectives
   ├→ Agent A: user perspective ────────────┐
   ├→ Agent B: technical perspective ───────┼→ Collect / deduplicate / group → Review → Enough ideas?
   └→ Agent C: contrarian perspective ──────┘                                      ├→ Yes: Done
                                                                                     ├→ No, rounds remain: change constraints and brainstorm again
                                                                                     └→ No, round limit reached: Stop
```

It is easy to name this structure Brainstorming Team, Orchestrator–Worker, or a Multi-Agent brainstorming pattern.

Now erase “product brainstorming” and replace it with coding, incident diagnosis, or travel planning. Some things change, and some do not.

The concrete task changes: the participants, inputs, tools, and acceptance criteria are different. Once those business-specific labels are removed, however, the collaboration skeleton remains familiar:

- one thing completes before another begins;
- one piece of work expands into several branches;
- several branches later converge;
- the review outcome selects the next path;
- an unsatisfactory result starts another round;
- a satisfactory result ends the process.

In other words, the business goal, context, and content of each step keep changing. What remains is how work is assigned, when collaboration waits, how it separates and converges, and which conditions let it continue or end.

The cases at the top keep changing. Underneath them, the same small set of relationships keeps returning.

These relationships do not belong specifically to brainstorming, or even specifically to AI Agents.

They resemble `if`, `loop`, `call`, `fork`, and `join` in programming languages, or logic gates in digital circuits: each one is simple in isolation, but together they can form complex systems.

Yet gates are only part of a circuit. Once collaboration begins to wait, transfer responsibility, converge, or loop, the system must also remember where it is. More precisely, this article discusses L0 of an Actor circuit: the foundational collaboration-state transitions, together with the state and connections those transitions depend on.

One boundary should be made explicit at the start: **these foundational gates are not original inventions of this article.** The article reorganizes established structures and Multi-Actor engineering experience around a common Actor boundary, removing business-specific names and implementation details so the resulting circuit can be discussed, composed, and reviewed. Their research lineage appears below.

This article also takes a clear position:

> **Agents alone are not engineering.**

An Agent completing a task demonstrates the capability of a model and its tools. Several Agents placed together are still only a collection of capabilities. Engineering addresses a different problem: when participation grows, work runs longer, state keeps changing, and failures may happen at any moment, the system must still operate predictably.

Stability does not imply simplicity. Engineering means that even when complexity is real, work can still be split, run independently, wait, transfer, recover, converge, and terminate. Even if every Agent is individually capable, a system without a defined Fan-in, responsibility transfer, or terminal state can still repeat work, wait forever, or silently stop when nobody owns continuation.

The Actor circuit connects these levels. Downward, it connects individual Actor capabilities. Upward, it gives communication protocols and Multi-Actor systems a collaboration structure that can be described and reviewed. Whether that circuit can survive process exits, network failures, and machine outages depends on reliable state carriage and recovery mechanisms.

We therefore need to study more than prompts, model choice, and role design. We also need to study the stable structures from which collaboration itself is built.

---

## The Collaborating Subject: Actor

Although the industry usually calls these systems Multi-Agent Systems, this project calls them **Multi-Actor Systems** because real collaboration involves more than AI Agents.

A task may proceed like this:

```text
Human states the objective
  → AI Agent creates a plan
  → Script performs an operation
  → Service returns data
  → Human approves the result
```

If the foundational model contains only Agents, Humans, Scripts, and Services become exceptions. The system gradually accumulates several message models, state models, and control mechanisms.

This article therefore uses a broader term: **Actor**.

> **An Actor is not a technology category. It is a collaboration boundary.**

More concretely, an Actor is an independently identifiable collaboration boundary with state, capabilities, and a lifecycle. It receives input, advances state inside the boundary, and produces an observable outcome, but it need not possess human-like intelligence or use a large language model.

An Actor may be:

- a person;
- an AI Agent;
- a long-running Service;
- a Script execution that exits after producing an outcome;
- a Team that presents one external identity;
- an External System.

A static definition must be distinguished from an instance actively participating in collaboration:

| Static definition | Active collaborator |
| --- | --- |
| Service code and API | A Service instance receiving input, advancing state, and returning an outcome |
| Workflow definition | A Workflow instance that can wait, resume, and complete |
| Script file | A Script execution with concrete input and outcome |
| Agent prompt | A running Agent with a concrete lifecycle |

The left side says what something is. The right side says who is acting. A Service, Workflow, or Script is not automatically an Actor because of its category. It can be modeled as an Actor when it participates in a concrete piece of work through an independent collaboration boundary.

Actor boundaries are recursive. The same object may be one Actor from the outside and a complete system from the inside:

```text
External view: Research Team is one Actor

Internal view: Research Team
               ├─ Planner
               ├─ Search Agents
               └─ Reviewer
```

A Workflow behaves similarly. To its caller, it may be an independently identifiable Actor that can wait and return a result. Internally, it may be a collaboration circuit composed of logic gates and other Actors.

Actors do not need equal intelligence or equal authority. “Equal” means only that they can use the same collaboration language, rather than requiring a new process model for every participant type.

Multi-Actor collaboration therefore does not ask how several models chat. It asks:

> How do several participants with independent boundaries advance one piece of work together?

---

## What Is a Collaboration Logic Gate?

Collaboration continually changes state.

Work may begin without an owner and later be assigned to an Actor. One path may expand into three branches. An active path may begin waiting for Human confirmation.

Each such change can be viewed as a collaboration-state transition.

One Gate occurrence means one committed transition. A conversation that lasts minutes or days is not one giant Gate. Call opens Boundary State that Return or another declared outcome may resolve; Wait creates Wait State that Resume or another declared outcome may resolve; a Handoff protocol may exchange several messages before Transfer finally commits.

It also helps to reserve four different words:

- an event or signal may trigger a Gate;
- a Gate outcome says which structural change occurred;
- a completion signal says that some work reached a declared outcome;
- a work result is the document, value, artifact, or reference produced by that work.

A transition can be treated as a collaboration logic gate when it:

- does not depend on a particular business domain;
- has a stable, explicit effect;
- can connect to other operations;
- exposes when it commits and which Gate outcome it produces.

This model does not require mathematical irreducibility.

Retry, for example, can be decomposed into testing for failure, returning to an earlier operation, and trying again. Yet Retry has stable operational meaning and needs independent limits and side-effect treatment. Keeping it as a named derived form is useful.

The goal is not a minimal machine instruction set. It is a collaboration vocabulary that people can actually use.

---

## This Vocabulary Did Not Appear from Nowhere in the Agent Era

Engineering disciplines studied similar problems long before modern AI Agents appeared.

The 2003 paper [Workflow Patterns](https://research.tue.nl/en/publications/workflow-patterns-2/) systematically described Sequence, Parallel Split, Synchronization, Exclusive Choice, and other workflow-control structures. The later [Control-Flow Patterns](https://www.workflowpatterns.com/patterns/control/) expanded the vocabulary with Partial Join, Discriminator, Cancellation, Persistent Trigger, and Explicit Termination. Sequence, Fan-out, Fan-in, Branch, Race, and Quorum all have direct antecedents in this line of work.

The 2005 [Service Interaction Patterns](https://kodu.ut.ee/~dumas/ServiceInteractionPatterns/documentation.html) moved the problem from activities inside one workflow to interactions among independent participants: one exchange or many, one receiver or many, and whether the result returns to the requester or is routed to another participant.

Those works provide essential foundations, but they begin from Activities inside Workflows or from Services. In the AI era, Humans, Agents, Services, and Scripts increasingly share the same long-running task. Control flow, cross-participant interaction, and Agent orchestration have converged again.

This article does not claim to reinvent Sequence or Fan-out. Its classification comes from my Multi-Actor engineering practice and from observing existing Workflow, Service Interaction, and Agent frameworks. It attempts something different: use Actor as the common participant, then re-abstract, name, and place those established designs into a clear layering—L0 for human-readable collaboration semantics, L1 for structured Actor-circuit protocols, and L2 for complete Multi-Actor systems.

---

## Group One: Advance or End a Path

### Sequence: then

The simplest gate is Sequence: the successor starts after its predecessor completes.

```text
Analyze requirements → Change code → Run tests
```

Sequence expresses ordering only. Whether one Actor performs all three steps or three Actors perform one each is irrelevant.

It also exposes a common Agent-system mistake:

> “The request was sent” is not the same as “the previous step completed.”

The successor should open only after the predecessor produces a distinguishable completion state.

### Branch: if

Branch selects a set of candidate continuations according to an observable decision fact.

```text
Tests passed?
   ├→ Yes: release
   └→ No: revise
```

The decision may come from a program rule, AI Agent, Human, or External System. Branch defines selection, not how the decision is computed. If it selects several continuations that must advance independently, Fan-out activates them as separate branches.

### Stop: finish here

Stop moves the current collaboration scope into an explicit terminal state: succeeded, failed, cancelled, rejected, or not satisfied.

Stop applies only to its declared scope. Whether child scopes, sibling branches, or pending boundaries are cancelled, detached, or allowed to continue is a property of the surrounding circuit or L2 system.

This sounds obvious, yet many Agent systems have no real Stop. They merely reach a moment when nobody is speaking.

Silence cannot tell us whether work completed, failed, or was forgotten.

Workflow research distinguishes natural termination—nothing remains to do—from explicit termination by a declared action. The latter matters even more in Multi-Actor systems: if participants may be offline, disconnected, or waiting for external input, inactivity cannot be interpreted as completion.

---

## Group Two: Split Work; Sometimes Bring Results Back Together

### Fan-out: one becomes many

Fan-out expands one work path into several branches that may advance independently.

```text
                    ┌→ Agent A: find evidence
One research problem ├→ Agent B: find cases
                    └→ Agent C: find counterexamples
```

Common forms include:

- assigning different parts to different Actors;
- asking several Actors to solve the same problem independently;
- deliberately using different models, tools, or strategies to increase diversity.

Fan-out means the branches may advance independently. It does not guarantee simultaneous execution. Actual parallelism or queued execution is a runtime decision.

Branch and Fan-out answer different questions. Branch chooses candidate continuations; Fan-out turns several selected continuations into independently advancing paths. A single-choice Branch therefore needs no Fan-out.

It also does not promise that the branches will later return to one place. After Fan-out, a branch may:

- hand its work result directly to another Actor;
- continue through its own circuit;
- join a later convergence;
- wait, be cancelled, or terminate independently.

So Fan-out and Fan-in are not matching parentheses. Fan-out creates branches; what later consumes each branch's work result is a separate design decision.

### Fan-in: several signals enable one continuation

Fan-in observes a declared set of satisfaction signals and opens one shared successor after they satisfy a condition.

```text
Agent A completed ─┐
Agent B completed ─┼→ Fan-in updates Join State ── ready and unfired ─→ enable Next
Agent C completed ─┘
```

“Satisfy a condition” does not always mean waiting for every branch:

- wait for all required completion signals;
- use the first acceptable signal;
- continue after a required count or weight is reached.

Two questions must be separated:

> Fan-in decides when to continue. Summarization, voting, selection, and synthesis decide how to process work results.

A deadline is an alternative temporal boundary, not another result-selection rule. It may close the observation window and produce a Timeout path. If the circuit then continues with currently available inputs, that relationship and the downstream selection rule must remain explicit.

Fan-in is not simply “ask one Agent to summarize.”

Nor does Fan-in require those signals to come from one preceding Fan-out. Three independently started tasks, two Human approvals, or conditions restored after a restart may also converge if the circuit declares them as one input set.

When signals arrive one after another, they cannot each activate the shared successor immediately. The circuit needs a logical place to retain convergence state until the condition is satisfied:

```text
Completion A ─┐
Completion B ─┼→ Fan-in updates [ Join State: A ✓  B ✓  C … ]
Completion C ─┘                              ↓ ready and unfired
                                             enable Next

Work results ─→ [ Result State ] ─────────────→ Next reads them
```

This is the “slot” in an Actor circuit. Fan-in processes each qualifying arrival against Join State. It may record progress without opening the successor. Join State remembers membership, satisfied inputs, readiness, and whether the successor has already been activated; for one convergence instance, only the first change from unsatisfied to satisfied may open the successor, and the same logical input counts at most once. Result State separately retains the work results needed downstream. Neither is necessarily a queue or database; those are implementation choices.

Fan-in does not consume or merge work results. It only opens the continuation. If branches continue independently and no later condition depends on their joint progress, the circuit needs no Fan-in or Join State merely because the work began with Fan-out.

An `N → 1` drawing is not enough. At minimum, it must answer:

- does it wait for every possible branch or only the branches started in this round?
- is branch membership known at the start, or can new branches appear at runtime?
- what happens to late signals and still-running branches after convergence?
- can the gate reset for another round?

The fate of late work results is a separate Result Flow question: they may be retained, consumed elsewhere, or given a terminal disposition.

Workflow Patterns split apparently similar synchronization structures into several patterns precisely because these questions produce different execution semantics. In Multi-Actor systems, the differences are even more visible: dynamically created Agents may add branches, while a Human response remains pending.

### Simple Merge: paths meet without waiting

Sometimes an `N → 1` drawing is not Fan-in at all:

```text
Tests passed?
   ├→ Yes: release ─┐
   └→ No: revise ───┴→ Record decision
```

Only one branch is active, so the shared successor does not wait or accumulate signals. This is Simple Merge. Fan-in is required only when a declared set of independently satisfiable inputs must be synchronized.

### Race: the first qualifying input continues

Race is a common convergence policy. Several eligible inputs compete; the first one producing a qualifying signal wins. Any associated work result remains separate from the continuation decision.

```text
Candidate A ─┐
Candidate B ─┼→ First candidate that passes tests
Candidate C ─┘
```

The hard part is not selecting the winner. It is deciding the fate of the other branches: cancel them, retain them, ignore them, or keep them as fallbacks. If losing work has no defined fate, the Race is not finished.

If several qualifying signals appear almost simultaneously, the system must also say whether it uses priority, deterministic adjudication, or nondeterministic selection. “First” alone is not a complete definition.

---

## Group Three: Choose Actors and Assign Responsibility

The previous gates mainly change path shape. Once work crosses an Actor boundary, four dimensions must remain distinct:

1. **target selection** — which Actor is the candidate;
2. **call boundary** — whether a request expects a corresponding outcome;
3. **execution ownership** — which Actor owns the bounded work;
4. **continuation ownership** — which Actor decides what happens next.

These dimensions come from Service Interaction Patterns. Translated into Actor language, they explain why Route, Call, Delegate, and Transfer look similar but cannot be collapsed into a generic “handoff.”

### Route: who is the candidate?

Route selects an Actor as the candidate for a later responsibility-bearing transition.

```text
Refund issue    → Route → Refund Actor    → Delegate
Technical issue → Route → Technical Actor → Call
High-risk issue → Route → Human            → Transfer
```

Route does not itself deliver work, prove acceptance, or change execution or continuation ownership. Actor discovery, reliable delivery, and offline behavior belong to the communication protocol.

The successor need not be fixed at design time. Route may choose an Actor at runtime according to task content, current load, or an earlier result. But “how to choose” and “how to deliver after choosing” remain separate questions.

### Call: help me, then return

Call opens a bounded request/response relationship with another Actor while the caller retains its continuation.

```text
Actor A ── Call ──→ Actor B
Actor B ── Return ──→ Actor A's continuation
```

Call and Return are two different committed transitions. Actor B may execute a complex internal process between them, while Actor A still owns the outer continuation. Call does not reassign ownership of a pre-existing work scope.

Many “Agent as Tool” relationships have this shape. The called Agent may be powerful, but it remains behind a call boundary.

### Delegate: you own this bounded piece of work

Delegate assigns execution ownership of an explicitly bounded work scope to another Actor.

```text
Lead Agent ──delegate subtask──→ Worker
```

Call and Delegate describe different dimensions and may be composed. Call says that an outcome belongs to a request/response boundary. Delegate says who owns execution of an explicit work scope. A delegator may wait for an outcome or continue independently.

If acceptance is required, it must occur before the Delegate ownership change commits. Whether the Worker may redelegate, how much budget it receives, and which resources it can access are beyond the Gate itself.

### Transfer: you take over from here

Transfer commits who owns continuation for the current work.

```text
Call + Return: A → B → A
Transfer:      A → B → C → ...
```

In Call, B returns to A. In Transfer, A hands further progress to B, and A is no longer required for the path to continue.

The core of a Handoff is usually Transfer, but proposal, delivery, and acceptance are not yet Transfer. A complete Handoff defines which context moves and which acceptance rule authorizes the ownership commit. Those concerns belong to the next-layer communication protocol.

### Return: I am done; here is the outcome

Return is the Actor-produced outcome that resolves a Call Boundary State or an outcome boundary explicitly composed with Delegate. It carries a completion signal and, when applicable, a work result.

The outcome need not be success. It may be:

- completed;
- partially completed;
- failed;
- rejected;
- blocked;
- cancelled;

“The Worker stopped producing output” is not Return. Timeout or an observed Stop status declared to be boundary-resolving may resolve the same boundary through alternative outcomes; a Cancel request alone does not. Once the boundary resolves, a later conflicting outcome cannot advance the same continuation again.

An outcome such as `blocked` resolves the boundary only when the circuit declares it terminal for that relationship; otherwise the boundary remains pending.

### Executor, continuation owner, and observer are different

Actor B may execute work that later returns to Actor A. B may instead pass it to Actor C while A remains informed. Service Interaction Patterns call the latter a relayed request: an intermediary delegates work while retaining visibility into some later interaction.

Three relationships must therefore remain distinct:

- **who executes the current work**;
- **who owns the right and obligation to continue**;
- **who may observe the process and outcome**.

Delegate changes the executor. Transfer changes the continuation owner. Visibility belongs to the communication protocol or higher-level policy. Collapsing all three into “give it to another Agent” makes responsibility and context flow impossible to reason about after failure.

---

## Group Four: Manage Waiting and Directed Termination

### Wait: pause here

Multi-Actor collaboration naturally includes waiting: for Human approval, a Service response, another Actor, or a point in time.

```text
Submit request → Wait for Human approval
```

Wait preserves the path's continuation and names the outcomes that may resolve it. Waiting is not failure. It becomes a problem only when the circuit does not know what it is waiting for or which outcome may continue it.

### Resume: the awaited condition was satisfied

Resume resolves a Wait State through its declared success signal and restores the preserved continuation.

```text
Approval arrives → Resume → Continue
```

### Timeout: do not wait forever

Timeout places a temporal boundary around a Wait or another pending relationship.

```text
Wait for result
 ├→ Result arrives  → Resume  → Continue
 └→ Deadline passes → Timeout → Reroute / degrade / cancel / ask a Human
```

Timeout is not inherently failure. It resolves the waiting or controlling relationship through an alternative outcome. It does not necessarily stop the underlying work, so the surrounding circuit must define the fate of that work and any late result. Once one outcome resolves a Wait, a later conflicting signal cannot advance the same continuation again.

### Cancel: stop another live path

Cancel is a directed termination request. A Race winner, Human, or policy may ask a declared target scope to reach `Stop(cancelled)`.

```text
Cancel(target) → cancellation requested
target         → Stop(cancelled)
```

The request and the committed Stop are different facts. The circuit must identify the target and propagation boundary; the protocol and runtime decide how the cancellation reaches running work and how cleanup occurs.

---

## Group Five: Repeat Work with Bounds

### Retry: try again

Retry attempts an operation again under declared conditions.

Reliable Retry needs more than “retry on failure.” It must know:

- the maximum attempts;
- which errors are retryable;
- whether to wait between attempts;
- whether the previous attempt already produced an effect.

For Actors that modify files, send messages, or invoke external services, blind Retry can turn one failure into repeated writes.

### Loop: run another round

Loop repeats a collaboration path until a termination condition holds.

```text
Generate draft → Review quality → Good enough?
                                  ├→ Yes → Exit Loop → Next
                                  ├→ No, rounds remain → Revise ↺ Review quality
                                  └→ No, round limit reached → Stop(not_satisfied)
```

Generator–Critic and Evaluator–Optimizer both contain this structure.

Loop must have a bound on rounds, time, or cost. “The Agent thinks it can still improve” is not sufficient reason to run forever.

---

## From Combinational Circuits to Sequential Circuits

Gates describe transitions, but real collaboration also depends on facts remembered between transitions. In that sense, **a collaboration circuit is not made only of logic gates.**

If the next step depends only on current input, the collaboration resembles a combinational circuit:

```text
Tests passed?
   ├→ Yes: release
   └→ No: revise
```

Feed the current test result into Branch, and the next path can be selected without knowing earlier history.

Real Multi-Actor collaboration, however, also contains situations such as:

- Human approval has not arrived, so the system is waiting;
- two of three Workers have returned, while Fan-in still waits for one;
- Actor A has proposed a Handoff to Actor B, but Transfer has not committed;
- this is revision round three, with two rounds remaining;
- a result remains available after the path that produced it has completed.

In these cases, the next step depends not only on the current event but also on retained collaboration state. The result resembles a sequential circuit with memory:

```text
C_(k+1) = G(C_k, E_k)
```

Where:

- `C_k` is the state at a logical transition, not a global wall-clock snapshot;
- `E_k` is a newly observed signal, message, acceptance, or timeout;
- `G` is the collaboration logic that determines the transition;
- `C_(k+1)` is the successor collaboration state.

In digital circuits, flip-flops and registers retain prior state. In an Actor circuit, the corresponding elements are not particular hardware components, but collaboration facts that must survive one transition so later transitions can observe them.

An Actor circuit therefore contains at least three kinds of elements:

```text
State transitions  Sequence / Branch / Fan-out / Fan-in / Transfer
State memory       current path, owner, pending boundary, retained work results, join signals, continuation
Feedback edges     Loop / Retry / revise-until-verified
```

“State memory” does not mean that L0 selects a database. L0 says only which facts must remain distinguishable for the collaboration semantics to hold.

Looking at the same circuit from the top, L0 must keep four aspects distinguishable:

```text
Flow            Which path may continue?
Result Flow     Who or what consumes each work result, now or later?
Responsibility  Who executes, and who owns continuation?
Lifecycle       Is the work active, waiting, or terminal?
```

The missing piece in many gate-only diagrams is Result Flow. An arrow may show that a branch completed, but not whether its work result was handed off immediately, retained for a later consumer, used jointly downstream, or discarded. Completion signals may accumulate in Join State; work results remain in Result State. Those choices belong to L0 semantics even though the result's business schema does not.

---

## A Gate Is Not a System

Return to the research team from the beginning:

```text
Understand the question
  → Fan-out
      ├→ Delegate A + outcome boundary → Return A ─┐
      ├→ Delegate B + outcome boundary → Return B ─┼→ Fan-in(all completion signals)
      └→ Delegate C + outcome boundary → Return C ─┘
  → Synthesize retained results
  → Review
  → Branch: is quality sufficient?
      ├→ No  → Loop to Revise → Review
      └→ Yes → Stop(succeeded)
```

Now the levels are clearer:

- Fan-out creates the independent research branches;
- Delegate assigns each bounded research scope, while Return resolves its declared outcome boundary;
- Fan-in observes completion signals, while Synthesize consumes retained work results;
- Branch selects the quality path, Loop provides the bounded feedback edge, and Stop closes the declared scope;
- Orchestrator–Worker is an L2 composition whose internal circuit uses these L0 elements and whose interactions require L1 protocol choices;
- Research Team is one application of that circuit in the research domain.

These levels should not be conflated.

The same L0 vocabulary can describe a complete circuit or only the fragment relevant to a design question. It does not require an executable start node, global IDs, or a storage design. Those concerns enter only when the conceptual circuit becomes a concrete protocol and system:

```text
Actor Foundation
    Defines who participates

L0  Actor Circuit
    Defines collaboration-state transitions, remembered facts, and connections

L1  Actor Circuit Protocol
    Represents concrete circuit instances, correlation, timing, delivery,
    acknowledgement, and interaction protocol

L2  Multi-Actor Systems
    Compose Actors, circuits, and protocols into complete systems such as
    Orchestrator–Worker, Group Chat, and Evaluator–Optimizer

Execution Plane
    Implements the storage, timers, recovery, and other guarantees a system requires
```

This article stops at L0. A short-lived circuit may keep its state in memory; a system promising to continue across time or failure needs stronger execution guarantees. In either case, L0 says what must remain distinguishable, not which storage technology implements it.

Only after establishing a stable foundational vocabulary can we discuss a Multi-Actor system precisely: where it fans out, whether and how results later converge, who is merely called and who truly takes over, whether the system is waiting or already terminated, and whether a supposed new pattern introduces a new collaboration mechanism or merely reconnects familiar circuit elements.

Many structures called Multi-Agent Patterns today are Actor circuits composed from state transitions, state memory, and feedback connections.

Seeing those foundational elements is the first step toward understanding Multi-Actor collaboration.

---

## From a Vocabulary to a Skill, Then a CLI

I want the Actor circuit to become more than an article.

The first step is an open-source Skill. Today's Agents are capable enough that a Skill need not prescribe “do this first, then do that.” More importantly, it should tell them which stable concepts exist, where their boundaries lie, and when Gate, State, Circuit, Protocol, and System must be distinguished.

L0 is defined first. L1 begins as experimental YAML that turns L0 semantics into structured circuit definitions, while L2 remains intentionally unfinished. After the model has been exercised in real design and review tasks and its concepts have stabilized, a CLI can follow.

The CLI will not be another Multi-Agent orchestration framework. It will begin as a design tool for collaboration systems: helping Humans and Agents use the same vocabulary, decompose structures such as Orchestrator–Worker and Group Chat into concrete circuit elements, and expose underspecified semantics.

For example, it may eventually help identify whether:

- every Fan-out branch has an understandable fate, and every relevant work result has a direct consumer, downstream joint consumer, or terminal disposition;
- Branch is being used for selection and Fan-out only where several selected paths advance independently;
- a deferred work result names both the Result State that retains it and the later consumer;
- a Fan-in declares which satisfaction signals it waits for and never hides result processing;
- an `N → 1` shape is Simple Merge or Fan-in for an explicit reason;
- a Race defines the fate of other participants;
- an interaction is Call, Delegate, Transfer, or an explicit composition of them;
- Wait preserves its continuation and names the outcomes that may resolve it;
- an asynchronous Handoff leaves responsibility state that can be acknowledged and recovered;
- each Stop names the exact scope it terminates.

It may also turn a collaboration description into a reviewable diagram, document, or protocol draft, so the circuit can be read by people and applied directly by Agents.

The CLI does not exist yet. It is a direction for after the model stabilizes. But it represents the intended destination: not another glossary, but an engineering language for Multi-Actor collaboration that can be applied, checked, and extended.

---

## References

- [Workflow Patterns](https://research.tue.nl/en/publications/workflow-patterns-2/), W.M.P. van der Aalst, A.H.M. ter Hofstede, B. Kiepuszewski, and A.P. Barros, 2003.
- [Workflow Control-Flow Patterns](https://www.workflowpatterns.com/patterns/control/), Workflow Patterns Initiative.
- [Service Interaction Patterns](https://kodu.ut.ee/~dumas/ServiceInteractionPatterns/documentation.html), Alistair Barros, Marlon Dumas, and Arthur ter Hofstede, 2005.
- [Choosing workflow type in Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/choosing-workflow-type.html), an example of durable and non-durable execution semantics under the same state-machine language.
- [Temporal Platform Documentation](https://docs.temporal.io/), an engineering reference for durable execution, state recovery, and long-running Workflows.
- [Agent2Agent Protocol Specification](https://a2a-protocol.org/latest/specification/), a reference for stateful Tasks, lifecycle, and asynchronous interaction protocols.
