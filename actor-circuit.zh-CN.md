# Actor Circuit 中文审阅稿

> 临时文件。用于中文阅读和概念审阅，发布前可整份删除。它位于 Skill 目录外，不参与 Skill 的渐进式加载。

英文文件保持当前 Skill 的运行入口和规范来源：

- [`skills/actor-circuit/SKILL.md`](skills/actor-circuit/SKILL.md)
- [`skills/actor-circuit/references/actor.md`](skills/actor-circuit/references/actor.md)
- [`skills/actor-circuit/references/layers.md`](skills/actor-circuit/references/layers.md)
- [`skills/actor-circuit/references/l0.md`](skills/actor-circuit/references/l0.md)

本文把这些内容合并成一份中文版本，便于连续阅读。

阅读建议：

- 只想先理解整套分层，直接看“二、Actor Foundation 与 L0/L1/L2”；
- 想判断 Service、Workflow 或 Team 能否算 Actor，看“三、Actor Foundation”；
- 想设计或审阅具体协作电路，看“四、L0：Collaboration Logic Circuit”；
- “一、Skill 如何使用”主要解释 Agent 使用这套 Skill 时应遵守的边界，可以最后再看。

## 一、Skill 如何使用

Actor Circuit 提供一套以 Actor 为中心的协作词汇，用来理解、解释、设计和审阅 Human、AI Agent、Service、Script、Team 与 Workflow instance 之间的协作。

适用问题包括：

- Multi-Agent 架构与编排；
- Route、Call、Delegate 与 Handoff；
- Fan-out、Fan-in、Wait、Retry 和 Stop；
- 责任怎样流动；
- 一个结构属于基础 Gate、组合 Circuit，还是完整 System。

使用这套模型时，应保留用户原本的问题，选择最容易呈现关键关系的表达方式。输出可以是文字、表格、结构图、设计评审或其他结构化产物，无需套用固定模板。

根据问题读取对应参考：

- 判断谁是 Actor、边界画在哪里：读取 Actor Foundation；
- 判断 L0、L1、L2 的边界：读取分层模型；
- 分析状态转换、State Element、Gate、Circuit、等待、汇合、循环和终止：读取 L0。

当前版本完整定义 Actor Foundation 与 L0。L1 和 L2 只标出范围，后续再展开。使用者可以指出对 L1/L2 的依赖，同时应避免替项目补写一套尚未形成的协议或系统分类。

需要保持清楚的几个判断：

- Actor Foundation 是所有层共同使用的基础，没有编号；
- State Element 保存后续转换仍需观察的协作事实，在电路类比中接近寄存器或锁存器；
- Gate 改变协作状态；
- Circuit 连接 Gate、State Element 和可能的接续路径；
- 一张拓扑图还需要补全接续权、责任、等待、汇合与终止语义；
- Circuit 和编排结构不会因为有了名称就自动成为设计模式；
- 基础 Gate 早已存在于工作流和分布式系统研究中。本项目的工作是以 Actor 为基础重新抽象、组织和分层。

---

## 二、Actor Foundation 与 L0/L1/L2

这是一套用于组织 Multi-Actor 工程问题的工作模型，目前还没有成为行业标准。

### Actor Foundation：谁参与协作

Actor 是一个可以被独立识别的协作边界。它拥有状态、能力和生命周期，可以参与协作状态转换。

Human、AI Agent、Service、Script、Team、Workflow instance 和 External System 都可能成为 Actor。

Actor Foundation 由所有层共同使用：

```text
Actor Foundation  定义谁能参与
L0                定义协作状态怎样变化
L1                定义变化怎样跨 Actor 表达和确认
L2                把 Actor、电路和协议组成完整系统
```

### L0：Collaboration Logic Circuit

L0 回答：**协作状态怎样变化？**

三个核心概念：

- **Collaboration State**：协作逻辑能够观察到的当前状态；
- **Gate**：一类稳定的状态转换；
- **Circuit**：由 Gate、State Element 和接续关系形成的结构。

