## 18. What should be standardized internally

The enterprise does not need to standardize how agents reason.

It benefits from standardizing the interfaces around them.

A minimal internal agent-runtime contract might cover:

```text
AgentDefinition
RunIdentity
TenantContext
DelegationContext
RevocationContext
ResourceEnvelope
ResourceReservation
RunLifecycle
ToolRequest
ToolResult
SideEffectIntent
ApprovalRequest
DeclassificationRequest
ArtifactReference
ContentTrustMetadata
SandboxInterface
EvidenceEvent
GovernanceChangeEvent
KeyEpoch
```

For example:

```typescript
interface AgentRuntime {
  createRun(agent: AgentRef, objective: unknown): Promise<RunRef>;
  getRun(run: RunRef): Promise<RunState>;
  signalRun(run: RunRef, signal: RunSignal): Promise<void>;
  cancelRun(run: RunRef, reason: string): Promise<void>;
}

interface ResourceAllocator {
  reserveChild(parent: RunRef, child: RunRef, request: ResourceRequest): Promise<ResourceEnvelope>;
  // Terminal settlement atomically records trusted consumption and credits unused
  // reservation back to the parent's authoritative available pool.
  settleUsage(run: RunRef, usage: UsageRecord, terminal?: boolean): Promise<SettlementResult>;
  // Idempotent recovery/fallback for reservations that require explicit reconciliation.
  reclaim(run: RunRef): Promise<ReclaimResult>;
}

interface SandboxService {
  provision(profile: SandboxProfile): Promise<SandboxRef>;
  execute(sandbox: SandboxRef, request: ExecuteRequest): Promise<ExecuteResult>;
  destroy(sandbox: SandboxRef): Promise<void>;
}
```

The stable contract is concerned with identity, authority, revocation, lifecycle, resources, content trust, side effects, and evidence.

The harness remains replaceable.

This direction is visible in current vendor systems. Anthropic describes stable session/harness/sandbox interfaces intended to survive changes in harness implementation.[1] Microsoft is making agent identity a separate governable enterprise object.[2] MCP is adding enterprise-managed authorization for centrally governed tool connectivity.[6] NIST is explicitly studying how existing identity and authorization standards apply to software and AI agents.[15]

---

