## 4. Domain model and identities

The system needs identifiers for several different concepts that can otherwise be accidentally conflated.

### 4.1 Experiment identity

```text
experiment_id
iteration_id
config_revision
variant_id
```

`experiment_id` identifies the logical experiment.

`iteration_id` identifies one immutable treatment definition.

These IDs are opaque and globally unique; tenant, project, and environment remain explicit authorization and query dimensions rather than being inferred from an ID string.

If the meaning of A or B changes, a new iteration is created.

`config_revision` can change operational configuration that does not redefine treatment identity, such as pause state or enrollment rate.

`variant_id` identifies a treatment arm within the iteration.

### 4.2 Subject identity

A **subject** is the entity being randomized.

A subject key is interpreted only together with its tenant, project, environment, subject type, and randomization namespace. A bare value such as `user-42` is not globally unique. SDKs should use a stable pseudonymous key when raw enterprise identifiers do not need to enter the experimentation plane.

The first implementation supports:

```text
request
user
session
workflow_instance
```

The schema should permit future subject types such as:

```text
account
tenant
device
organization
conversation
```

without changing the assignment protocol.

### 4.3 Workflow identity

Long-running agents require a persistent execution scope:

```text
workflow_instance_id
```

For example:

```text
workflow_instance_id = migration-8291
```

may encompass several executions:

```text
Monday      trace-1
Tuesday     trace-2
Thursday    trace-3
```

while retaining the same experimental assignment.

### 4.4 Assignment identity

```text
assignment_id
```

identifies:

```text
experiment iteration
+
randomization namespace
+
randomization unit
+
subject
```

The variant is the result attached to that assignment, not part of the assignment's identity. Otherwise two clients that incorrectly return different variants for the same subject also produce different IDs and conceal the split-brain decision. The identifier should be deterministic so duplicate events can be deduplicated and conflicting decisions for the same assignment can be quarantined.

### 4.5 Execution identity

```text
trace_id
span_id
```

follow OpenTelemetry semantics.

A trace represents an execution.

A span represents an operation inside that execution.

A trace is not normally an experimental unit.

### 4.6 Event identity

Each experiment event has:

```text
event_id
```

Event IDs make at-least-once ingestion practical because consumers can perform idempotent writes.

Every event also distinguishes:

```text
occurred_at     domain event time
observed_at     time recorded by the producer
ingested_at     time accepted by the platform
```

Corrections do not rewrite accepted events. They append a replacement or retraction that references the superseded event or source fact.

### 4.7 Evidence identity

Additional versioned objects include:

```text
pipeline_manifest_id
dataset_snapshot_id
scenario_version
evaluator_version
metric_version
environment_fixture_version
```

Together these identifiers allow the platform to reconstruct which system, data, measurement process, and analysis definition produced a reported result.

---
