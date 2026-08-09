## 9. Sandbox isolation for generated code

Not every agent needs arbitrary code execution.

A Slack or Jira automation that operates entirely through typed tools may require no sandbox at all.

Agents that execute model-generated shell commands, Python, downloaded packages, browsers, or user-supplied code require a stronger isolation boundary.

The isolation mechanism should be selected based on risk rather than fixed globally.

```text
low risk:
  typed tools only

moderate risk:
  hardened container / gVisor-style sandbox

high risk or multi-tenant arbitrary code:
  hardware-virtualized sandbox such as Kata or dedicated microVM
```

Kata Containers is designed to combine container orchestration with hardware-virtualized isolation and supports several hypervisors, including Firecracker.[10]

The architecture should therefore depend on a sandbox interface rather than on a particular hypervisor.

```text
provision(profile) -> sandbox_id
execute(sandbox_id, command) -> result
snapshot(sandbox_id) -> artifact_ref
destroy(sandbox_id)
```

### Sandbox policy

A high-risk sandbox should generally have:

- no reusable user or agent credentials;
- no cloud-instance metadata access;
- no direct route to internal control-plane services;
- bounded CPU, memory, disk, and execution time;
- controlled egress through a policy-aware proxy;
- ephemeral root filesystems where feasible;
- explicit artifact export rather than arbitrary host mounts.

Anthropic has described the same structural goal in its hosted agent architecture: generated code executes in an environment where sensitive credentials are not reachable, while authenticated external actions pass through a separate proxy or vault-backed tool path.[1]

---

