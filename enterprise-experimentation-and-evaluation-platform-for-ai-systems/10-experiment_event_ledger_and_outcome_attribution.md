## 11. Experiment event ledger and outcome attribution

Distributed traces are not sufficient as the authoritative experimental record.

A separate append-oriented event stream records:

```text
assignment
exposure
outcome
feedback
evaluation
```

### 11.1 Assignment event

Example:

```json
{
  "event_id": "evt-1",
  "type": "assignment",
  "experiment_id": "exp-184",
  "iteration_id": "iter-3",
  "assignment_id": "asg-87",
  "subject_type": "user",
  "subject_id": "user-192",
  "variant_id": "B",
  "timestamp": "..."
}
```

### 11.2 Exposure event

Exposure associates the experimental unit with the execution in which treatment-specific behavior occurred.

### 11.3 Outcome event

An outcome may occur during the trace:

```text
ticket_resolved = true
```

or much later:

```text
subscription_renewed = true
```

The latter should not require an open distributed trace.

### 11.4 Feedback event

User feedback can reference:

```text
subject
trace
message
span
```

depending on scope.

For example:

```text
thumbs_up
thumbs_down
1-5 rating
free-text feedback
```

Feedback is not assumed to be representative of all exposed users.

The platform should report:

```text
feedback rate
```

alongside the score.

### 11.5 Evaluation event

Evaluation records include:

```text
target
evaluator ID
evaluator version
score
label
metadata
```

### 11.6 Event transport

CloudEvents is a suitable optional envelope because it standardizes common event metadata while leaving domain payload semantics to the application.

At-least-once delivery plus stable event IDs is preferable to coupling all producers and stores through distributed exactly-once semantics.

---
