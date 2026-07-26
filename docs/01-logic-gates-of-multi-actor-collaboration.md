# Logic Gates of Multi-Actor Collaboration

> A foundational vocabulary for describing how Humans, Agents, Services, and Scripts advance work together

Over the past two years, “Multi-Agent Patterns” has rapidly become an all-inclusive catalog: Sequential, Concurrent, Routing, Handoff, Group Chat, Orchestrator–Worker, Evaluator–Optimizer, Debate, and more.

These names describe different forms of collaboration, but they often mix concepts from different levels.

Sequential and Fan-out/Fan-in resemble `;`, `fork`, and `join` in programming languages. Orchestrator–Worker and Group Chat are complete execution structures composed from several basic operations. Calling all of them design patterns obscures a more fundamental question:

> When several independent participants advance one piece of work together, what are the most basic reusable collaboration operations?

This article calls those operations **logic gates of Multi-Actor collaboration**.

“Logic gate” is an engineering analogy rather than a strict mathematical mapping to digital circuits. Each gate has stable, business-independent input/output semantics, and gates can be composed into more complex Multi-Actor systems.

This article discusses only two subjects:

1. what an Actor is;
2. how L0 describes a collaboration circuit through Gates, Result Flow, State Elements, and continuations.

Communication protocols, framework implementations, concrete Multi-Actor systems, and design patterns above the gate level are outside its scope.

An L0 circuit may describe a complete collaboration or only the fragment needed to explain one design question. It does not require an executable start node or a globally closed workflow.

---

## 1. From Agent to Actor

Multi-Agent remains the most common entry point today, but the participants in real collaboration systems are not limited to AI Agents.

A piece of work may be initiated by a Human, planned by an AI Agent, executed by a Script, blocked on a Service response, and finally approved by another Human. If the foundational model recognizes only Agents, every other participant is reduced to a tool or special node, requiring separate collaboration semantics.

A more stable abstraction is the Actor.

In this article:

> **An Actor is a subject with an independent identity boundary, state, capabilities, and lifecycle that can participate in collaboration-state transitions.**

In compact form:

```text
Actor = Identity + State + Capability + Lifecycle
```

An Actor may be a:

- Human;
- AI Agent;
- Service;
- Script;
- Team;
- External System.

“Equal” does not mean that all Actors have equal permissions or capabilities, or that they possess human-like autonomy. It means only that they participate through the same basic relationships in the collaboration model. Adding a new participant type should not require adding a new control-flow language.

### What an Actor is not

An Actor is not a model. An AI Actor may change models while retaining identity continuity.

An Actor is not a process. One execution may stop while the Actor continues to exist.

An Actor is not a role. “Reviewer” is a responsibility in a particular collaboration; the same Actor may assume different roles in different tasks.

An Actor also need not be one natural person or one program. If the outside world observes a stable identity and collaboration boundary, a Team can be modeled as a composite Actor.

Multi-Actor collaboration therefore does not ask how several models chat. It asks how several participants with independent boundaries jointly change the state of a piece of work.

---

## 2. What Is a Collaboration Logic Gate?

Let the collaboration state visible at logical transition `k` be `C_k`. This is an observation order, not a globally synchronized wall clock.

This article does not prescribe where that state lives or which protocol Actors use to exchange information. It may exist in memory, a database, a filesystem, a messaging system, or human understanding.

A collaboration logic gate is a class of stable state transition:

```text
g: (C_k, e_k) → (C_k+1, o_k)
```

It receives the current collaboration state and an observable event or condition, then commits a new state and a structural Gate outcome.

One Gate occurrence represents one committed transition. Long-running interaction remains visible as several Gates connected through State Elements: Call opens Boundary State and Return or another declared outcome resolves it; Wait creates Wait State and Resume or another declared outcome resolves it. Delivery, acceptance, and correlation may determine whether a transition can commit, but they are not hidden inside the Gate.