Collaboration State 可以拆成具名的 State Element，例如接续点、责任人、活动分支、等待条件、部分完成结果和当前轮次。

L0 观察三类变化：

- 路径怎样展开、选择和汇合；
- 当前工作由谁执行、由谁接着推进；
- 协作处于活动、等待、可恢复还是终止状态。

L0 定义 Sequence、Branch、Fan-out、Fan-in、Route、Call、Delegate、Transfer、Wait、Resume、Stop 等语义，不指定消息格式、传输方式和存储实现。

### L1：Multi-Actor Communication Protocol

L1 回答：**L0 的状态转换怎样跨 Actor 被表达，并获得可靠的交互语义？**

预计包含：

- 身份与寻址；
- Message 和 Artifact envelope；
- 投递与确认；
- Task、Branch、Round、Correlation 和 Causation identity；
- Context 与 Result 的携带；
- 责任转移的接受与拒绝；
- 离线投递与恢复信号；
- 取消、重复、幂等和迟到结果；
- 权限与可观察的生命周期事件。

L0 可以暴露对 L1 的要求：

```text
Fan-out + Fan-in  需要 Branch correlation
Transfer          需要可观察的接收确认
Wait              需要可识别的 Resume signal
```

L1 让协作在 Actor 边界之间可观察、可关联、可确认。持久化后端和端到端耐久性仍由执行环境共同承担。

### L2：Multi-Agent Systems

L2 回答：**Actor、L0 电路和 L1 通信怎样组成完整系统？**

Orchestrator–Worker、Group Chat、Recursive Delegation 和 Evaluator–Optimizer 属于这一层。

一个完整的 L2 描述需要包含：

- Actor 边界；
- State ownership；
- 使用了哪些 L0 Circuit；
- 对 L1 的协议假设；
- 汇合条件；
- 失败行为；
- 终止条件。

名称只能提供入口，无法替代这些语义。

### Durable Execution Plane

持久执行回答：**哪些协作事实需要跨越时间和故障继续存在，未完成的执行怎样恢复？**

它贯穿编号层：

- L0 指出 Circuit 依赖哪些 State Element；
- L1 为跨 Actor 的 Event、Task、Branch 和 Transfer 提供稳定身份；
- Runtime 保存 History、Checkpoint、Timer、Lease 或等价的恢复信息；
- L2 选择并对外说明端到端可靠性保证。

短暂的进程内电路可以把状态放在内存中。需要跨进程重启或基础设施故障继续运行的电路，应明确持久边界和恢复行为。Database、Log、Queue 与 Object Store 属于 Runtime 选择。

### Pattern 跨越多层

协作设计模式要解决反复出现的协作矛盾，安排角色、知识、权力、承诺、反馈和退出方式。完整描述通常包含 Context、Forces、Structure、Protocol 和 Consequences。

Fan-out/Fan-in 是 L0 Circuit，Orchestrator–Worker 是 L2 Composition。真正的协作 Pattern 往往跨越 Actor Foundation、L0、L1 和 L2。

---

## 三、Actor Foundation

### 定义

Actor 是一个可以被独立识别的协作边界。它拥有状态、能力与生命周期，并能参与协作状态转换。

```text
Actor = Identity Boundary + Actor-local State + Capability + Lifecycle
```

- **Identity Boundary**：在一项协作中区分参与者；具体 ID、地址和寻址方式由 L1 定义。
- **Actor-local State**：Actor 可以在自身边界内保存或改变的状态。
- **Capability**：Actor 可以贡献什么，或承诺完成什么。
- **Lifecycle**：在需要时能够区分可用、活动、等待、完成和消失。

这个定义不要求 Actor 具有人类意义上的智能或自主性，也不要求使用大模型。

### 操作性判断

在选定的抽象层次上，能够观察到下面这些边界现象时，可以把参与者建模为 Actor：

- 输入跨入边界；
- 工作或状态在边界内部推进；
- Outcome、Refusal、Failure 或 Lifecycle change 跨出边界；
- 在当前协作范围内能够把它和其他参与者区分开。

