## 13. Shadow execution and rollout safety

Shadow execution runs a candidate asynchronously against copies of production inputs.

```text
production input
      │
      ├────► A ───► user-visible result
      │
      └────► B ───► shadow result
```

Variant B must not create user-visible side effects.

Writes should be:

```text
blocked
redirected
or executed in an isolated environment
```

### 13.1 Purpose

Shadow execution measures candidate behavior under realistic input distributions.

Typical observations include:

```text
latency
cost
token usage
tool usage
fallback frequency
guardrail failures
trajectory length
model-based scores
deterministic validation
```

Statsig's current AI evaluation tooling also exposes shadow candidate evaluation against production traffic, reflecting the usefulness of this deployment stage in current AI experimentation workflows.

### 13.2 Statistical interpretation

Shadow execution is not an online controlled experiment.

Users do not receive B.

The system therefore cannot use shadow results to estimate effects such as:

```text
conversion
retention
user satisfaction
task completion caused by user interaction with B
```

Shadow data is operational and evaluative evidence.

### 13.3 Safety

Shadow pipelines should have:

```text
separate credentials
restricted network access
side-effect suppression
independent budgets
rate limits
execution.purpose = shadow
```

Shadow execution should be sampled when running every production request twice would be prohibitively expensive.

---