Four terms should not be mixed:

- an **event or signal** may enable a Gate;
- a **Gate outcome** describes its structural result, such as a selected path;
- a **completion signal** proves that a declared scope reached an outcome;
- a **work result** is a value, artifact, or reference produced by Actor work.

For example:

- Fan-out expands one work path into several branches;
- Fan-in observes declared satisfaction signals and enables one continuation;
- Branch selects a set of candidate continuations;
- Transfer moves continuation ownership from one Actor to another;
- Wait preserves the current continuation while it awaits one of its declared resolution outcomes.

### Deciding whether an operation is an L0 gate

This article uses four criteria.

#### 1. Business-independent

Its semantics remain intact when domain terms such as coding, research, support, and approval are replaced.

#### 2. Semantically stable

It clearly changes something: the number of paths, the selected successor, the structural fate of an outcome, a waiting condition, or the Actor that owns continuation.

#### 3. Composable

It can participate in a larger collaboration structure rather than describing only a complete product or fixed team.

#### 4. Observable at the boundary

The system can distinguish the fact that enables the transition from the committed state change it produces.

“Impossible to decompose further” is not a criterion here.

Fan-out can be implemented as repeated dispatch. Loop can be expanded into Branch plus a back edge. Retry can be explained as another attempt on a failure branch. A stable recurring concept can remain useful as a configured or derived form without being promoted to a base Gate.

The goal is a practical collaboration vocabulary, not a theoretically minimal machine instruction set.

---

## 3. L0: Collaboration Logic Gates

The following is a candidate L0 vocabulary. Configured and derived forms appear near the base semantics they refine; the heading labels and classification below keep their level explicit.

| Classification | Members |
| --- | --- |
| Base Gates | Sequence, Branch, Fan-out, Fan-in, Route, Call, Delegate, Transfer, Return, Wait, Resume, Stop |
| Configured or derived forms | Race, Quorum, Simple Merge, Timeout, Cancel, Retry, Loop |

Base Gates are first-class committed transitions. The other names remain useful because they express recurring policies or circuit structures, but they are not peers of every base Gate.

### Sequence — base Flow Gate

A successor may begin only after the current step completes.

```text
A → B → C
```

Sequence defines ordering only. It does not require A, B, and C to be executed by the same Actor, nor does it define how information moves between them.

Core semantics:

```text
complete(A) enables B
```

A common mistake is to confuse “invoked” with “completed.” In a Multi-Actor environment, sending a request and satisfying a successor condition are different facts.

### Branch — base Flow Gate

Select a set of candidate continuations according to the current state.

```text
               ┌→ if A → Candidate path A
Input → Branch ├→ if B → Candidate path B
               └→ if C → Candidate path C
```

Branch is defined by how a decision changes successor paths, not by who makes that decision.

```text
condition(C) → selected continuation set
```

The condition may come from a deterministic rule, a Human decision, an Agent judgment, or an external event. The decision mechanism belongs to a higher level; Branch expresses only the selection. The example may select one candidate or a declared subset. When several selected continuations must advance independently, Fan-out performs the path expansion.

### Fan-out — base Flow Gate

Expand one input path into multiple work branches that can advance independently.

```text
          ┌→ Active branch A
Input ────├→ Active branch B
          └→ Active branch C
```

Core semantics:

```text
1 work branch → N child branches
```

Fan-out may be configured in several common ways:

- **Partition**: different branches process different parts;
- **Replication**: several branches solve the same problem independently;
- **Diversification**: branches deliberately use different models, roles, or strategies.

These configurations change branch content and participation, but not the basic shape of Fan-out.

Fan-out does not imply parallel execution. It says that branches may advance independently; the runtime may run them concurrently or serialize them under resource constraints.

Fan-out also does not imply a later Fan-in. A branch may hand its result directly to another Actor, continue through its own circuit, participate in a later convergence, wait, be cancelled, or terminate. Fan-out creates independent paths; it does not decide their eventual relationship.

