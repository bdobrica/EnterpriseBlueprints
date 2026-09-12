## 20. Data architecture and telemetry retention

The default deployment uses four principal storage/transport systems.

```text
PostgreSQL
NATS JetStream or Kafka
ClickHouse
S3-compatible object storage
```

### 20.1 PostgreSQL

PostgreSQL stores strongly consistent control-plane metadata:

```text
experiments
pipelines
datasets
evaluators
metrics
users / organizations
governance state
audit metadata
```

### 20.2 Durable event stream

NATS JetStream or Kafka receives:

```text
experiment events
trace ingestion work
evaluation requests
materialization work
```

The platform should expose an internal event abstraction so deployments can choose either technology.

Events use:

```text
event_id
schema_version
tenant/project context
event timestamp
producer timestamp
```

### 20.3 ClickHouse

ClickHouse stores high-volume analytical facts:

```text
assignments
exposures
outcomes
evaluations
trace indexes
span metadata
materialized experiment-unit metrics
```

The schema should favor append-oriented ingestion.

### 20.4 Object storage

Large objects reside in S3-compatible storage:

```text
prompt payloads
model responses
tool results
retrieved documents
dataset artifacts
scenario fixtures
raw event archives
Parquet exports
```

The analytical database stores references rather than repeatedly embedding large payloads.

### 20.5 Context deduplication

Deep agent trajectories can repeatedly transmit large static context blocks.

At sufficient scale, immutable blocks can be content-addressed:

```text
system prompt
tool schemas
policies
static documents
```

and referenced by digest.

A context can then be represented as an ordered manifest:

```text
system      sha256:A
tool schema sha256:B
message     sha256:C
retrieval   sha256:D
```

rather than as a complete duplicated blob for every model call.

Cross-tenant content deduplication should not be assumed safe.

Content addressing should generally be scoped to a tenant or encryption domain.

### 20.6 Retention tiers

Evidence classes receive different retention:

```text
experiment ledger       long / authoritative
analysis materialized   long
trace metadata          medium
full trace payloads     configurable
debug payloads          shorter
raw archives            policy-dependent
```

Deleting a prompt payload should not require deleting the experiment assignment needed to preserve aggregate historical analysis.

---
