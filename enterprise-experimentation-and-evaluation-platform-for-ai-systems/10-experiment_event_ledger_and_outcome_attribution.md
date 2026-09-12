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

Every event envelope includes:

```text
event_id and schema version
tenant, project, and environment
producer identity
source_fact_id when the fact originates elsewhere
occurred_at, observed_at, and ingested_at
```

The immutable raw event archive is the authoritative evidence record. Query stores and experiment-unit tables are reproducible materializations from that archive.

### 11.1 Assignment event

Example:

```json
{
  "event_id": "evt-1",
  "type": "assignment",
  "experiment_id": "exp-184",
  "iteration_id": "iter-3",
  "assignment_id": "asg-87",
  "config_revision": 14,
  "assignment_algorithm": "prf-v1",
  "key_version": "2026-03",
  "randomization_namespace": "support-agent",
  "subject_type": "user",
  "subject_id": "user-192",
  "variant_id": "B",
  "enrollment_bucket": 19381,
  "variant_bucket": 49852,
  "allocation_range": "32768..65535",
  "occurred_at": "...",
  "observed_at": "..."
}
```

### 11.2 Exposure event

Exposure associates the experimental unit with the execution in which treatment-specific behavior was admitted. It includes a stable `exposure_id`, exposure-definition version, execution and operation identity, intended pipeline manifest, and event time. At the published treatment boundary, a trusted execution wrapper durably records exposure before admitting dispatch. This observable boundary avoids pretending the ledger can prove completion inside an opaque provider.

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

An outcome includes a stable `outcome_id`, source fact identity, metric source and version, subject or execution scope, value, and event time. A source correction or deletion appends a correction or retraction referencing the prior fact; consumers never infer the latest truth by silently overwriting history.

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

### 11.6 Attribution and materialization

Outcome attribution is a deterministic, versioned transformation. It specifies the subject join, assignment anchor, observation window, time-zone behavior, deduplication, correction precedence, and treatment-blind handling of missing and late facts. Facts are never selected or excluded according to the assigned variant.

Each materialized row retains the assignment, metric-definition version, attribution version, source watermark, and contributing fact IDs. Rebuilding from the same archive and versions must produce the same result. A changed attribution rule creates a new analysis revision.

### 11.7 Event transport

CloudEvents is a suitable optional envelope because it standardizes common event metadata while leaving domain payload semantics to the application.

At-least-once delivery plus stable event IDs is preferable to coupling all producers and stores through distributed exactly-once semantics.

---