### Fan-in — base Flow Gate

Observe the satisfaction state of multiple declared inputs and enable one successor after a convergence condition is satisfied.

```text
Branch A ─┐
Branch B ─┼→ Fan-in → Next
Branch C ─┘
```

Core semantics:

```text
N declared inputs → 1 continuation activation
```

Fan-in must declare a convergence condition:

- **All**: all designated branches complete;
- **First**: the first acceptable satisfaction signal arrives;
- **Quorum**: a specified count or weight is reached.

A deadline is an alternative temporal boundary, not another result-selection rule. It may close the observation window and produce a Timeout path. If the circuit then continues with currently available inputs, that relationship and the downstream result-processing rule must remain explicit.

Fan-in decides when and from which declared inputs the circuit continues. It does not decide how work results are processed. Concatenation, voting, selecting the best available result, synthesis, and Reduce are downstream result-processing strategies and should not be hidden inside Fan-in semantics.

Fan-in does not require a preceding Fan-out. It may observe any declared set of satisfaction signals or conditions. When signals arrive at different logical transitions, Fan-in processes each qualifying arrival against Join State. An arrival may record progress without enabling anything. For one convergence, only the first transition from unsatisfied to satisfied may fire the successor, and one logical input may satisfy the condition only once:

```text
Signal A ─┐
Signal B ─┼→ Fan-in updates Join State ── ready and unfired ─→ enable Next
Signal C ─┘

Result A ─┐
Result B ─┼→ Result State ────────────────→ Next reads results
Result C ─┘
```

Join State is a logical convergence slot, not a prescribed queue or database. It may reference Result States, but Fan-in neither merges nor consumes their business content. If branches continue independently and no later condition depends on their joint progress, the circuit needs no Fan-in or Join State merely because the work began with Fan-out.

### Race — configured Fan-in policy

Let multiple eligible inputs compete to satisfy the same continuation condition. The first input whose evidence satisfies the acceptance condition produces the qualifying signal that advances the state.

```text
A ─┐
B ─┼→ First qualifying signal → Next
C ─┘
```

Race can be viewed as a common Fan-in policy, but it has important operational semantics of its own: after a winner emerges, the other branches must be cancelled, ignored, retained, or moved to background work.

If the fate of losing branches is undefined, the Race has not actually finished.

### Quorum — configured Fan-in policy

Allow the successor path to begin when a sufficient number or weight of eligible inputs satisfies a condition.

```text
5 actors → 3 confirmations → Continue
```

Quorum concerns the convergence threshold, not how opinions are produced. Majority voting, confidence weighting, and test-based adjudication can provide inputs to Quorum, but they are not Quorum itself.

### Simple Merge — derived non-synchronizing convergence

Several mutually exclusive incoming paths may share one successor without waiting for one another:

```text
Branch
  ├→ Path A ─┐
  └→ Path B ─┴→ Next
```

This is Simple Merge, not Fan-in. It uses no Join State. If several incoming paths may all be active and each arrival should activate Next, that repeated-activation behavior must be stated explicitly.

### Route — base Responsibility Gate

Select an Actor as the candidate for a later responsibility-bearing transition.

```text
Work → Route → Actor B → Call / Delegate / Transfer
```

Branch chooses candidate continuations. Route chooses an Actor. They often occur together, but their semantics differ.

Route does not define Actor discovery, guarantee delivery, or change ownership. Those concerns belong to later Gates, the communication protocol, and the runtime.

### Call — base Responsibility Gate

One Actor opens a bounded request/response relationship with another Actor while retaining its continuation.

```text
Actor A ── Call ──→ Actor B
```

Core semantics:

```text
Call opens Boundary State; one declared outcome resolves it
```

