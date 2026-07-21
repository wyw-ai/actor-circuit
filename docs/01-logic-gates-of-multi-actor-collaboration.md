# Logic Gates of Multi-Actor Collaboration

> A foundational vocabulary for describing how Humans, Agents, Services, and Scripts advance work together

Over the past two years, “Multi-Agent Patterns” has rapidly become an all-inclusive catalog: Sequential, Concurrent, Routing, Handoff, Group Chat, Orchestrator–Worker, Evaluator–Optimizer, Debate, and more.

These names describe different forms of collaboration, but they often mix concepts from different levels.

Sequential and Fan-out/Fan-in resemble `;`, `fork`, and `join` in programming languages. Orchestrator–Worker and Group Chat are complete execution structures composed from several basic operations. Calling all of them design patterns obscures a more fundamental question:

> When several independent participants advance one piece of work together, what are the most basic reusable collaboration operations?

This article calls those operations **logic gates of Multi-Actor collaboration**.

“Logic gate” is an engineering analogy rather than a strict mathematical mapping to digital circuits. Each gate has stable, business-independent input/output semantics, and gates can be composed into more complex Multi-Agent systems.

This article discusses only two subjects:

1. what an Actor is;
2. which collaboration logic gates belong to L0.

Communication protocols, framework implementations, concrete Multi-Agent systems, and design patterns above the gate level are outside its scope.

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

Let the collaboration state at any moment be `C(t)`.

This article does not prescribe where that state lives or which protocol Actors use to exchange information. It may exist in memory, a database, a filesystem, a messaging system, or human understanding.

A collaboration logic gate is a class of stable state transition:

```text
g: C(t) → C(t+1)
```

It receives the current work state and one or more inputs, then produces a new state and possible continuations.

For example:

- Fan-out expands one work path into several branches;
- Fan-in converges several branches into one path;
- Branch selects one of several possible continuations;
- Transfer moves continuation ownership from one Actor to another;
- Wait suspends the current path until an external condition becomes true.

### Deciding whether an operation is an L0 gate

This article uses four criteria.

#### 1. Business-independent

Its semantics remain intact when domain terms such as coding, research, support, and approval are replaced.

#### 2. Semantically stable

It clearly changes something: the number of paths, the selected successor, a waiting condition, or the Actor that owns continuation.

#### 3. Composable

It can participate in a larger collaboration structure rather than describing only a complete product or fixed team.

#### 4. Observable at the boundary

The system can distinguish when the transition starts, when it completes, and which successor states it produces.

“Impossible to decompose further” is not a criterion here.

Fan-out can be implemented as repeated dispatch. Loop can be expanded into Branch plus a back edge. Retry can be explained as another attempt on a failure branch. If an operation has stable, independent, recurring meaning in collaboration systems, it can still be useful as a named gate.

The goal is a practical collaboration vocabulary, not a theoretically minimal machine instruction set.

---

## 3. L0: Collaboration Logic Gates

The following is a candidate set of L0 gates. It is not a closed standard, but a foundational vocabulary for describing and comparing Multi-Actor systems.

### 1. Sequence

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

### 2. Branch

Select one or more successor paths according to the current state.

```text
             ┌→ Path A
Input → Test ├→ Path B
             └→ Path C
```

Branch is defined by how a decision changes successor paths, not by who makes that decision.

```text
condition(C) → selected continuation
```

The condition may come from a deterministic rule, a Human decision, an Agent judgment, or an external event. The decision mechanism belongs to a higher level; Branch expresses only the selection.

### 3. Route

Select an Actor to continue the work according to the objective, capability, content, or current state.

```text
Work → Route → Actor B
```

Branch chooses which path to take. Route chooses which Actor continues. They often occur together, but their semantics differ.

Route does not define Actor discovery and does not guarantee delivery. Those concerns belong to the communication protocol and runtime.

### 4. Fan-out

Expand one input path into multiple work branches that can advance independently.

