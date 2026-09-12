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
planned stopping rule
planned sample size or duration
confidence level
```

Interim estimates can be displayed operationally.

The platform should not encourage repeatedly inspecting an ordinary fixed-horizon p-value and stopping when it becomes favorable.

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

### 18.3 Continuous metrics

Examples:

```text
latency
cost
token count
resolution time
```

The implementation should allow unequal-variance methods rather than assuming treatment and control have identical variance.

### 18.4 Sample ratio mismatch

The platform continuously compares observed randomization-unit counts with the configured allocation.

Microsoft has documented SRM as a high-value indicator of experiment data-quality and randomization problems.

Experiment state should therefore include:

```text
HEALTHY
INSUFFICIENT_DATA
DEGRADED
INVALID
```

A significant unexplained SRM should result in:

```text
INVALID
```

rather than an ordinary winner/loser result accompanied by a small warning.

### 18.5 A/A testing

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

### 18.6 Future analysis methods

The data model should support later addition of:

```text
CUPED
sequential testing
ratio-metric estimators
multiple-comparison correction
cluster-aware estimators
persistent holdouts
heterogeneous treatment analysis
adaptive allocation
```

CUPED, for example, uses pre-experiment covariates to reduce estimator variance and improve sensitivity.

These capabilities do not belong in the minimum statistical implementation.

---