Call creates Boundary State around the called operation. The callee may execute an arbitrarily complex process. A normal Actor-produced outcome uses a separate Return transition; another declared outcome may resolve the same Boundary through a different path. Call does not reassign ownership of a pre-existing work scope.

### Delegate — base Responsibility Gate

One Actor assigns execution ownership of an explicitly bounded work scope to another Actor.

```text
Actor A ──delegate work──→ Actor B
```

Call and Delegate describe different dimensions rather than synchronous and asynchronous variants. Call creates a request/response boundary. Delegate changes the executor of a named work scope. A system may Call without delegating a pre-existing scope, Delegate without waiting for a response, or compose Delegate with Call/Return when the delegator expects an outcome.

Whether the Worker may redelegate, whether the delegator retains final accountability, and how permissions and budgets change are not decided by L0. Any required acceptance precedes the committed execution-ownership change.

### Transfer — base Responsibility Gate

The current Actor commits continuation ownership for a piece of work to another Actor.

```text
Actor A ──transfer continuation──→ Actor B
```

Transfer is not an ordinary call:

```text
Call + Return: A → B → A
Transfer:      A → B → ...
```

After Transfer, Actor B owns continuation for the current path; Actor A is no longer a required node for further progress.

Transfer is not a proposal, delivery, or acceptance event. What is usually called a Handoff uses an L1 protocol to carry context and obtain any required acceptance; only the declared commit rule produces the L0 Transfer transition.

### Return — base Responsibility Gate

An Actor resolves a Call Boundary State, or an outcome boundary explicitly composed with Delegate, using a completion signal and, when applicable, a work result.

```text
Actor B → Return(completed | partial | failed | rejected | blocked | cancelled)
        → Actor A / Parent continuation
```

Return need not contain a successful answer. It may report:

- completed;
- partially completed;
- failed;
- rejected;
- blocked;
- cancelled;

“The Worker stopped producing output” is not Return. For Call, Return resolves the caller's declared Boundary. For Delegate, Return is present only when the Circuit also declares an outcome boundary. Timeout or an observed Stop status declared to be boundary-resolving may resolve that Boundary through a different path; a Cancel request by itself does not.

An outcome such as `blocked` resolves the Boundary only when the Circuit declares it terminal for that relationship; otherwise the Boundary remains pending.

### Wait — base Lifecycle Gate

Move the current path into a Wait State that preserves its continuation and names its possible resolution outcomes.

```text
Current path → Wait for condition
```

Waiting is not failure. Waiting for a Human, external Service, another Actor, or a point in time is normal in Multi-Actor collaboration.

Wait must expose observable outcomes; otherwise it is only an undiagnosable stall. Resume, Timeout, and an observed cancellation outcome are alternatives: after one resolves the Wait, a later conflicting signal cannot advance the same continuation again.

### Resume — base Lifecycle Gate

Restore a waiting path from its declared Wait State.

```text
Wait State → Resume(trigger) → Active path
```

Resume must resolve a specific Wait State. Runtime recovery may lead to Resume only when a valid Wait and continuation were preserved; Resume does not invent missing state or assume that the original process, model session, or machine remains alive.

### Stop — base Lifecycle Gate

End the current collaboration scope and produce an explicit terminal state.

```text
Active path → Stop(status)
```

Common terminal states include:

- succeeded;
- failed;
- cancelled;
- rejected;

Stop is not “no Actor is speaking.” Silence cannot distinguish success, failure, abandonment, and disconnection. Termination must be an observable collaboration fact.

Stop affects only its declared scope. Whether related branches, delegated work, or pending boundaries are cancelled, detached, or allowed to continue belongs to the surrounding Circuit or L2 system.

### Timeout — derived resolution

When a Wait or another pending relationship exceeds a deadline, produce an alternative timeout outcome.

```text
Wait for approval
  ├─ Approval arrives → Resume → Deploy
  └─ Deadline passes  → Timeout → Escalate
```