```text
          ┌→ Branch A
Input ────├→ Branch B
          └→ Branch C
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

### 5. Fan-in

Converge the states of multiple work branches into one successor path.

```text
Branch A ─┐
Branch B ─┼→ Fan-in → Next
Branch C ─┘
```

Core semantics:

```text
N branch states → 1 continuation
```

Fan-in must declare a convergence condition:

- **All**: all designated branches complete;
- **First**: the first acceptable result arrives;
- **Quorum**: a specified count or weight is reached;
- **Best available**: use the best result available at a deadline or budget boundary.

Fan-in decides when and from which branches the circuit continues. It does not automatically decide how content is merged. Concatenation, voting, selection, synthesis, and Reduce are result-processing strategies and should not be hidden inside Fan-in semantics.

### 6. Race

Let multiple branches compete to satisfy the same continuation condition. The first branch to produce an acceptable result advances the state.

```text
A ─┐
B ─┼→ First acceptable result → Next
C ─┘
```

Race can be viewed as a common Fan-in policy, but it has important operational semantics of its own: after a winner emerges, the other branches must be cancelled, ignored, retained, or moved to background work.

If the fate of losing branches is undefined, the Race has not actually finished.

### 7. Quorum

Allow the successor path to begin when a sufficient number or weight of branches satisfy a condition.

```text
5 actors → 3 confirmations → Continue
```

Quorum concerns the convergence threshold, not how opinions are produced. Majority voting, confidence weighting, and test-based adjudication can provide inputs to Quorum, but they are not Quorum itself.

### 8. Call

One Actor asks another Actor to perform work while retaining continuation ownership.

```text
Actor A → Call Actor B → Result → Actor A
```

Core semantics:

```text
control returns to caller
```

The callee may execute an arbitrarily complex process, but from the current path it remains behind a boundary that is expected to return.

### 9. Delegate

One Actor assigns bounded work to another Actor, making the latter the executor of that work.

```text
Actor A ──delegate work──→ Actor B
```

The difference between Delegate and Call is not merely asynchronous versus synchronous. Call emphasizes nested execution and return. Delegate emphasizes a work commitment that may advance independently.

Whether the Worker may redelegate, whether the delegator retains final accountability, and how permissions and budgets change are not decided by L0. L0 expresses only that execution ownership changes explicitly.

### 10. Transfer

The current Actor transfers continuation ownership for a piece of work to another Actor.

```text
Actor A ──transfer continuation──→ Actor B
```

Transfer is not an ordinary call:

```text
Call:      A → B → A
Transfer:  A → B → ...
```

After Transfer, Actor B owns continuation for the current path; Actor A is no longer a required node for further progress.

What is usually called a Handoff contains at least one Transfer. How context is packaged, how responsibility is recorded, and how the receiver acknowledges acceptance belong to the L1 communication protocol rather than to the gate itself.

### 11. Return

An Actor completes a Call or Delegate boundary and returns a result state to a pre-existing continuation point.

```text
Actor B → Return(result) → Actor A / Parent continuation
```

Return need not contain a successful answer. It may report:

- completed;
- partially completed;
- failed;
- rejected;
- blocked.

“The Worker stopped producing output” is not Return. Return must resolve an outstanding Call or Delegate boundary and give the waiting path a distinguishable state.

### 12. Wait

Suspend the current path until a specified condition or event appears.

```text
Current path → Wait(condition) … → Resume
```

Waiting is not failure. Waiting for a Human, external Service, another Actor, or a point in time is normal in Multi-Actor collaboration.

Wait must expose an observable condition; otherwise it is only an undiagnosable stall.

### 13. Timeout

When a Wait or another operation exceeds a deadline, produce a timeout result that successor paths can consume.

```text
Wait ──deadline exceeded──→ Timeout → Branch
```

Timeout does not inherently mean failure. It is a fact that may trigger cancellation, degradation, Retry, rerouting, or Human intervention.

### 14. Retry

Attempt an operation again under declared conditions and within a declared budget.

```text
Attempt → Failure → Retry → Attempt
```

Retry must say at least:

- which operation is retried;
- which outcomes are retryable;
- the maximum attempts or budget;
- whether attempts wait or back off;
- how effects from earlier attempts are handled.

Formally, Retry can be composed from Branch, Wait, and Loop. Operationally, it remains a stable collaboration concept that needs independent observation and constraints.

### 15. Loop

Repeat a collaboration path until a termination condition holds.

```text
Execute → Evaluate → Continue?
   ↑                       │
   └───────────────────────┘
