## 19. A practical implementation path

The complete architecture is large. Adoption can be staged while preserving the hard-to-retrofit security boundaries.

### Stage 0: prove the non-bypassable boundary

```text
Identity:
  existing enterprise IdP
  normal OAuth access tokens
  existing workload identity

One agent / one tool domain:
  stable run identity
  centralized tool PEP
  credential brokerage
  stable tool_call_id
  durable mutation intent
  structured security evidence
```

The purpose of Stage 0 is to prove that reusable enterprise credentials stay outside the harness and that consequential actions cross a non-bypassable enforcement point. Durable multi-day orchestration is not required to prove that boundary.

### Stage 1: durable governed agents

```text
Control plane:
  registered agents and approved digests
  REST run API
  Resource Allocation Service
  Revocation Controller
  action-bound approvals
  protected governance administration / key custody
  independent evidence for privileged governance changes

Execution:
  Kubernetes
  Temporal or equivalent
  Run Governor

Tools and models:
  central MCP gateway
  Side-Effect Ledger
  OAuth credential broker
  existing LLM gateway

Supply chain:
  OCI registry
  Cosign signatures and admission verification

Evidence:
  OpenTelemetry for operations
  independent structured security events
  immutable or WORM storage for selected evidence
```

### Stage 2: platform ecosystem

```text
subagent delegation with atomic resource reservation
content lineage and taint
explicit declassification workflows
governed cross-run memory
multi-tenant partitioning
policy replay / simulation / shadow execution
multi-region gateway redundancy
```

### Initial delegation and resource rules

The initial authorization rule can remain:

```text
allowed action =
    current user entitlement
  ∩ registered agent capability
  ∩ run resource constraints
  ∩ current gateway policy
```

Resource accounting adds the parallel rule that descendants cannot reserve or consume more than the parent subtree allocation.

More sophisticated multi-hop delegation or capability tokens can be added later without changing the trust boundary.

The early decisions that are difficult to retrofit are:

1. reusable enterprise credentials never enter prompts or arbitrary code environments;
2. consequential tool calls cross a non-bypassable enforcement point;
3. human, agent, workload, and tenant identities remain distinguishable;
4. run lifetime is independent of worker lifetime;
5. revocation is enforced at trusted gateways rather than by worker cancellation;
6. resource reservations are authoritative, atomic, and non-expanding across child runs;
7. side-effect semantics are explicit and tool-specific, including ambiguous `UNKNOWN` outcomes;
8. agent and skill implementations are immutable and attributable;
9. approvals and declassifications are bound to specific actions or transformations rather than broad sessions;
10. content provenance and taint survive summarization and memory promotion;
11. evidence records preserve enough information to reconstruct consequential actions;
12. selected high-risk writes can require independent evidence acknowledgement before dispatch;
13. governance-plane administrative authority, signing keys, policy publication, and trust-lowering operations are themselves strongly controlled and independently evidenced.

---