同一项技术可以处于 Actor 边界内，也可以独立成为 Actor：

```text
Agent → internal library call
        Library 留在 Agent 边界内

Agent → request → remote Service → observable outcome
                  Service 可以作为 Actor
```

### 静态定义与活动 Actor

类型、源码、配置和角色描述本身没有形成一个活动 Actor。具体实例进入协作后才产生可观察边界。

| 静态定义 | 活动协作边界 |
| --- | --- |
| Service code 与 API | 接收输入并产生结果的 Service instance |
| Workflow definition | 可以 Wait、Resume 和 Stop 的 Workflow instance |
| Script file | 带有具体输入和结果的 Script execution |
| Agent prompt | 拥有具体身份和生命周期的运行中 Agent |
| Team structure | 通过统一外部边界行动的 Team |

### 边界属性

#### 异构参与者

Human、AI Agent、Service、Script、Team、Workflow instance 与 External System 可以共享 Actor 抽象。它们的能力、智能、权力和可用性可以完全不同。

这里的统一发生在语义层：协作模型无需为每一种参与者重新发明控制语言。

#### 边界内自治

Actor 可以在边界内推进状态，无需向调用者暴露所有内部步骤。策略、权限、预算和监督仍然可以约束它。

#### 可观察结果

Actor 的结果包括完成、部分完成、失败、拒绝、阻塞、等待和取消。

#### 稳定身份与可变实现

Actor 可以更换模型、进程、机器或内部成员，同时保持协作身份。相同实现的两次执行也可能是两个不同 Actor。

### 递归边界

Actor 边界可以递归展开，并随观察视角变化：

```text
外部视角：Research Team = 一个 Actor

内部视角：Research Team
          ├─ Planner
          ├─ Search Actors
          └─ Reviewer
```

Workflow instance 对调用者可以表现为一个 Actor，内部则包含 L0 Circuit 和多个 Actor。

递归抽象不会消除内部责任。它让上层 Circuit 能够把一个组合边界当作一个参与者。

### Actor 的边界

- AI Actor 可以更换模型，身份保持连续。
- 进程可以退出，Actor 仍可能保持可寻址或可恢复。
- Planner 和 Reviewer 表示协作职责，同一个 Actor 可以承担不同职责。
- Team 和 System 也能形成有效 Actor 边界。
- Actor 内部的 Library call 属于实现细节；拥有独立身份并参与协作的 Service 可以成为 Actor。
- Agent 是 Actor 的一种实现。

---

## 四、L0：Collaboration Logic Circuit

### 范围与来源

L0 定义作用于 Actor 的、业务无关且语义稳定的协作状态转换。Actor 由 Actor Foundation 提供。

Sequence、Branch、Parallel Split、Synchronization、Partial Join、Race、Cancellation、Loop 和 Termination 在 Workflow 与 Distributed Systems 研究中已有长期积累。跨参与者的差异也来自 Service Interaction Patterns 和现代 Agent framework。

本项目将这些已有结构放到共同的 Actor Foundation 上，结合 Multi-Actor 工程实践重新组织，并将 L0、L1 和 L2 分开。

主要来源：

