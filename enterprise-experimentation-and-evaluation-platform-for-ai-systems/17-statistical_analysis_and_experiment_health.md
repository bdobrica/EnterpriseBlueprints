## 18. Statistical analysis and experiment health

The first implementation intentionally supports a small statistical surface.

```text
A/B allocation
binary metrics
continuous metrics
95% confidence intervals
fixed-horizon analysis
sample ratio mismatch detection
```

### 18.1 Fixed-horizon design

Before launch, an experiment records:

```text
primary metric
estimand, analysis population, and trigger
planned stopping rule
planned sample size or duration
minimum detectable effect and power
allocation ratio
confidence level
observation and correction windows
expected data delay
```

Interim estimates can be displayed operationally.

The platform should not encourage repeatedly inspecting an ordinary fixed-horizon p-value and stopping when it becomes favorable. A confirmatory result is calculated only at the declared horizon or under another stopping rule published before exposure.

Sequential inference can be added later when continuous stopping decisions are required.

### 18.2 Binary metrics

Examples:

```text
resolved / not resolved
converted / not converted
escalated / not escalated
```

The analysis reports:

```text
A rate
B rate
absolute difference
relative difference where meaningful
confidence interval
sample sizes
```

Confidence intervals use a score/Newcombe method or a regression method with appropriate robust uncertainty. A simple Wald interval is not the default because it performs poorly for rare outcomes and small samples.

### 18.3 Continuous metrics

Examples:

```text
latency
cost
token count
resolution time
```

For per-unit means, the implementation uses unequal-variance or heteroskedasticity-robust methods rather than assuming identical variance. Heavy-tailed metrics require a predeclared transformation, trimming or robust estimator, and reports retain interpretable raw-scale summaries. Ratio and quantile metrics remain unsupported until their corresponding estimators and validation tests exist.

All analyses apply the published observation window. The fixed-horizon MVP waits for included units to mature before final analysis and reports data completeness explicitly. A time-to-event estimand with right censoring requires a declared survival-analysis method and remains unsupported until that method is implemented; immature units are never silently dropped.

### 18.4 Sample ratio mismatch

The platform compares assignment counts at the randomization unit with the configured allocation on a predefined cadence and threshold. Exposure imbalance is reported separately: it can reveal post-assignment execution or instrumentation failures but is not the same SRM test.

Microsoft has documented SRM as a high-value indicator of experiment data-quality and randomization problems.

Experiment state should therefore include:

```text
HEALTHY
INSUFFICIENT_DATA
DEGRADED
INVALID
```

A significant SRM first triggers diagnosis of scope, bot/filter rules, duplicate handling, config revisions, and telemetry completeness. A significant unexplained SRM should result in:

```text
INVALID
```

rather than an ordinary winner/loser result accompanied by a small warning.

The threshold and monitoring cadence are fixed in advance; repeatedly testing many slices and times without accounting for those looks creates false alarms.

### 18.5 Multiplicity and interpretation

Each iteration has one confirmatory primary metric. Secondary metrics are exploratory unless included in a predefined family with an adjustment such as Holm's procedure. Inferential guardrails use their own predefined family or non-inferiority policy; streaming operational thresholds remain circuit-breaker signals.

Reports lead with the effect estimate and confidence interval. A binary significance label does not establish practical value, safety, or absence of harm.

### 18.6 A/A testing

Before the platform is trusted for production decisions, it should run repeated A/A experiments.

These validate:

```text
assignment
event collection
metric joins
SRM behavior
confidence-interval calibration
false-positive behavior
```

### 18.7 Future analysis methods

The data model should support later addition of:

```text
CUPED
sequential testing
ratio-metric estimators
hierarchical and false-discovery-rate procedures
cluster-aware estimators
persistent holdouts
heterogeneous treatment analysis
adaptive allocation
```

CUPED, for example, uses pre-experiment covariates to reduce estimator variance and improve sensitivity.

These capabilities do not belong in the minimum statistical implementation.

---
