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

### 9.1 Exposure definition

Every experiment should define the operation that constitutes exposure.

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
trace_id
span_id where applicable
timestamp
pipeline_manifest_id
```

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

The primary online analysis should use **intention-to-treat** semantics unless the experiment explicitly defines another statistically valid estimand.

That means subjects are analyzed according to the treatment they were randomized to.

Filtering out:

```text
fallbacks
failures
timeouts
```

because they occurred after assignment can introduce selection bias.

Exposure and realized-treatment data remain essential for diagnostics and experiment-health analysis.

The architecture deliberately keeps them separate from the primary assignment variable.

---
