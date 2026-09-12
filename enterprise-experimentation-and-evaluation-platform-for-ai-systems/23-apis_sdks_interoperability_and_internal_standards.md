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
config_valid_until
randomization_namespace
assignment_algorithm_version
key_version
enrollment_bucket and variant_bucket
```

### 24.2 Exposure API

```text
experiment.expose(
    assignment,
    exposure_id,
    exposure_definition_version,
    trace_id,
    span_id,
    occurred_at
)
```

should be idempotent where the experiment semantics expect one exposure event.

Repeated exposures remain possible when the experiment needs them. A trusted execution wrapper calls this API at the published dispatch boundary. It returns only after the exposure record is durably appended and refuses to admit dispatch when that guarantee cannot be met. The event means dispatch admission, not opaque-provider completion.

### 24.3 Outcome API

```text
experiment.outcome(
    outcome_id,
    source_fact_id,
    subject,
    metric_source,
    value,
    occurred_at,
    correction_of = optional
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

Instrumentation should use standard OpenTelemetry exporters and semantic conventions. Trace propagation uses W3C Trace Context; authenticated experiment context is carried separately because trace headers do not authorize a tenant or treatment.

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
eligibility and trigger semantics
estimand and analysis-population schema
pipeline manifest schema
experiment event schemas
event-time, deduplication, and correction semantics
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

**Local assignment** favors availability and latency over globally centralized decision logic. It requires authenticated atomic config distribution, protected PRF keys, cross-language conformance, expiry behavior, and monitoring for clients on mixed revisions.

**A separate experiment ledger** adds another data product but keeps authoritative assignment and outcome evidence independent of sampled diagnostic traces.

**Immutable pipeline manifests** add artifact-management requirements but make system-level treatment identity reconstructable.

**Resettable sandboxes** provide less generality than complete environment virtualization but make multi-turn agent evaluation tractable without inventing responses after trajectory divergence.

**Fixed-horizon statistics** provide fewer features than mature experimentation suites but establish a small statistically understandable foundation.

**A modular control service** avoids premature microservice decomposition while allowing high-volume ingestion, analysis, and evaluation workloads to scale separately.

---
