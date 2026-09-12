## 9. Exposure, realized treatment, and intention-to-treat

Assignment alone does not prove that the treatment participated in an execution.

Suppose:

```text
B = memory-v2
```

and the agent never performs a memory lookup.

The platform records:

```text
ASSIGNMENT
user-18271 → B
```

without falsely recording:

```text
EXPOSURE
memory-v2 used
```

The **trigger** and the **exposure** have different roles. The trigger is a treatment-blind, pre-assignment condition that admits a subject to the analysis population. Exposure is a post-assignment fact showing that treatment-specific behavior began. Using exposure itself as the trigger can select different populations in A and B.

### 9.1 Exposure definition

Every experiment should define and version the operation that constitutes exposure. For a pipeline-wide treatment, exposure usually occurs immediately before dispatch to the selected pipeline. For a component treatment, it occurs immediately before the first variant-specific component operation.

Examples:

```text
first model request using model-Y
first retrieval using memory-v2
agent-v18 execution started
new ranking policy produced a result
```

Exposure events should carry:

```text
assignment_id
exposure_id
exposure_definition_version
trace_id
span_id where applicable
occurred_at
pipeline_manifest_id
```

The producer first durably appends the assignment to its bounded local evidence spool. At the defined treatment boundary, a trusted execution wrapper durably appends the exposure record before admitting dispatch of the treatment-specific operation. Exposure therefore means dispatch admission at that observable boundary, not completion inside an opaque provider. This ordering makes evidence loss less likely to differ by treatment and does not require a synchronous acknowledgement from a remote broker.

### 9.2 Exposure rate

Operational dashboards should display:

```text
assigned
exposed
exposure rate
```

Large treatment differences in exposure rate deserve investigation.

They may indicate:

```text
instrumentation bugs
eligibility bugs
control-flow differences
runtime failures
```

### 9.3 Realized treatment

Assigned and realized behavior may differ.

Example:

```text
assigned_variant = B
intended_model   = model-Y
realized_model   = model-X
fallback_reason  = provider_unavailable
```

The execution remains assigned to B.

The fallback behavior is part of the behavior produced by the B treatment protocol.

### 9.4 Intention-to-treat

The primary online analysis should use **intention-to-treat** semantics unless the experiment explicitly defines another statistically valid estimand. Its population includes every randomized subject admitted by the published pre-treatment trigger, whether or not exposure was later observed.

That means subjects are analyzed according to the treatment they were randomized to.

Filtering out:

```text
fallbacks
failures
timeouts
```

because they occurred after assignment can introduce selection bias.

Exposure and realized-treatment data remain essential for diagnostics and experiment-health analysis.

An exposure-conditioned comparison is descriptive unless exposure opportunity was fixed before assignment and defined identically for every variant. Filtering on an event that treatment can affect conditions on a post-treatment variable and does not recover a causal treatment effect.

The architecture deliberately keeps them separate from the primary assignment variable.

---
