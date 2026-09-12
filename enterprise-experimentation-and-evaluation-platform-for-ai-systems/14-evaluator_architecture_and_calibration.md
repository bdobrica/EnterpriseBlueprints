## 15. Evaluator architecture and calibration

The initial platform supports two automated evaluator classes:

```text
deterministic
model-based
```

Agentic evaluators are a later extension.

### 15.1 Deterministic evaluators

When the environment contains an objectively measurable result, deterministic checks are preferred.

Examples:

```text
refund_count == 1
ticket_status == resolved
JSON conforms to schema
required tool was invoked
forbidden tool was not invoked
database row exists
latency < threshold
```

This is cheaper, reproducible, and usually easier to debug than asking another model to infer the outcome.

### 15.2 Model-based evaluators

Model-based evaluation applies to properties such as:

```text
helpfulness
clarity
tone
groundedness
explanation quality
instruction following
```

Every evaluator is versioned.

A definition contains:

```text
evaluator_id
evaluator_version
judge_model
judge_prompt
rubric
input contract
output contract
```

Every emitted score retains that provenance.

### 15.3 Calibration

The platform maintains a human-labelled calibration dataset.

Periodically:

```text
judge output
      │
      ▼
compare with human labels
      │
      ▼
agreement / error analysis
```

This prevents a change in evaluator implementation from being mistaken for a change in candidate quality.

Anthropic similarly recommends combining deterministic, model-based, and human assessment according to the property being measured rather than treating an LLM grader as an infallible oracle.

### 15.4 Evaluator changes

Changing:

```text
judge model
prompt
rubric
normalization
output interpretation
```

creates a new evaluator version.

Historical scores remain associated with the evaluator that produced them.

### 15.5 Post-MVP evaluator governance

Future versions can add:

```text
cross-evaluator audits
blind pairwise judging
order randomization
abstention
drift alerts
judge-family bias analysis
agentic judges
human review queues
```

The evaluator schema already supports these extensions.

---