Timeout resolves the waiting or controlling relationship but does not inherently mean failure or stop the underlying Actor work. The surrounding Circuit decides whether that work is cancelled, retained, or allowed to produce a late result.

### Cancel — derived termination request

Cancel requests that a declared target path or scope terminate outside its normal success path:

```text
Race winner → Cancel(non-winning branches) → cancellation requested
Non-winning branch → Stop(cancelled)
```

The cancellation request and committed Stop are different facts. Cancel defines the target and propagation boundary; L1 and the runtime define delivery, interruption, and cleanup. If another continuation must wait for termination, the Circuit must say so instead of treating the request itself as completion.

### Retry — derived circuit

Attempt an operation again under declared conditions and within a declared attempt limit.

```text
Attempt → Failure → Retry → Attempt
```

Retry must say at least:

- which operation is retried;
- which outcomes are retryable;
- the maximum attempts;
- whether attempts wait or back off;
- how effects from earlier attempts are handled.

Formally, Retry can be composed from Branch, Wait, and Loop. Operationally, it remains a stable collaboration concept that needs independent observation and constraints.

### Loop — derived circuit form

Repeat a collaboration path until a termination condition holds.

```text
Execute → Evaluate → Exit condition met?
                    ├→ Yes → Exit Loop → Next
                    ├→ No, rounds remain → Loop → Execute
                    └→ No, round limit reached → Stop(not_satisfied)
```

Loop must have an externally visible termination condition and a bound on rounds, time, or cost. A model deciding that “it can still improve” is not sufficient justification for an infinite loop.

Retry is a local Loop around a failure condition. Evaluator–Optimizer may use a Loop around a quality condition.

---

## 4. Paths, Results, and State Elements

A circuit is not fully described by drawing arrows between gates. It must keep four projections of collaboration state distinguishable:

- **Flow** — which paths exist and which continuation is enabled;
- **Result Flow** — which work result is consumed directly, retained for later use, used jointly downstream, or given a terminal disposition;
- **Responsibility** — which Actor owns execution and which Actor owns continuation;
- **Lifecycle** — whether the work is active, waiting, or terminal.

Control Flow and Result Flow are related, but they are not the same. A branch may finish while its work result remains available for a later consumer. Several completion signals may arrive before a shared continuation becomes enabled. A work result may also be handed off immediately without any convergence.

Facts that later transitions must observe are **State Elements**:

| State Element | What it remembers |
| --- | --- |
| Continuation State | Where a path may continue after the current relationship resolves |
| Ownership State | Which Actor owns execution or continuation |
| Boundary State | Which request/response or declared outcome relationship remains unresolved |
| Result State | Which work result remains available and what its declared disposition is |
| Join State | Which inputs belong to one convergence, which are satisfied, and whether its continuation has fired |
| Wait State | Which continuation is waiting and which outcomes may resolve it |
| Round State | Which Loop, review, or Retry round is being discussed |
| Deadline State | Which temporal boundary may produce a Timeout outcome |

Boundary State, Wait State, and Join State describe unfinished collaboration relationships. They may have several possible outcomes, but conflicting outcomes must not advance the same logical continuation twice. Join State may refer to Result States needed downstream, but it does not merge or consume their business content. L1 supplies the concrete correlation and commit mechanism.

L0 defines which facts must remain distinguishable and which later transition may consume them. It does not choose whether they live in a call stack, memory, a database, a filesystem, or a message system.

## 5. Quick Reference by Structural Effect

The following table summarizes each element by its effect on paths, boundaries, ownership, or state:

