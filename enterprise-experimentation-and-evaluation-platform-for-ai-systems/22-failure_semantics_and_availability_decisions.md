## 23. Failure semantics and availability decisions

Failure behavior is part of the architecture.

| Failure | Required behavior |
|---|---|
| Experiment UI unavailable | Production execution continues. |
| Control API unavailable | Existing clients use last-known-good configuration. |
| Config publication unavailable | New experiments and ramps are delayed. |
| Local SDK cannot find an experiment | Use declared baseline/default behavior. |
| Event stream unavailable | Buffer experiment events within bounded limits. |
| Trace backend unavailable | Serving continues; diagnostic telemetry follows buffer/drop policy. |
| ClickHouse unavailable | Results become stale; serving continues. |
| Object storage unavailable | Large diagnostic payload capture may degrade; core experiment events continue. |
| Evaluator unavailable | Evaluations queue. |
| Offline runner unavailable | Offline experiments stop; online serving continues. |
| Analysis service unavailable | No new result is published; assignment continues. |
| Circuit-breaker analysis unavailable | Local execution budgets remain active. |

### 23.1 Last-known-good configuration

Decision SDKs persist or cache the most recent valid configuration snapshot.

Snapshots have:

```text
revision
signature
publication time
optional expiry
```

An invalid or partial update is rejected atomically.

### 23.2 Telemetry loss

Experiment evidence loss must be visible.

If an outage causes assignment or outcome events to be dropped beyond recoverable buffers, the experiment should expose:

```text
evidence_gap = true
```

and may be marked invalid depending on scale and affected population.

The system should not silently analyze known-incomplete evidence.

### 23.3 Local safety budgets

Per-execution limits remain enforceable during control-plane or analytics outages.

Examples:

```text
max token count
max tool calls
max duration
max cost
```

### 23.4 Global pauses

A global variant pause is propagated through the same configuration mechanism as other experiment state.

There is therefore a bounded propagation interval.

Systems requiring an instantaneous safety kill switch should implement that control in the application or agent execution platform rather than relying exclusively on experimentation infrastructure.

---
