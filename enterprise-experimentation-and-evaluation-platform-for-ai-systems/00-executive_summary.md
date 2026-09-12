# Designing an Enterprise Experimentation and Evaluation Platform for AI Systems

**Revision: 0.1.1**

This document describes a vendor-neutral architecture for evaluating changes to AI systems through offline evaluation, shadow execution, and online controlled experimentation. The platform treats models, prompts, retrieval systems, memory, tools, agent runtimes, policies, and complete agent pipelines as versioned treatments that can be evaluated using a common evidence model.

The initial implementation intentionally supports a narrower set of capabilities than the complete architecture described here. The architecture preserves extension points for more advanced experimentation and evaluation methods without requiring those capabilities in the first release.

---

## 1. Executive summary

AI applications increasingly consist of several independently changing components:

```text
agent runtime
memory and retrieval
system prompts
models
tools
guardrails
routing policies
application logic
```

A change to any one of these components can alter not only output quality but also cost, latency, tool usage, failure rate, trajectory length, and downstream business outcomes.

The unit being evaluated should therefore be the **system configuration that produced the behavior**, rather than an isolated prompt or model call.

This architecture defines that system configuration through an immutable **Pipeline Manifest**.

An experiment compares pipeline variants:

```text
A → pipeline manifest A
B → pipeline manifest B
```

The variants may differ by a single property:

```text
model X vs model Y
```

or by several components:

```text
agent runtime v17 + memory v4 + model X

vs

agent runtime v18 + memory v6 + model Y
```

The experimentation platform is divided into several concerns.

The **control plane** stores experiment definitions, pipelines, datasets, evaluators, and metrics.

The **decision plane** performs deterministic experiment assignment locally inside participating applications. Production requests do not depend synchronously on the availability of the experimentation service.

The **execution plane** runs the actual AI application.

The **evidence plane** records two related forms of evidence:

```text
distributed traces
experiment events
```

Traces describe what happened during an execution.

Experiment events record assignment, exposure, outcomes, feedback, and evaluations.

The **offline evaluation plane** executes pipeline variants against versioned scenarios inside resettable environments.

The **shadow plane** executes candidate pipelines against copies of real production inputs without exposing candidate output or side effects to users.

The **analysis plane** aggregates observations at the correct experimental unit and performs statistical analysis.

The initial statistical implementation supports:

```text
A/B experiments
binary metrics
continuous metrics
95% confidence intervals
sample ratio mismatch detection
fixed-horizon analysis
```

More advanced methods such as CUPED, sequential testing, experiment layers, persistent holdouts, cluster randomization, and adaptive allocation fit the same architecture but are not required for the first implementation.

The platform should answer three questions reliably:

```text
What exactly did we run?

What happened when we ran it?

Did assigning subjects to B change the outcome we care about?
```

Those questions correspond respectively to:

```text
pipeline identity

tracing and evaluation

controlled experimentation
```

The architecture keeps those concepts related without making them interchangeable.

---
