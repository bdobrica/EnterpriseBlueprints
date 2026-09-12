## 23. Failure semantics and availability decisions

Failure behavior is part of the architecture.

| Failure | Required behavior |
|---|---|
| Experiment UI unavailable | Production execution continues. |
| Control API unavailable | Existing clients use last-known-good configuration. |
| Config publication unavailable | New experiments and ramps are delayed. |
| Last-known-good config expired | Admit no new experiment exposure; serve the declared baseline. Pinned workflows follow the published outage policy. |
| Regions disagree on active config revision | Mark health degraded and stop new enrollment until convergence. |
| Local SDK cannot find an experiment | Use declared baseline/default behavior. |
| Event stream unavailable | Buffer experiment events in the durable local spool within bounded limits. |
| Evidence spool full or unavailable | Admit no new experimental exposure; serve baseline and surface the evidence gap after recovery. |
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

and may be marked invalid depending on scale, affected population, and whether loss differs by assignment, execution stage, region, or time.

The system should compare expected and observed evidence rates by variant and execution stage. Differential or unbounded loss can bias an estimate even when the absolute missing fraction is small, so the system should not silently analyze known-incomplete evidence.

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
