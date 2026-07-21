# When Agents Become Circuits

> From collaboration logic gates to stable Multi-Agent systems

Suppose you want several AI Agents to produce a research report together.

The most direct approach might be to let one Agent understand the question, assign three directions to three other Agents, wait for all of them, ask another Agent to synthesize the results, and then send the draft to a Reviewer. If the result is not good enough, it goes back for another revision.

It might look like this:

```text
Understand the question
   ↓
Split the work
   ├→ Agent A: direction one ───┐
   ├→ Agent B: direction two ───┼→ Synthesize → Review → Done
   └→ Agent C: direction three ─┘                 ↑      │
                                                  └──────┘
```

It is easy to name this structure Research Team, Orchestrator–Worker, or a Multi-Agent Research Pattern.

But replace “research report” with coding, incident diagnosis, travel planning, or financial analysis, and most of the structure remains unchanged:

- one thing completes before another begins;
- one piece of work expands into several branches;
- several branches later converge;
- the result selects the next path;
- an unsatisfactory result starts another round;
- a satisfactory result ends the process.

These operations do not belong specifically to research, or even specifically to AI Agents.

They resemble `if`, `loop`, `call`, `fork`, and `join` in programming languages, or logic gates in digital circuits: each one is simple in isolation, but together they can form complex systems.

Yet gates are only part of a circuit. Once collaboration begins to wait, transfer responsibility, converge, or loop, the system must also remember where it is. More precisely, this article discusses L0 of an Actor circuit: the foundational collaboration-state transitions, together with the state and connections those transitions depend on.

One boundary should be made explicit at the start: **these foundational gates are not original inventions of this article.** Sequence, Branch, Fan-out, Fan-in, Wait, and similar structures have long existed in workflow research, distributed systems, and Agent frameworks. What this article does is place my own Multi-Actor engineering experience alongside those established designs, remove business-specific vocabulary, implementation details, and role names, and abstract them into a shared vocabulary that can be discussed, composed, and reviewed.

In other words, the goal is not to invent gates that have never existed. It is to draw the circuit they form together.

This article also takes a clear position:

> **Agents alone are not engineering.**

An Agent completing a task demonstrates the capability of a model and its tools. Several Agents placed together are still only a collection of capabilities. Engineering addresses a different problem: when participation grows, work runs longer, state keeps changing, and failures may happen at any moment, the system must still operate predictably.

Stability does not imply simplicity. Engineering means that even when complexity is real, work can still be split, run independently, wait, transfer, recover, converge, and terminate. Even if every Agent is individually capable, a system without a defined Fan-in, responsibility transfer, or terminal state can still repeat work, wait forever, or silently stop when nobody owns continuation.

The Actor circuit connects these levels. Downward, it connects individual Actor capabilities. Upward, it gives communication protocols and Multi-Agent systems a collaboration structure that can be described and reviewed. Whether that circuit can survive process exits, network failures, and machine outages depends on reliable state carriage and recovery mechanisms.

We therefore need to study more than prompts, model choice, and role design. We also need to study the stable structures from which collaboration itself is built.

---

## The Collaborating Subject: Actor

Although the industry usually calls these systems Multi-Agent Systems, real collaboration involves more than AI Agents.

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

More concretely, an Actor is an addressable collaboration boundary. It receives input, advances state inside the boundary, and produces an observable outcome. It may have capabilities and a lifecycle, but it need not possess human-like intelligence or use a large language model.

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
| Workflow definition | A Workflow instance with a `workflow_id` that can wait, resume, and complete |
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

A Workflow behaves similarly. To its caller, it may be an addressable Actor that can wait and return a result. Internally, it may be a collaboration circuit composed of logic gates and other Actors.

Actors do not need equal intelligence or equal authority. “Equal” means only that they can use the same collaboration language, rather than requiring a new process model for every participant type.

Multi-Actor collaboration therefore does not ask how several models chat. It asks:

> How do several participants with independent boundaries advance one piece of work together?

---

## What Is a Collaboration Logic Gate?

Collaboration continually changes state.

Work may begin without an owner and later be assigned to an Actor. One path may expand into three branches. An active path may begin waiting for Human confirmation.

Each such change can be viewed as a collaboration-state transition.

A transition can be treated as a collaboration logic gate when it:

- does not depend on a particular business domain;
- has a stable, explicit effect;
- can connect to other operations;
- exposes when it happens and what result it produces.

This model does not require mathematical irreducibility.

Retry, for example, can be decomposed into testing for failure, returning to an earlier operation, and trying again. Yet Retry has stable operational meaning and needs independent limits, budgets, and side-effect treatment. Keeping it as a named gate is useful.

The goal is not a minimal machine instruction set. It is a collaboration vocabulary that people can actually use.

---

## This Vocabulary Did Not Appear from Nowhere in the Agent Era

