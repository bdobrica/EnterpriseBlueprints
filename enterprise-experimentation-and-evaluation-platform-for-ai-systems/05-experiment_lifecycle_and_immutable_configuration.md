## 6. Experiment lifecycle and immutable configuration

An experiment should have an explicit lifecycle.

```text
DRAFT
  │
  ▼
VALIDATED
  │
  ▼
READY
  │
  ▼
RUNNING ─────► PAUSED
  │              │
  │              └────► RUNNING
  │
  ├────► ABORTED
  │
  ├────► INVALID
  │
  ▼
COMPLETED
```

### 6.1 Draft

Draft experiments can be edited freely.

Validation checks include:

```text
valid randomization unit
valid pipeline manifests
metrics exist
variant weights sum correctly
dataset/evaluator references resolve
no conflicting identifiers
required guardrails defined
```

### 6.2 Publication

Publishing an experiment creates an immutable:

```text
iteration_id
```

The following are frozen for the iteration:

```text
hypothesis
variant definitions
pipeline manifests
randomization unit
primary metric
analysis method
```

If one of these changes materially, create a new iteration.

### 6.3 Configuration revisions

Operational state can evolve without redefining treatment identity.

Examples include:

```text
pause/resume
enrollment percentage
emergency stop
configuration expiration
```

Each publication creates a monotonically increasing:

```text
config_revision
```

Decision clients use complete snapshots rather than partially applied mutations.

### 6.4 Completion

Completing an experiment prevents new assignment and exposure.

Historical configuration remains queryable.

The final experiment record should retain:

```text
final configuration
analysis result
experiment health
decision
decision author
decision timestamp
```

The decision itself should not overwrite the measured result.

---
