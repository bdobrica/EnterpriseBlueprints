## 12. Online controlled experiment execution

An online experiment follows this flow:

```text
request
   │
   ▼
resolve randomization subject
   │
   ▼
evaluate eligibility
   │
   ▼
deterministic local assignment
   │
   ▼
execute assigned pipeline
   │
   ├── exposure
   │
   ├── trace
   │
   ├── operational metrics
   │
   └── immediate outcomes
   │
   ▼
later business outcomes
   │
   ▼
experiment-unit materialization
   │
   ▼
statistical analysis
```

### 12.1 Eligibility

Eligibility belongs to the published experiment definition.

Typical conditions include:

```text
environment == production
country in [...]
application version >= X
feature capability available
```

Eligibility should not depend on post-treatment outcomes.

### 12.2 Subject materialization

Analysis does not operate directly over requests.

For a user-randomized experiment, raw evidence becomes something like:

```text
user_id
variant
assigned_at
exposed
ticket_resolution
total_cost
error_count
```

One user remains one randomized unit regardless of how many traces or model calls the user's workload generated.

### 12.3 Delayed outcomes

Metric definitions specify attribution windows.

Example:

```text
renewal within 14 days after assignment
```

Late-arriving data can revise experiment aggregates until the attribution window closes.

### 12.4 Decision record

At experiment completion, the system stores:

```text
effect estimate
confidence interval
sample size
health state
metric versions
analysis version
decision
```

The decision may be:

```text
ship B
keep A
continue development
rerun experiment
invalid experiment
```

The platform records the decision without pretending that it follows mechanically from one p-value.

---
