## 25. Practical implementation path and conclusion

The system should be implemented in stages that protect semantic boundaries before adding advanced functionality.

### Stage 0 — contracts and conformance

Define:

```text
experiment schema
pipeline manifest
subject types
assignment algorithm
assignment ID
exposure semantics
experiment event schema
metric schema
evaluator schema
dataset/scenario schema
execution-purpose taxonomy
```

Create cross-language assignment conformance tests.

Run simulated and real A/A tests.

### Stage 1 — online experimentation core

Build:

```text
control service
Experiment Registry
Pipeline Registry
Metric Registry

configuration publisher
local decision SDK

assignment ingestion
exposure ingestion
outcome ingestion
feedback ingestion

PostgreSQL
event stream
ClickHouse

fixed-horizon binary/continuous analysis
confidence intervals
SRM detection
basic experiment UI
```

This stage should be capable of running a correct ordinary online A/B experiment before introducing AI-specific scoring.

### Stage 2 — AI tracing

Add:

```text
OpenTelemetry integration
experiment trace attributes
pipeline provenance
model usage
cost
token counts
tool spans
retrieval spans
workflow IDs
```

The event ledger remains independent of trace retention.

### Stage 3 — offline evaluation

Add:

```text
Dataset Registry
Evaluator Registry
scenario runner
resettable sandboxes
deterministic evaluators
model-based evaluators
repeated trials
human calibration dataset
```

Do not require general multi-turn trace replay.

### Stage 4 — shadow execution

Add:

```text
production input sampling
shadow dispatcher
side-effect suppression
shadow budgets
candidate evaluation
shadow trace comparison
```

Shadow evidence remains separate from causal online experiment results.

### Stage 5 — operational safety and enterprise controls

Add:

```text
per-execution budgets
variant spend limits
circuit breakers
SSO
RBAC
audit
retention
data residency
payload capture policies
private networking
```

### Stage 6 — scale optimization

Add where measurement justifies it:

```text
content-addressed payload storage
tail-based trace sampling
regional ingestion
regional storage
warehouse export
materialized analysis tables
multi-region config distribution
```

### Stage 7 — advanced experimentation

Later releases can introduce:

```text
CUPED
sequential testing
holdouts
experiment layers
cluster randomization
ratio metrics
multiple-comparison correction
adaptive allocation
```

Google's work on overlapping experiments and Microsoft's published experimentation research provide useful reference models for these extensions.

### Stage 8 — advanced agent evaluation

Potential later capabilities include:

```text
agentic judges
evaluator drift monitoring
cross-evaluator consistency audits
snapshot/fork environment virtualization
persistent-state isolation
automated trace clustering
failure localization
counterfactual component evaluation
multi-agent diagnostic attribution
```

These capabilities should extend the existing evidence model rather than redefine it.

### Conclusion

The main architectural requirement is that evaluation evidence retain its meaning as the system grows.

A pipeline manifest identifies the intended treatment.

Local deterministic assignment determines which treatment a randomized subject receives without placing the experimentation service in the production request path.

Exposure and runtime provenance explain whether and how that treatment was actually encountered.

OpenTelemetry traces record execution details.

The experiment ledger connects assignment to outcomes over longer periods.

Offline scenarios test pipeline candidates from controlled initial state.

Shadow execution tests candidate behavior against production input distributions without exposing users.

Online randomized experiments estimate changes in user or business outcomes.

Evaluators remain versioned measurement instruments.

Statistical analysis operates on the randomization unit rather than on whichever telemetry objects happen to be easiest to query.

This produces a platform whose first implementation can remain relatively small:

```text
control service
local assignment SDK
experiment event ingestion
OpenTelemetry
PostgreSQL
NATS/Kafka
ClickHouse
object storage
offline runner
resettable sandbox
deterministic/model-based evaluators
fixed-horizon A/B analysis
```

while preserving the identities and contracts needed for a substantially more capable experimentation system later.

The architecture is successful when a reviewer can return to an experiment months later and establish:

```text
what population was randomized
what treatment each subject was assigned
what pipeline that treatment represented
what actually executed
which outcomes were observed
which evaluator produced each derived score
which metric definitions were used
whether the experiment was healthy
and what evidence supported the final decision
```

That is the foundation on which more sophisticated experimentation and AI evaluation capabilities can safely be built.

---
