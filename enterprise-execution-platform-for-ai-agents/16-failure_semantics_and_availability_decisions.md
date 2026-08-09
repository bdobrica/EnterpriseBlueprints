## 16. Failure semantics and availability decisions

A production design needs explicit behavior when enforcement dependencies fail.

### Identity or policy service unavailable

For write operations, the default should usually be fail closed.

Read-only operations may use a short-lived cached authorization decision where the data classification and business requirements permit it and the cache remains inside the policy-defined freshness bound.

### Governance-plane integrity or signing-key compromise suspected

A suspected compromise of the STS, revocation authority, policy-management path, Resource Allocation Service, approval/declassification authority, or governance signing keys is a security incident rather than an ordinary availability failure. New privileged grants, trust-lowering decisions, resource expansions, and high-risk writes should fail closed while the affected trust root is isolated or rotated.

Gateways should support emergency trust-root/key-epoch revocation so that assertions signed by a compromised key can be rejected even if their nominal token lifetime has not expired. Recovery should require controlled key rotation or policy restoration, reconciliation of gateway trust state, and independently evidenced administrative actions.

### Revocation distribution unavailable

Gateways may continue only while their locally reconciled security state remains within the operation's maximum allowed staleness.

Once that bound expires, a gateway cannot issue a new `ALLOW` that depends on stale revocation state. High-risk writes require live authoritative state.

A recovered gateway reconciles its local epoch state before resuming affected operations.

### Resource Allocation Service unavailable

A Run Governor may continue consuming resources that were already durably allocated to its run if local metering is trustworthy and policy permits disconnected enforcement.

It must not allocate new child budget, extend deadlines, increase quotas, or assume reclaimed capacity until the authoritative allocation service is available.

### MCP gateway unavailable

The run can remain durable in Temporal and retry later. The harness should not bypass the gateway by falling back to direct credentials.

### Side-Effect Ledger unavailable

New mutating operations that require a durable intent record are not dispatched. Read-only operations can follow separate policy.

If ledger state is `UNKNOWN`, the connector follows its tool-specific reconciliation policy rather than blindly retrying.

### LLM provider unavailable

The LLM gateway may route to an approved fallback model if the agent's model policy permits it. Otherwise the workflow remains suspended or retries.

### Memory or content-lineage service unavailable

A workflow that requires governed retrieval should not bypass the Memory Gateway. Policy may permit operation without optional memory, but it should not silently fall back to an ungoverned vector store.

### Approval or declassification channel unavailable

Temporal holds the pending action until an approval or review arrives or the policy-defined deadline expires. Required approval or judgment-based declassification fails closed on timeout.

### Independent evidence plane unavailable

Operational telemetry can degrade independently. An action configured as `PRECOMMIT_REQUIRED` is not dispatched without the required evidence acknowledgement. Lower-risk `ASYNC` evidence may be buffered according to policy.

### Policy changes during a suspended run

The action is re-evaluated on resume. An approval issued under an earlier policy does not automatically override current policy or revocation state.

These decisions are part of the platform contract and should be tested explicitly.

---

