## 17. Metrics, guardrails, and operational budgets

A metric is a versioned semantic definition rather than a dashboard query.

A metric definition includes:

```text
metric_id
metric_version
description
source
analysis_unit
aggregation
directionality
attribution_window
missing-data behavior
```

### 17.1 Metric categories

The platform distinguishes:

#### Primary metric

The main outcome the experiment is intended to improve.

Example:

```text
ticket resolution rate
```

#### Secondary metrics

Additional decision context.

Examples:

```text
user rating
escalation rate
response latency
```

#### Guardrail metrics

Outcomes that should not regress beyond acceptable bounds.

Examples:

```text
error rate
policy violations
p95 latency
cost per task
```

#### Diagnostic metrics

Useful for explaining behavior but not necessarily business decision criteria.

Examples:

```text
tool calls
retrieval count
loop incidence
fallback count
```

### 17.2 Operational execution limits

Agent systems also need hard resource budgets:

```text
max cost / execution
max tokens / execution
max turns
max tool calls
max wall-clock time
```

These limits are enforced close to execution.

### 17.3 Variant-level circuit breakers

Aggregated safety policies may include:

```text
max variant spend / hour
max error rate
max guardrail violation rate
```

Example:

```text
Variant B

SRM               PASS
Error rate        PASS
Cost/request      +312%

Circuit breaker   TRIPPED
New exposure      PAUSED
```

This is an operational action.

It is not evidence that A is statistically superior to B.

### 17.4 Local versus global enforcement

Per-execution budgets can be enforced immediately.

Aggregate variant budgets depend on streaming observations and configuration propagation.

The platform should therefore document a maximum pause-propagation delay rather than implying globally instantaneous shutdown while preserving local decisioning.

---