| Shape | Gate | Effect |
| --- | --- | --- |
| `1 → 1` | Sequence | Continue one path |
| `1 → selected set` | Branch | Select candidate continuations |
| `1 work → actor candidate` | Route | Select an Actor without changing ownership |
| `1 → N` | Fan-out | Expand multiple branches |
| `N signals → 1` | Fan-in | Observe convergence readiness and enable one continuation |
| `N alternatives → 1` | Simple Merge | Share a successor without waiting |
| `N → first 1` | Race | Continue from the first qualifying signal |
| `N → threshold → 1` | Quorum | Continue after reaching a threshold |
| `A → B → A` | Call / Return | Cross an Actor boundary and return |
| `A → B` | Delegate | Change execution ownership |
| `A → B → ...` | Transfer | Change the continuation owner |
| `1 → waiting → 1` | Wait / Resume | Wait and restore a path |
| `1 ↺` | Retry / Loop | Repeat a path |
| `source → target request` | Cancel | Request another scope to terminate |
| `1 → 0` | Stop | Produce a terminal state |

This table describes logical shape, not implementation topology. The same gate may cross processes, machines, or organizations, or remain entirely inside one process.

---

## 6. The Most Important Boundaries Between Gates

| Do not conflate | Distinction |
| --- | --- |
| Branch / Fan-out | Branch selects candidate continuations; Fan-out creates independently advancing branches. |
| Fan-out / Broadcast | Fan-out creates work paths; Broadcast changes information visibility. |
| Fan-out / Fan-in | Fan-out creates branches; Fan-in observes convergence. Neither requires the other. |
| Fan-in / Reduce | Fan-in controls when to continue; Reduce processes work-result content. |
| Simple Merge / Fan-in | Simple Merge shares a successor without waiting; Fan-in waits for a convergence condition. |
| Flow / Result Flow | Flow identifies enabled paths; Result Flow identifies the fate of work results. |
| Branch / Route | Branch selects continuations; Route selects an Actor. |
| Route / Transfer | Route selects a candidate; Transfer changes continuation ownership. |
| Call / Delegate | Call creates a request/response boundary; Delegate assigns execution ownership of an explicit work scope. They may be composed. |
| Delegate / Transfer | Delegate changes the executor of a scope; Transfer changes the continuation owner of a path. |
| Wait / Stop | Wait preserves a continuation; Stop creates a terminal outcome. |
| Cancel / Stop | Cancel requests termination; Stop is committed termination of the declared scope. |
| Race / Voting | Race selects the first qualifying input; Voting is a result-processing mechanism. |

---

## 7. What L0 Does Not Define

Logic gates define how collaboration state changes, including the structural fate of work results. They do not define the business schema of those results or how transitions happen reliably in a real system.

For example, `Transfer(A, B)` says that continuation ownership has committed from A to B. It does not answer:

- how A and B are uniquely identified;
- which message expresses Transfer;
- what happens when B is offline;
- which acceptance rule, if any, authorizes the commit;
- how context and artifacts travel with Transfer;
- how state is persisted and recovered;
- whether duplicate delivery causes duplicate execution.

These concerns belong to L1: the structured Actor Circuit Protocol.

Likewise, gates do not prescribe which gates should be composed. Orchestrator–Worker, Group Chat, Debate, Evaluator–Optimizer, and other complete execution structures belong to Multi-Actor systems built above gates and communication protocols.

This produces a clear layering:

```text
L0  Collaboration Logic Circuit
    Define Flow, Result Flow, Responsibility, and Lifecycle changes

L1  Actor Circuit Protocol
    Represent Gate instances, process edges, state retention, communication timing,
    acknowledgement, and correlation as structured data

L2  Multi-Actor Systems
    Compose gates into complete collaboration systems
```

This article stops at L0.

It does not attempt to decide which Multi-Actor system is best or force all collaboration into one workflow. It establishes a more basic vocabulary so that later we can say precisely:

- this is Fan-out, not merely “starting several Agents”;
- these completion signals synchronize through Fan-in, while the successor consumes the retained work results;
- this is Call, not Transfer;
- this is still Wait, not Stop;
- execution ownership changed, but continuation ownership did not.

Only after these gates are clear can we discuss communication protocols, Multi-Actor systems, and collaboration design patterns above the gate level.