- [Workflow Patterns](https://research.tue.nl/en/publications/workflow-patterns-2/)，van der Aalst 等，2003。
- [Workflow Control-Flow Patterns](https://www.workflowpatterns.com/patterns/control/)，Workflow Patterns Initiative。
- [Service Interaction Patterns](https://kodu.ut.ee/~dumas/ServiceInteractionPatterns/documentation.html)，Barros、Dumas、ter Hofstede，2005。

### 形式化核心

L0 有三个核心概念。

#### Collaboration State

用 `C(t)` 表示时间 `t` 时协作逻辑能够观察到的状态。

L0 指出哪些协作事实需要保持可区分。状态可以存在于内存、数据库、文件系统、消息系统、Artifact graph 或 Human understanding 中。Durability contract 决定哪些事实要跨越时间和故障继续存在。

#### Gate

Gate 表示由可观察 Event、Fact 或 Outcome 触发的一类协作状态转换：

```text
g: (C(t), e(t)) → C(t+1)
```

Gate 具有可观察的启用条件，并产生可观察的后继状态或终态。当触发事实已经包含在 `C(t)` 中，可以省略 `e(t)`。

#### Circuit

Circuit 连接 Gate、State Element 和可能的接续路径：

```text
W = (G, R, E)
```

- `G`：Gate instance 集合；
- `R`：被 Gate 读取或改变的 State Element 集合；
- `E`：Gate outcome、状态依赖、后继 Gate 与终态之间的连接关系。

图形拓扑只能提供结构。Gate condition、Branch membership、Responsibility、Wait、Convergence、Repetition 与 Termination 还需要保留明确语义。

### State Element

State Element 是 `C(t)` 的具名投影。一个或多个后续状态转换仍然需要观察它。

| State Element | 记录什么 |
| --- | --- |
| **Continuation State** | 当前边界结束后从哪里继续 |
| **Ownership State** | 当前执行者或接续者是谁 |
| **Join State** | 期待哪些分支、哪些分支已经完成 |
| **Wait State** | 当前正在等待哪个条件或事件 |
| **Round State** | 当前 Loop、Review 或 Retry 处于第几轮 |
| **Deadline State** | 什么时候可以观察到 Timeout 条件 |

Gate 会读取或改变 State Element。在时序电路类比中，State Element 的作用接近寄存器或锁存器。它只描述电路要记住什么，不选择存储技术和持久保证。

### 状态的三个观察维度

#### Flow

Flow 描述有哪些路径、哪些路径已经启用或正在活动、哪些后继路径可以继续。

Branch 是 Gate 创建或选择的 Flow state，无需和 Gate 并列成为核心概念。

#### Responsibility

Responsibility 描述 Actor 与路径之间的关系。至少区分：

- **Execution Owner**：执行当前有边界的工作；
- **Continuation Owner**：拥有推进下一次状态转换的权利和义务。

观察权限需要由 L1 和更高层策略定义，不能从这两种 Ownership 自动推出。

#### Lifecycle

Lifecycle 区分 Active、Waiting、Resumable 和 Terminal state。

没有活动无法代表一种 Lifecycle state。系统应通过显式状态区分成功、失败、等待和失联。

业务 Payload 不进入 L0 核心。Gate 可以观察做出转换所需的 Fact 或 Outcome，无需定义业务对象本身。

### Gate 的准入标准

一项操作满足下面条件时，可以作为 L0 Gate：

1. **Business-independent**：替换业务词汇后，语义仍然成立；
2. **Semantically stable**：明确改变 Flow、Responsibility、Lifecycle 或其中多个维度；
3. **Composable**：可以参与更大的 Circuit；
4. **Observable at the boundary**：启用条件和结果状态能够被区分。

L0 追求实用的工程词汇，无需把所有操作拆成数学意义上的最小指令。可分解的 Policy 和多 Gate 结构应标明其组合性质。

### 基础 Gate

这里的“基础”表示当前词汇中的一级命名转换，不代表数学上的不可约。

#### Flow Gate

##### Sequence

前一步达到要求的完成状态后，启用后继步骤。

```text
complete(A) enables B

理解需求 → 修改代码 → 运行测试
```

派发 A 只说明请求已经发出。Sequence 依赖 A 的完成事实，不规定由哪个 Actor 执行，也不规定信息怎样传输。

##### Branch

根据当前 Collaboration State 选择一个或多个后继路径。

```text
测试通过？
  ├─ 是 → 发布
  └─ 否 → 修改
```

判断事实可以来自 Rule、Human、Agent 或 External System。Branch 只定义它对后继路径的影响。

##### Fan-out

把一条路径展开为多条可以独立推进的分支。

```text
1 path → N branches

研究问题
  ├─ Agent A → 查证据
  ├─ Agent B → 找案例
  └─ Agent C → 找反例
```

分支可以拆分不同子问题、重复解决同一问题，也可以刻意采用不同模型、工具、角色或策略。Fan-out 允许独立推进，不承诺同时执行，也不代表 Broadcast。

##### Fan-in

多个分支满足声明的收敛条件后，启用一条共同后继路径。

```text
N branches → 1 continuation

证据 ─┐
案例 ─┼→ 所需分支完成 → Review
反例 ─┘
```

Fan-in 定义何时收敛、等待哪些分支。Aggregation、Reduce、Voting、Selection 和 Synthesis 负责处理分支内容。

完整语义可能包括：相关 Branch set、Convergence condition、Dynamic membership、Reset behavior、Late-result policy 和剩余分支的处理方式。

#### Responsibility Gate

##### Route

根据 Capability、Content、State、Policy 或 Availability 选择候选接收者。

```text
问题进入系统
  ├─ 账单问题 → Billing Actor
  ├─ 技术问题 → Support Actor
  └─ 高风险   → Human
```

Route 只选择 Actor。Discovery、Delivery、Acceptance、Execution ownership 与 Continuation transfer 仍需要其他机制。

##### Call

请求另一个 Actor 完成工作，同时保留调用者的 Continuation。

```text
A → B → A

Lead → Call Search Actor → Evidence → Lead continues
```

被调用者内部可以包含复杂 Circuit。外层协作期待结果回到调用者已有的接续点。

##### Delegate

把一项有边界的工作交给另一个 Actor，使其成为 Execution Owner，并建立预期交付边界。

```text
Lead ── Delegate "负责测试计划" ──→ QA Actor
```

Final accountability、Redelegation、Permission 与 Budget propagation 由更高层规则决定。

##### Transfer

把一条路径的 Continuation ownership 转给另一个 Actor。

```text
A → B → …

Frontline Support ── Transfer ──→ Specialist → Resolution
```

Transfer 完成后，原 Actor 不再是继续推进的必要节点。完整 Handoff 通常还需要 L1 的 Context carriage 与 Acceptance。系统承诺跨故障继续时，Durable execution environment 还要保存并恢复 Pending transfer state。

##### Return

解决一个已有的 Call 或 Delegate 边界，把可观察 Outcome 送回声明的 Continuation point。

```text
Worker → Return(completed | failed | blocked) → Lead
```

Outcome 可以是 Completed、Partially completed、Failed、Rejected 或 Blocked。停止输出无法完成 Return 语义。

#### Lifecycle Gate

##### Wait

把 Active path 转为 Waiting state，直到声明的 Condition 或 Event 可观察。

```text
提交变更 → Wait(Human approval) …
```

Wait 会保留未来 Continuation。

##### Resume

声明的 Trigger 出现后，把 Waiting、Suspended 或 Interrupted path 恢复为 Active state。

```text
Approval arrives → Resume → Deploy
```

Resume 恢复 Collaboration State，不要求原来的 Process、Machine、Model session 或 Actor instance 继续存活。

##### Stop

用明确终态结束一段协作范围。

```text
Review passed    → Stop(succeeded)
Budget exhausted → Stop(exhausted)
```

常见终态包括 Succeeded、Failed、Cancelled、Rejected 和 Exhausted。没有可见活动不足以判断终止。

### 配置型与派生操作

下面这些名称在 L0 中仍然有用，它们和基础 Gate 所处的结构层次不同。

#### Fan-in 的收敛策略

##### Race

第一个满足 Acceptance condition 的分支推动后继路径。Race 需要定义 Winner selection，并说明未胜出分支与近同时结果怎样处理。

```text
Candidate A ─┐
Candidate B ─┼→ 第一个通过测试的候选
Candidate C ─┘
```

##### Quorum

达到声明的数量或权重后继续。Quorum 需要定义 Threshold、Membership 和剩余分支语义。

```text
5 reviewers → 3 approvals → Continue
```

`All`、`First`、`Quorum`、`Best available at a boundary` 都可以配置 Fan-in 的收敛条件。它们不定义结果聚合算法。

#### Wait 的解决方式

##### Timeout

路径处于等待或执行状态时，超过声明的时间边界会产生可观察 Outcome。

```text
等待批准
  ├─ Approval arrives → Resume
  └─ 24 hours pass    → Timeout → Escalate
```

Timeout 产生一个供后续 Circuit 消费的事实，常见后继包括 Branch、Cancellation、Degradation、Retry、Route、Human intervention 和 Stop。

#### 重复结构

##### Loop

从声明的 Outcome 建立一条返回先前 Circuit region 的回边，直到终止条件成立。

```text
Draft → Review → Good enough?
  ↑                 │
  └──── No ─────────┘
```

Loop 是由 Gate 和 Continuation 组成的 Circuit form，需要可观察的 Condition，以及 Round、Time、Cost 或其他 Resource bound。

##### Retry

在指定 Outcome 出现后、限定 Attempt 或 Resource budget 内重复某项操作。

```text
Call API → Temporarily unavailable → Retry within budget
```

Retry 是由 Failure condition 驱动的 Loop，还需要处理 Target operation、Retryable outcome、Limit、Delay/Backoff 和前序副作用。

### 重要边界

| 容易混淆的概念 | 区别 |
| --- | --- |
| Fan-out / Broadcast | Fan-out 创建工作分支；Broadcast 扩大信息可见范围 |
| Fan-in / Reduce | Fan-in 控制收敛；Reduce 处理内容 |
| Branch / Route | Branch 选择后继路径；Route 选择 Actor |
| Route / Transfer | Route 选择候选目标；Transfer 改变 Continuation owner |
| Call / Delegate | Call 保留调用者 Continuation；Delegate 改变 Execution owner |
| Delegate / Transfer | Delegate 改变执行者；Transfer 改变接续者 |
| Wait / Stop | Wait 保留未来接续；Stop 产生终态 |
| Race / Voting | Race 是收敛策略；Voting 是可能的决策机制 |
| Gate / Circuit | Gate 是一次稳定转换；Circuit 连接 Gate、State Element 和 Continuation |
| Circuit / Pattern | Circuit 描述状态转换；Pattern 解决反复出现的 Forces 并包含 Consequences |

### 语义完整性

Circuit 无需回答所有工程问题，但不能隐藏会改变协作语义的问题。

- **Observable progress**：Dispatch、Enablement、Completion、Waiting 与 Silence 在需要时可区分；
- **Explicit state**：后续转换能找到其依赖的 Continuation、Ownership、Branch、Wait、Round 或 Transfer facts；
- **Explicit continuation**：每次转换后都能知道下一条路径或 Continuation owner；
- **Responsibility separation**：Execution ownership 与 Continuation ownership 保持可区分；
- **Branch fate**：每个 Fan-out branch 都有可以理解的去向；
- **Convergence**：Fan-in 表达 Membership、Threshold、Late result 和剩余分支行为；
- **Bounded suspension**：Wait 说明哪些条件可以 Resume、Timeout、Cancel 或解决等待；
- **Bounded repetition**：Loop 与 Retry 说明终止条件和资源边界；
- **Explicit transfer**：Transfer 标出新的 Continuation owner，可靠 Acceptance 由 L1 提供；
- **Explicit termination**：Stop 产生终态事实，不依赖沉默推断。

这些条件用于分析真正影响 Circuit 的部分，无需形成强制检查表或固定输出格式。

### L0 的边界

L0 不定义：

- Actor ID、Discovery、Addressing 和 Routing table；
- Message 与 Artifact format；
- Delivery、Acknowledgement、Ordering、Duplication 和 Idempotency mechanism；
- Correlation 与 Causation representation；
- Durable storage、Offline behavior、Resumption transport 和 Recovery；
- Permission、Trust、Budget 和 Delegation policy；
- Result aggregation algorithm；
- 完整 Multi-Agent system composition；
- Collaboration pattern 及其 Consequences。

这些问题分别推动 L1、Durable Execution Plane 和 L2 的设计。L0 可以暴露依赖，同时保持自身的抽象边界。
