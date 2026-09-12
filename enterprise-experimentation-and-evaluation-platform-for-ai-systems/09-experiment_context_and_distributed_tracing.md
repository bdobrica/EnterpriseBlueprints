## 10. Experiment context and distributed tracing

The platform should use OpenTelemetry and the W3C Trace Context format rather than defining a proprietary tracing protocol.

OpenTelemetry semantic conventions provide a common vocabulary for telemetry, including current GenAI-specific conventions.

The experimentation system adds only the identifiers it owns.

For example:

```text
experiment.id
experiment.iteration_id
experiment.variant
experiment.assignment_id

pipeline.id
pipeline.version

workflow.instance_id

execution.purpose
```

### 10.1 Execution purpose

Every execution should be classified:

```text
production
shadow
offline
evaluation
replay
synthetic
```

This prevents evaluation traffic from being mistaken for user traffic.

For example, an LLM judge invoking the same model gateway should not accidentally create a production exposure.

### 10.2 Experiment context propagation

Services participating in one execution need access to experiment context.

A small authenticated internal context envelope can propagate:

```text
assignment ID
variant
pipeline ID
workflow ID
execution purpose
issuer and audience
issued-at and expiration time
signing key ID
```

alongside normal W3C trace context.

OpenTelemetry baggage can propagate contextual values, but its documentation notes that baggage can leak to unintended downstream services and has no built-in integrity protection.

For treatment identity, a signed internal context envelope is therefore preferable when crossing service boundaries. Receivers derive tenant and treatment identity from authenticated workload context and this envelope; they reject conflicting caller-supplied fields, expired envelopes, and unauthorized audiences.

Before calling external model providers or third-party tools, experimentation context should be removed unless forwarding has been explicitly approved.

### 10.3 Trace semantics

A representative trace is:

```text
AGENT support-agent
│
├── RETRIEVER memory-service
│
├── LLM model-x
│
├── TOOL customer-lookup
│
├── TOOL refund
│
└── LLM model-x
```

Tracing helps answer questions such as:

```text
Did B retrieve different context?

Did B make more LLM calls?

Did B enter loops?

Did B trigger model fallback?

Did B use the wrong tool?

Did B require more retries?
```

Those are diagnostic questions.

Trace differences are not automatically causal effect estimates. Trace attributes are diagnostic evidence and may be sampled or dropped; decision-grade assignment, exposure, and outcome facts are reconciled against the experiment event ledger.

---
