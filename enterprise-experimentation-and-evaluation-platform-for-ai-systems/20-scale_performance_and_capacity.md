## 21. Scale, performance, and capacity

Agent telemetry amplifies application traffic significantly.

Assume:

```text
500 application requests / second
20 spans / request
```

The tracing plane receives approximately:

```text
10,000 spans / second
```

or approximately:

```text
864 million spans / day
```

before offline trials and evaluator executions are included.

The architecture therefore scales decisioning, ingestion, evaluation, and analysis independently.

### 21.1 Decision SDK

Decisioning is local.

No network request is required per evaluation.

The operation should consist primarily of:

```text
config lookup
eligibility evaluation
hash computation
bucket lookup
```

### 21.2 Configuration distribution

Configuration freshness can tolerate seconds of delay in most experiments.

Correctness requires clients to apply complete revisions atomically.

### 21.3 Event ingestion

Ingestion services remain stateless.

Events are acknowledged after acceptance by the durable stream rather than after ClickHouse analysis completes.

### 21.4 Backpressure

If downstream systems are overloaded:

```text
experiment events → preserve / spool
diagnostic traces → sample or drop according to policy
evaluation work → queue
offline runs → throttle
```

The serving application should not block on analytical backpressure.

### 21.5 Trace sampling

Experiment events required for analysis should not rely on trace sampling.

Diagnostic traces can use:

```text
small unbiased baseline sample
+
100% retention of selected anomalies
```

such as:

```text
errors
guardrail failures
high latency
high cost
human escalation
```

The two samples should be distinguishable.

Failure-enriched trace samples are useful for diagnosis but are not representative experiment datasets.

### 21.6 Offline quotas

Offline runs can generate large amounts of model traffic.

The runner therefore enforces:

```text
tenant concurrency
dataset concurrency
model budget
scenario budget
daily cost budget
```

Offline capacity is independent of production request-serving capacity.

### 21.7 Multi-region deployment

A large deployment can run:

```text
regional configuration caches
regional ingestion
regional ClickHouse clusters
region-scoped object storage
```

while maintaining a logically unified control plane.

Data-residency policy determines whether raw payloads may leave the originating region.

---
