## 24. APIs, SDKs, interoperability, and internal standards

The platform should standardize semantics more aggressively than implementation technology.

### 24.1 Decision API

A local SDK API may resemble:

```text
decision = experiment.decide(
    experiment = "support-agent-v18",
    subject = {
        type: "user",
        id: "..."
    },
    context = {...}
)
```

and return:

```text
experiment_id
iteration_id
assignment_id
variant_id
pipeline_manifest_id
config_revision
```

### 24.2 Exposure API

```text
experiment.expose(
    assignment,
    trace_id,
    span_id
)
```

should be idempotent where the experiment semantics expect one exposure event.

Repeated exposures remain possible when the experiment needs them.

### 24.3 Outcome API

```text
experiment.outcome(
    subject,
    metric_source,
    value,
    timestamp
)
```

Outcomes need not originate from the AI application.

Business systems can publish them later.

### 24.4 Feedback API

```text
experiment.feedback(
    trace_id,
    subject_id,
    score
)
```

supports UI feedback and similar signals.

### 24.5 OpenTelemetry integration

Instrumentation should use standard OpenTelemetry exporters and semantic conventions.

Experiment identifiers become ordinary span attributes.

The system should integrate with existing observability backends rather than require traces to remain exclusively inside the experimentation platform.

### 24.6 Event interoperability

Experiment events can use a CloudEvents-compatible envelope.

Consumers may subscribe through:

```text
Kafka
NATS
webhooks
warehouse export
Parquet
```

### 24.7 Internal standards

The first specifications to freeze should be:

```text
identifier formats
assignment hash algorithm
hash input canonicalization
bucketing semantics
experiment iteration semantics
pipeline manifest schema
experiment event schemas
metric definition schema
evaluator definition schema
dataset snapshot schema
scenario schema
execution purpose values
trace attribute namespace
analysis-result schema
```

Cross-language SDKs must pass identical assignment conformance vectors.

For example:

```text
experiment X
user Y
salt Z
```

must produce the same bucket in:

```text
Go
Java
Python
Rust
TypeScript
```

### 24.8 Architectural trade-offs

The architecture makes several deliberate choices.

**Local assignment** favors availability and latency over globally centralized decision logic.

**A separate experiment ledger** adds another data product but prevents diagnostic trace storage from becoming the source of causal truth.

**Immutable pipeline manifests** add artifact-management requirements but make system-level treatment identity reconstructable.

**Resettable sandboxes** provide less generality than complete environment virtualization but make multi-turn agent evaluation tractable without inventing responses after trajectory divergence.

**Fixed-horizon statistics** provide fewer features than mature experimentation suites but establish a small statistically understandable foundation.

**A modular control service** avoids premature microservice decomposition while allowing high-volume ingestion, analysis, and evaluation workloads to scale separately.

---
