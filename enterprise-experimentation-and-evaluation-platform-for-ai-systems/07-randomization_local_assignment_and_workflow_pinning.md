## 8. Randomization, local assignment, and workflow pinning

The decision SDK should be deterministic and side-effect free with respect to assignment.

A simplified assignment function is:

```text
bucket =
    H(
      experiment_iteration,
      randomization_namespace,
      subject_id,
      salt
    )
```

mapped uniformly onto `[0,1)`.

A good implementation provides:

```text
repeatability
uniform allocation
independence across experiments
cross-language consistency
```

These properties match the variant-assignment requirements described by LinkedIn's experimentation platform.

### 8.1 Enrollment and assignment

It is useful to separate experiment enrollment from variant assignment.

For example:

```text
enrollment_bucket =
    H(experiment, subject, "enrollment")

variant_bucket =
    H(experiment, subject, "variant")
```

Then:

```text
enrollment_bucket < enrollment_rate
```

determines whether the subject enters the experiment.

The independently derived variant bucket determines A or B.

This permits gradual traffic ramping without changing the A/B mapping for subjects already enrolled.

For the MVP, changing the relative A/B split after exposure begins should be avoided.

### 8.2 Supported randomization units

The first implementation supports:

```text
request
user
session
workflow_instance
```

The experiment definition must state the randomization unit explicitly.

The analysis engine must know the same unit.

### 8.3 Workflow pinning

Long-running workflows carry:

```text
experiment_id
iteration_id
assignment_id
variant_id
pipeline_manifest_id
```

when suspended.

When they resume, they do not recompute the assignment from the current experiment configuration.

Example:

```text
workflow_instance = migration-8291
variant           = B
pipeline          = agent-v18
```

remains pinned over the lifetime of the workflow.

This avoids starting a stateful process on one treatment and completing it on another.

### 8.4 Configuration propagation

Decision clients maintain the last known valid configuration.

Control-plane outage means:

```text
cannot start newly published configuration
```

rather than:

```text
cannot serve production requests
```

Configuration snapshots should be signed or otherwise authenticated when distributed across trust boundaries.

---
