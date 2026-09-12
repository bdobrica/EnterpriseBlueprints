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
pre-treatment eligibility and trigger
valid pipeline manifests
metrics exist
variant weights sum correctly
estimand and analysis population defined
minimum detectable effect and power assumptions
stopping and late-data rules
multiplicity policy
dataset/evaluator references resolve
no conflicting identifiers
required guardrails defined
owner and operational escalation path
```

`VALIDATED` means the immutable scientific and technical contracts pass automated checks. `READY` additionally means required approvals, production permissions, capacity checks, and safety controls are present. This distinction prevents schema validity from being mistaken for authorization to expose users.

### 6.2 Publication

Publishing an experiment creates an immutable:

```text
iteration_id
```

The following are frozen for the iteration:

```text
hypothesis
eligibility and trigger definition
variant definitions
pipeline manifests
randomization unit
randomization namespace and assignment algorithm version
allocation ranges
primary metric
estimand and analysis population
analysis method
attribution window and missing-data behavior
stopping rule and multiplicity policy
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
coordinated activation time
```

Each publication creates a monotonically increasing:

```text
config_revision
```

Decision clients use complete snapshots rather than partially applied mutations.

During a running iteration, the enrollment threshold may increase monotonically because independently hashed enrollment preserves previously admitted subjects. It is never lowered: that could switch a persistent subject back to baseline and introduce carryover. Pause or stop uses a separate admission state that blocks new subjects while previously assigned workflows remain pinned. Planned safety pauses are recorded in the run history; departures from the published analysis plan are protocol deviations. Variant allocation ranges remain frozen for the iteration.

### 6.4 Completion

Completing an experiment prevents new assignment and exposure. Already exposed long-running workflows either finish on their pinned treatment or follow a published cancellation policy; completion does not silently switch them to another pipeline.

A run can be closed to new traffic before its result is final. Results remain provisional until attribution windows, correction windows, and declared data-completeness checks have closed. Reanalysis after that point creates a new analysis revision and preserves the prior result.

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