```

Loop must have an externally visible termination condition and a bound on rounds, time, or cost. A model deciding that “it can still improve” is not sufficient justification for an infinite loop.

Retry is a local Loop around a failure condition. Evaluator–Optimizer may use a Loop around a quality condition.

### 16. Resume

Restore waiting, suspended, or interrupted work from existing collaboration state.

```text
Suspended state → Resume(trigger) → Active path
```

Resume must not assume that the original process, model session, or machine remains alive. It restores the collaboration path, not one concrete runtime instance.

### 17. Stop

End the current collaboration scope and produce an explicit terminal state.

```text
Active path → Stop(status)
```

Common terminal states include:

- succeeded;
- failed;
- cancelled;
- rejected;
- exhausted.

Stop is not “no Actor is speaking.” Silence cannot distinguish success, failure, abandonment, and disconnection. Termination must be an observable collaboration fact.

---

## 4. Understanding Gates by Shape

If names are temporarily ignored, these gates can be understood through their path shapes:

| Shape | Gate | Effect |
| --- | --- | --- |
| `1 → 1` | Sequence | Continue one path |
| `1 → 1 of N` | Branch | Select a successor path |
| `1 work → 1 actor` | Route | Select a continuation Actor |
| `1 → N` | Fan-out | Expand multiple branches |
| `N → 1` | Fan-in | Converge branch state |
| `N → first 1` | Race | Continue from the first acceptable result |
| `N → threshold → 1` | Quorum | Continue after reaching a threshold |
| `A → B → A` | Call / Return | Cross an Actor boundary and return |
| `A → B` | Delegate | Change execution ownership |
| `A → B → ...` | Transfer | Change the continuation owner |
| `1 → suspended → 1` | Wait / Resume | Suspend and restore a path |
| `1 ↺` | Retry / Loop | Repeat a path |
| `1 → 0` | Stop | Produce a terminal state |

This table describes logical shape, not implementation topology. The same gate may cross processes, machines, or organizations, or remain entirely inside one process.

---

## 5. The Most Important Boundaries Between Gates

### Fan-out is not Broadcast

Fan-out creates work branches. Broadcast expands the visibility of the same information.

Every receiver seeing a message does not mean that every receiver owns a piece of work. Conversely, one job may be divided into several branches without broadcasting the full input to every Actor.

### Fan-in is not Reduce

Fan-in decides when branches converge. Reduce decides how several results are computed into one.

A system may Fan-in after all branches complete and then choose one result. It may also Reduce incrementally as branches arrive. The two should not be coupled.

### Route is not Transfer

Route selects a target Actor. Transfer changes continuation ownership.

A system may Route information to a Specialist while the original Actor still controls the subsequent process. The continuation owner changes only when Transfer occurs.

### Call is not Delegate

Call creates an execution boundary expected to return. Delegate creates a work commitment advanced by another Actor.

An Actor may call another Actor for information without delegating any work. It may also delegate a long-running task and continue processing something else.

### Wait is not Stop

Wait preserves a future continuation condition. Stop creates a terminal state.

Treating Wait as Stop loses unfinished work. Treating Stop as Wait produces suspended work that can never be reclaimed.

---

## 6. What L0 Does Not Define

Logic gates define how collaboration state changes. They do not define how those transitions happen reliably in a real system.

For example, `Transfer(A, B)` says only that continuation moves from A to B. It does not answer:

- how A and B are uniquely identified;
- which message expresses Transfer;
- what happens when B is offline;
- whether B must acknowledge acceptance;
- how context and artifacts travel with Transfer;
- how state is persisted and recovered;
- whether duplicate delivery causes duplicate execution.

These concerns belong to L1: the Multi-Actor communication protocol.

Likewise, gates do not prescribe which gates should be composed. Orchestrator–Worker, Group Chat, Debate, Evaluator–Optimizer, and other complete execution structures belong to Multi-Agent systems built above gates and communication protocols.

This produces a clear layering:

```text
L0  Collaboration Logic Gates
    Define how collaboration state changes

L1  Multi-Actor Communication Protocol
    Express, transport, acknowledge, and recover transitions across Actors

L2  Multi-Agent Systems
    Compose gates into complete collaboration systems
```

This article stops at L0.

It does not attempt to decide which Multi-Agent system is best or force all collaboration into one workflow. It establishes a more basic vocabulary so that later we can say precisely:

- this is Fan-out, not merely “starting several Agents”;
- this requires Fan-in, not merely “summarize at the end”;
- this is Call, not Transfer;
- this is still Wait, not Stop;
- execution ownership changed, but continuation ownership did not.

Only after these gates are clear can we discuss communication protocols, Multi-Agent systems, and collaboration design patterns above the gate level.
