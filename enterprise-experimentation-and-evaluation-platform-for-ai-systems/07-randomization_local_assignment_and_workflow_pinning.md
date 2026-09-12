## 8. Randomization, local assignment, and workflow pinning

The decision SDK should be deterministic and side-effect free with respect to assignment.

A simplified assignment function uses a versioned pseudorandom function (PRF):

```text
canonical_input = encode(
    tenant_id,
    iteration_id,
    randomization_namespace,
    subject_type,
    subject_key,
    purpose
)

bucket = PRF(key_version, canonical_input) mapped to an unsigned integer range
```

`purpose` separates enrollment from variant assignment. A keyed cryptographic PRF such as HMAC-SHA-256 prevents predictable subject IDs from becoming an enumeration oracle; the key version is stable for the iteration and recorded in assignment metadata. The specification defines byte encoding, Unicode normalization, field lengths, unsigned conversion, and integer thresholds. It does not rely on language-specific string concatenation or floating-point comparisons.

The key and complete experiment configuration are distributed only to authenticated trusted workloads. Browser, mobile, and other user-controlled clients obtain a signed decision or assignment context from a trusted backend or sidecar; embedding the PRF key in public client code would defeat its protection.

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
enrollment_bucket = PRF(iteration, namespace, subject, "enrollment")
variant_bucket    = PRF(iteration, namespace, subject, "variant")
```

Then:

```text
enrollment_bucket < enrollment_rate
```

determines whether the subject enters the experiment.

The independently derived variant bucket determines A or B.

This permits monotonic traffic ramping without changing the A/B mapping for subjects already enrolled. Allocation is represented by immutable integer ranges, avoiding modulo bias and boundary disagreement between SDK languages.

Changing relative variant weights after exposure begins is prohibited for an iteration. The enrollment threshold is never lowered for persistent subjects; pause and stop change admission state without recomputing an existing assignment. A restarted treatment definition or allocation uses a new iteration.

`assignment_id` is derived from the iteration, namespace, subject type, and subject key before the variant is selected. The assignment event records the algorithm version, PRF key version, enrollment and variant buckets, allocation ranges, and selected variant. Two different variants for one assignment ID are a correctness failure.

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

The unit must be chosen at or above the level where treatment can create interference. Randomizing users is invalid when users share treatment-modified account state; the account must then be randomized or the state isolated. The experiment definition records the analysis unit separately when cluster-level assignment requires cluster-aware inference.

If the required randomization unit or estimator is not supported by the initial implementation, validation blocks the online experiment instead of substituting a convenient lower-level unit.

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

Snapshots include an activation time and validity interval. Clients reject revision rollback, record the revision used in every decision, and expose mixed-revision rates so a partial regional rollout can be distinguished from treatment effects.

---
