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
evaluate pre-treatment trigger
   │
   ▼
deterministic local assignment
   │
   ▼
durably spool assignment
   │
   ▼
execute assigned pipeline
   │
   ├── durable exposure at each published treatment boundary
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

Eligibility and the analysis trigger are immutable, treatment-blind, and evaluated before assignment. Neither may depend on post-treatment control flow, exposure, or outcomes.

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
observation_window_complete
```

One user remains one randomized unit regardless of how many traces or model calls the user's workload generated.

Materialization starts from all admitted assignments, then left-joins outcomes using the published attribution rule. It defines aggregation within a unit, missing-outcome behavior, and right-censoring before results are calculated. Starting from exposed or outcome-bearing rows would discard randomized subjects and bias the population.

### 12.3 Delayed outcomes

Metric definitions specify attribution windows.

Example:

```text
renewal within 14 days after assignment
```

Late-arriving data can revise experiment aggregates until the attribution and correction windows close. Watermarks and source-completeness checks determine whether a result is provisional; wall-clock experiment completion alone does not make delayed outcomes final.

### 12.4 Decision record

At experiment completion, the system stores:

```text
effect estimate
confidence interval
sample size
health state
metric versions
analysis version
estimand and analysis population
stopping and multiplicity rules
data-completeness state
protocol deviations
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
