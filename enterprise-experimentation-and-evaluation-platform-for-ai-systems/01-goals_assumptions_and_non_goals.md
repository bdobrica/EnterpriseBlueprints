## 2. Goals, assumptions, and non-goals

### 2.1 Goals

The platform should evaluate changes at any layer of an AI pipeline.

Examples include:

```text
model
model provider
prompt
retrieval algorithm
retrieval parameters
memory implementation
agent runtime
planning strategy
tool implementation
tool schema
guardrail
routing policy
complete agent stack
```

The platform should support three principal evidence-producing modes:

```text
offline evaluation
shadow execution
online controlled experimentation
```

Offline evaluation determines how candidates behave under controlled scenarios.

Shadow execution determines how candidates behave on production-like inputs without affecting users.

Online experimentation estimates what changes when real subjects are randomly assigned to different treatments.

The platform should support agentic executions containing multiple model, retrieval, memory, and tool calls.

It should support synchronous interactions as well as long-running workflows that may suspend and resume over hours or days.

The serving path should remain available when the experimentation control plane is unavailable.

Historical experiment results should remain interpretable after prompts, models, tools, metric definitions, datasets, or evaluator implementations have changed.

Experiment evidence should be suitable for both engineering diagnosis and statistical analysis.

Every decision-grade online experiment should define the causal estimand, pre-treatment analysis population, randomization unit, observation window, and stopping rule before exposure begins.

The architecture should support hundreds or thousands of application requests per second while allowing telemetry and offline workloads to scale independently.

### 2.2 Assumptions

AI execution is stochastic.

The same pipeline and scenario may generate different trajectories on repeated runs.

One user interaction may generate many spans.

A model provider may perform retries or fallbacks internally or through a gateway.

Business outcomes can arrive after the original AI execution has completed.

Telemetry can be duplicated, delayed, corrected, delivered out of order, or lost, and that loss can differ across execution paths.

External providers may change implementation behind a stable public model identifier and may not support deterministic replay.

User feedback is normally sparse and self-selected.

Model-based evaluators are themselves probabilistic systems.

Some experimental treatments modify persistent state.

Several experiments may eventually coexist within one pipeline.

Subjects can interfere through shared accounts, queues, caches, memories, tools, or social relationships.

### 2.3 Non-goals

The platform does not prescribe a particular:

```text
agent framework
model provider
memory implementation
retrieval system
tool protocol
observability backend
message broker
analytical database
data warehouse
```

It does not treat model-based evaluator scores as ground truth.

It does not infer causal effects from shadow traffic or offline datasets.

It does not require a model gateway to participate in experiments.

It does not attempt to replace general-purpose observability systems.

The first implementation does not require:

```text
general state-tree virtualization
arbitrary multi-turn trace replay
agentic judges
automated evaluator-drift detection
experiment layers
persistent holdouts
cluster randomization
adaptive allocation
multi-agent causal attribution
automated root-cause analysis
```

The schemas should allow these capabilities to be introduced later.

---
