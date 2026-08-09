## 17. Architectural trade-offs

### The governance plane is a concentrated trust root

Moving authority, revocation, resource allocation, approvals, and declassification into trusted services improves consistency but concentrates security impact. Governance-plane hardening, administrative separation of duties, protected key custody, secure software supply chain, and independent evidence are therefore part of the architecture rather than optional operations hygiene.

The goal is not to pretend that a compromised trust root can be made harmless. It is to minimize standing privilege, reduce single-person and single-key failure modes, make dangerous changes harder to perform silently, and provide controlled revocation and recovery paths when compromise is suspected.

### Central gateways are high-value infrastructure

Concentrating tool, model, and governed-memory traffic simplifies policy and observability but makes the gateways latency-sensitive and highly available security services.

They require regional redundancy, capacity planning, load shedding, and carefully controlled bypass paths. Active-active regional deployments should preserve the same trusted tenant context, revocation semantics, and policy versions rather than becoming independent authorization islands.

### Multi-tenancy increases the value and cost of the control plane

If multiple business units or external tenants share infrastructure, `tenant_id` must come from trusted identity or routing context rather than a harness-supplied tool argument.

Tenant-aware isolation should cover at least:

- agent and skill registries;
- delegation and revocation;
- credential brokerage;
- resource allocation and quotas;
- memory and artifact metadata;
- approvals and declassification;
- Side-Effect Ledger records;
- evidence stores and encryption-key domains.

A useful default invariant is that authority from one tenant cannot name or operate on another tenant's resources unless an explicit cross-tenant sharing policy exists.

### Fine-grained authorization depends on good resource metadata

"May use GitHub" is easy to express and provides weak isolation.

Policies such as:

```text
may comment on pull requests
for repositories owned by this business unit
when the run was delegated for code review
but may not merge protected branches
```

require canonical resource identifiers, ownership data, classifications, and policy-maintained relationships.

The difficult part is frequently the enterprise resource model rather than the policy language.

### Authorization freshness competes with availability

Evaluating current directory, resource, and revocation state on every action maximizes responsiveness but creates dependencies on policy and identity systems.

Short-lived signed assertions, streaming invalidation, periodic reconciliation, and bounded caches can reduce latency while placing an explicit upper bound on staleness.

The bound should be stated as an SLO by risk class and tested under regional partitions and event-stream loss. A stale cache is never an unlimited license to preserve a previous `ALLOW`.

### Policy changes need simulation and controlled rollout

The durable run and evidence model make it possible to test policy changes before enforcement. Three modes should remain distinct:

```text
policy replay
  evaluate recorded historical authorization requests against a proposed policy

simulation
  execute a harness against mock or non-mutating connectors

shadow execution
  run an alternative harness, model, or policy path while suppressing external side effects
```

Policy replay can answer questions such as whether a proposed rule would have blocked or newly allowed historical actions. It should operate on recorded request context and policy inputs rather than treating a new LLM invocation as a deterministic reconstruction of the original reasoning.

New high-impact policy versions should support dry-run or shadow evaluation before becoming authoritative where business risk permits it.

### Durable execution creates versioning obligations

Runs may outlive deployments.

The platform needs a policy for existing runs:

```text
pin run to approved implementation digest
or
migrate durable state through an explicit migration
```

Implicitly moving an in-flight run to a materially different harness makes forensic reconstruction difficult.

### Stronger sandbox isolation costs more

MicroVM-backed environments improve tenant isolation but increase startup time and operational complexity. The platform should reserve stronger isolation for workloads that actually execute untrusted code rather than impose it on every typed-tool agent.

### Strong lineage creates declassification pressure

Conservative taint propagation is safer but can over-constrain low-risk workflows if all derived content remains permanently high-taint. That pressure should be relieved through narrowly governed deterministic extraction or explicitly authorized review, not by allowing the harness to lower trust labels itself.

---