Engineering disciplines studied similar problems long before modern AI Agents appeared.

The 2003 paper [Workflow Patterns](https://research.tue.nl/en/publications/workflow-patterns-2/) systematically described Sequence, Parallel Split, Synchronization, Exclusive Choice, and other workflow-control structures. The later [Control-Flow Patterns](https://www.workflowpatterns.com/patterns/control/) expanded the vocabulary with Partial Join, Discriminator, Cancellation, Persistent Trigger, and Explicit Termination. Sequence, Fan-out, Fan-in, Branch, Race, and Quorum all have direct antecedents in this line of work.

The 2005 [Service Interaction Patterns](https://kodu.ut.ee/~dumas/ServiceInteractionPatterns/documentation.html) moved the problem from activities inside one workflow to interactions among independent participants: one exchange or many, one receiver or many, and whether the result returns to the requester or is routed to another participant.

Those works provide essential foundations, but they begin from Activities inside Workflows or from Services. In the AI era, Humans, Agents, Services, and Scripts increasingly share the same long-running task. Control flow, cross-participant interaction, and Agent orchestration have converged again.

This article does not claim to reinvent Sequence or Fan-out. Its classification comes from my Multi-Actor engineering practice and from observing existing Workflow, Service Interaction, and Agent frameworks. It attempts something different: use Actor as the common participant, then re-abstract, name, and place those established designs into a clear layering—L0 for collaboration-state transitions, L1 for communication protocol, and L2 for complete Multi-Actor systems.

---

## Group One: Move Work Forward

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

Branch selects the next path according to the current result.

```text
Tests passed?
   ├→ Yes: release
   └→ No: revise
```

The decision may come from a program rule, AI Agent, Human, or External System. Branch does not care who decides; it cares which path the result enables.

### Stop: finish here

Stop moves the current collaboration scope into an explicit terminal state: succeeded, failed, cancelled, rejected, or exhausted.

This sounds obvious, yet many Agent systems have no real Stop. They merely reach a moment when nobody is speaking.

Silence cannot tell us whether work completed, failed, or was forgotten.

Workflow research distinguishes natural termination—nothing remains to do—from explicit termination by a declared action. The latter matters even more in Multi-Actor systems: if participants may be offline, disconnected, or waiting for external input, inactivity cannot be interpreted as completion.

---

## Group Two: Split Work and Bring It Back Together

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

### Fan-in: many become one

Fan-in waits until several paths satisfy a condition, then opens one shared successor.

```text
Agent A result ─┐
Agent B result ─┼→ Fan-in → Next
Agent C result ─┘
```

“Satisfy a condition” does not always mean waiting for every branch:

- wait for all results;
- use the first acceptable result;
- continue after a required count is reached;
- use the available results when a deadline arrives.

Two questions must be separated:

> Fan-in decides when to converge. Summarization, voting, selection, and synthesis decide how to process results.

Fan-in is not simply “ask one Agent to summarize.”

An `N → 1` drawing is not enough. At minimum, it must answer:

- does it wait for every possible branch or only the branches started in this round?
- is branch membership known at the start, or can new branches appear at runtime?
- what happens to late results after convergence?
- can the gate reset for another round?

Workflow Patterns split apparently similar synchronization structures into several patterns precisely because these questions produce different execution semantics. In Multi-Actor systems, the differences are even more visible: dynamically created Agents may add branches, while a Human response remains pending.

### Race: the first acceptable result continues

Race is a common convergence policy. Several branches try at once; the first acceptable result wins.

```text
Candidate A ─┐
Candidate B ─┼→ First candidate that passes tests
Candidate C ─┘
```

The hard part is not selecting the winner. It is deciding the fate of the other branches: cancel them, retain them, ignore them, or keep them as fallbacks. If losing work has no defined fate, the Race is not finished.

If several acceptable results arrive almost simultaneously, the system must also say whether it uses priority, deterministic adjudication, or nondeterministic selection. “First” alone is not a complete definition.

---

## Group Three: Decide Who Continues

The previous gates mainly change path shape. Once work crosses an Actor boundary, three more questions matter:

1. is there one receiver or several?
2. is this a single request/response or an extended interaction?
3. does the result return to the original Actor or continue through another Actor?

These dimensions come from Service Interaction Patterns. Translated into Actor language, they explain why Route, Call, Delegate, and Transfer look similar but cannot be collapsed into a generic “handoff.”

### Route: who should receive it?

Route selects a suitable Actor for the work.

```text
Refund issue    → Refund Agent
Technical issue → Technical Agent
High-risk issue → Human
```

Route selects a possible successor. Actor discovery, reliable delivery, and offline behavior belong to the communication protocol.

The successor need not be fixed at design time. Route may choose an Actor at runtime according to task content, current load, or an earlier result. But “how to choose” and “how to deliver after choosing” remain separate questions.

### Call: help me, then return

Call means that one Actor asks another to perform work and expects the result to return.

```text
Actor A → Call Actor B → Result → Actor A
```

Actor B may execute a complex internal process, but Actor A still controls the outer flow and owns continuation.

Many “Agent as Tool” relationships have this shape. The called Agent may be powerful, but it remains behind a call boundary.

### Delegate: you own this bounded piece of work

Delegate does more than ask a question. It assigns bounded work to another Actor to advance.

```text
Lead Agent ──delegate subtask──→ Worker
```

Call resembles “calculate this and return the answer.” Delegate resembles “complete this part and deliver it.”

Whether the Worker may redelegate, how much budget it receives, and which resources it can access are beyond the gate itself.

### Transfer: you take over from here

Transfer changes who owns continuation for the current work.

```text
Call:      A → B → A
Transfer:  A → B → C → ...
```

In Call, B returns to A. In Transfer, A hands further progress to B, and A is no longer required for the path to continue.

The core of a Handoff is usually Transfer. A complete Handoff must additionally define which context moves, how receipt is accepted, and what happens when transfer fails. Those concerns belong to the next-layer communication protocol.

### Return: I am done; here is the outcome

Return closes a Call or Delegate and sends state back to a pre-existing continuation point.

The outcome need not be success. It may be:

- completed;
- partially completed;
- failed;
- rejected;
- blocked.

“The Worker stopped producing output” is not Return. Return must tell the waiting Actor what happened.

### Executor, continuation owner, and observer are different

Actor B may execute work that later returns to Actor A. B may instead pass it to Actor C while A remains informed. Service Interaction Patterns call the latter a relayed request: an intermediary delegates work while retaining visibility into some later interaction.

Three relationships must therefore remain distinct:

- **who executes the current work**;
- **who owns the right and obligation to continue**;
- **who may observe the process and outcome**.

Delegate changes the executor. Transfer changes the continuation owner. Visibility belongs to the communication protocol or higher-level policy. Collapsing all three into “give it to another Agent” makes responsibility and context flow impossible to reason about after failure.

---

## Group Four: Let Collaboration Cross Time

### Wait: pause here

Multi-Actor collaboration naturally includes waiting: for Human approval, a Service response, another Actor, or a point in time.

```text
Submit request → Wait(Human approval) → Continue
```

Waiting is not failure. It becomes a problem only when the system does not know what it is waiting for or how to resume.

### Timeout: do not wait forever

Timeout bounds waiting.

```text
Wait
 ├→ Result arrives: continue
 └→ Deadline passes: reroute / degrade / cancel / ask a Human
```

Timeout is not inherently failure. It creates a new fact from which the circuit can choose another path.

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
Generate draft → Review quality → Passed?
   ↑                              │
   └──────────── No ──────────────┘
```

Generator–Critic and Evaluator–Optimizer both contain this structure.

Loop must have a bound on rounds, time, or cost. “The Agent thinks it can still improve” is not sufficient reason to run forever.

---

## From Combinational Circuits to Sequential Circuits

At this point, one potentially misleading statement needs correction: **a collaboration circuit is not made only of logic gates.**

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
- Actor A is transferring work to Actor B, but B has not accepted;
- this is revision round three, with two rounds remaining;
- a result arrived late, and the system must identify its task, branch, and round.

In these cases, the next step depends not only on the current event but also on retained collaboration state. The result resembles a sequential circuit with memory:

```text
C(t + 1) = G(C(t), E(t))
```

Where:

- `C(t)` is the current collaboration state;
- `E(t)` is a newly observed result, message, acceptance, or timeout;
- `G` is the collaboration logic that determines the transition;
- `C(t + 1)` is the successor collaboration state.

In digital circuits, flip-flops and registers retain prior state. In an Actor circuit, the corresponding elements are not particular hardware components, but collaboration facts that must survive one transition so later transitions can observe them.

An Actor circuit therefore contains at least three kinds of elements:

```text
State transitions  Sequence / Branch / Fan-out / Fan-in / Transfer
State memory       current path, owner, wait condition, partial results, continuation
Feedback edges     Loop / Retry / revise-until-verified
```

“State memory” does not mean that L0 selects a database. L0 says only which facts must remain distinguishable for the collaboration semantics to hold.

An asynchronous Handoff, for example, cannot be fully represented as:

```text
Actor A → Actor B
```

It contains some responsibility-state progression:

```text
A owns continuation
   ↓ proposes transfer
Waiting for B to accept
   ↓ B accepts
Transfer committed; B owns continuation
```

If A exits after proposing the transfer, the system must still know whether to wait for B, return work to A, or reroute it. Otherwise, the arrow represents an intention rather than a stable transfer.

Synchronous calls do not avoid the problem. They temporarily hide the continuation in a call stack:

```text
A → Call B
A's call stack waits
B returns
A continues from the next step
```

The call stack is also a state medium, but it usually lives only inside the current process. Asynchronous tasks, long waits, and cross-Actor Loops force those implicit states to become explicit.

Four locations should therefore be distinguished:

```text
L0                 Defines what collaboration must remember
L1                 Defines how that state is expressed, correlated, and accepted across Actors
Durable Execution  Keeps required state recoverable across time and failure
Runtime            Chooses a database, log, queue, or another concrete medium
```

Durable execution is not another logic gate and should not be forced into L1. It is an execution plane supporting the entire circuit. The same Branch, Fan-out, and Loop may run within one process or in an environment that persists history, timers, and continuations. The circuit semantics remain the same; the engineering guarantees differ.

This is why Agents alone are not engineering. Engineering requires more than participants that can act. After a participant disappears, collaboration must still know what happened, who owns responsibility, and where to continue.

---

## A Gate Is Not a System

Return to the research team from the beginning:

```text
Understand the question
  → Fan-out
  → Delegate to several Actors
  → Wait
  → Fan-in
  → Branch: is quality sufficient?
      ├→ No: Loop
      └→ Yes: Stop
```

Now the levels are clearer:

- Fan-out, Fan-in, and Branch are collaboration-state transitions;
- Wait leaves a future continuation that can be resumed;
- Loop is a feedback structure made from decisions, state, and a back edge;
- Orchestrator–Worker is a collaboration circuit composed from these elements;
- Research Team is one application of that circuit in the research domain.

These levels should not be conflated.

Circuits also do not run themselves. For different Actors to collaborate, the system must answer how Actors are identified, how messages are sent, how work acceptance is acknowledged, and how results correlate to the original task. If work must survive failure, the system must additionally say which state is durable and how an unfinished continuation is recovered.

Correlation is particularly easy to underestimate. After Fan-out, autonomous Actors return at different times. The system must know the task, branch, and round to which each result belongs. Service Interaction Patterns treat correlation as a prerequisite for multi-party interaction, not as an incidental business field.

The system can first be described through three semantic layers, supported by an execution plane:

```text
Actor Foundation
    Defines who participates

L0  Actor Circuit
    Defines state transitions, collaboration state, continuations, and feedback

L1  Multi-Actor Communication Protocol
    Defines identity, addressing, messages, delivery, acknowledgement,
    correlation, and transfer protocol

L2  Multi-Agent Systems
    Compose Actors, circuits, and protocol into complete systems such as
    Orchestrator–Worker, Group Chat, and Evaluator–Optimizer

Durable Execution Plane
    Retains state, history, timers, and continuations for circuits that require it,
    and supports recovery and takeover of unfinished work
```

This does not mean that every Actor circuit needs a heavyweight workflow engine. Short-lived, disposable collaboration may remain in memory. But any system promising to continue across time, processes, or failures must declare its durability boundary.

This article still stops at L0. The Durable Execution Plane appears here only to show that `C(t)` is not an empty abstraction: stable Wait, Transfer, Fan-in, and Loop semantics depend on some state remaining observable. Concrete protocols and storage implementations belong to later work.

Only after establishing a stable foundational vocabulary can we discuss a Multi-Agent system precisely: where it fans out and converges, who is merely called and who truly takes over, whether the system is waiting or already terminated, and whether a supposed new pattern introduces a new collaboration mechanism or merely reconnects familiar circuit elements.

Many structures called Multi-Agent Patterns today are Actor circuits composed from state transitions, state memory, and feedback connections.

Seeing those foundational elements is the first step toward understanding Multi-Actor collaboration.

---

## From a Vocabulary to a Skill, Then a CLI

I want the Actor circuit to become more than an article.

The first step is an open-source Skill. Today's Agents are capable enough that a Skill need not prescribe “do this first, then do that.” More importantly, it should tell them which stable concepts exist, where their boundaries lie, and when Gate, State, Circuit, Protocol, and System must be distinguished.

L0 will be defined first. L1 and L2 will expose boundaries only and remain intentionally unfinished. After the model has been exercised in real design and review tasks and its concepts have stabilized, a CLI can follow.

The CLI will not be another Multi-Agent orchestration framework. It will begin as a design tool for collaboration systems: helping Humans and Agents use the same vocabulary, decompose structures such as Orchestrator–Worker and Group Chat into concrete circuit elements, and expose underspecified semantics.

For example, it may eventually help identify whether:

- every Fan-out has an appropriate Fan-in;
- a Fan-in declares which branches it waits for;
- a Race defines the fate of other participants;
- an interaction is Call, Delegate, or Transfer;
- Wait has a Timeout, continuation state, and resumption condition;
- an asynchronous Handoff leaves responsibility state that can be acknowledged and recovered;
- the system has an explicit Stop.

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
