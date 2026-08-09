## 14. Independent evidence and audit architecture

The platform needs security evidence beyond ordinary application logs. The evidence path should be independently governed so that compromise of a harness or worker cannot suppress the records needed to reconstruct consequential actions.

For a consequential action, reviewers should be able to reconstruct:

```text
initiating principal
agent and implementation version
run identity
tenant
skill versions
model-policy decision
content-lineage context when relevant
requested tool and arguments
policy decision and version
revocation / authorization epoch
human approval or declassification, if any
side-effect-ledger identity
external result
```

### 14.1 CloudEvents as an event envelope

CloudEvents is useful as a vendor-neutral event format. Its specification explicitly focuses on interoperable event representation; authorization, integrity, confidentiality, and persistence mechanisms are outside its scope.[13]

A CloudEvent can therefore be the envelope for an audit record, but CloudEvents does not make the record non-repudiable.

Example:

```json
{
  "specversion": "1.0",
  "id": "c1f7a421-392d-4d7a-8b89-29007f3099a1",
  "source": "urn:corp:agent-platform:mcp-gateway",
  "type": "com.corp.agent.tool.completed.v1",
  "subject": "run-991823ab",
  "time": "2026-08-08T12:12:15Z",
  "datacontenttype": "application/json",
  "data": {
    "tool_call_id": "call-3821",
    "agent_id": "github-pr-reviewer",
    "actor_workload": "spiffe://corp.example/agents/github-pr-reviewer",
    "human_principal_ref": "principal-83d...",
    "target_tool": "github.pull.comment",
    "policy_version": "agent-tools-128",
    "security_epoch": 193,
    "request_hash": "sha256:...",
    "response_hash": "sha256:...",
    "external_status": 201
  }
}
```

### 14.2 Tamper resistance and tamper evidence

WORM storage protects records after they have been written. For example, S3 Object Lock Compliance Mode prevents a protected object version from being overwritten or deleted during the retention period, including by the AWS account root user.[14]

That is useful but does not prove:

- that every event was emitted;
- that the producer identity was genuine;
- that events were not reordered before storage;
- that the record was not fabricated before it was locked.

A stronger evidence pipeline can add:

- authenticated producers using workload identity;
- monotonic per-run sequence numbers;
- hashes linking adjacent security events;
- signed event batches or attestations;
- acknowledgements from the immutable sink before selected high-risk operations commit;
- independent security-domain ownership of the long-term evidence store.

The appropriate term is usually **tamper-resistant or tamper-evident audit evidence**. "Non-repudiation" should be reserved for designs that define the cryptographic and organizational semantics required to support that claim.

### 14.3 Evidence acknowledgment modes

Tool or operation policy should state whether evidence delivery is asynchronous or part of the pre-dispatch contract.

```text
ASYNC
  evidence may be persisted after execution

INTENT_DURABLE
  mutation intent must be durably acknowledged before dispatch

PRECOMMIT_REQUIRED
  authorization + intent evidence must be acknowledged by the
  independent security evidence plane before external dispatch
```

Typical policy mappings might look like:

| Evidence mode | Typical use | Example action classes | Failure posture |
| --- | --- | --- | --- |
| `ASYNC` | Low-risk or read-only operations where evidence can be buffered | Repository reads, ticket search, low-risk model-policy decisions | Operation may proceed while evidence is buffered within policy limits. |
| `INTENT_DURABLE` | Ordinary mutations that need a durable retry/reconciliation identity before dispatch | Post an internal comment, create a non-sensitive ticket, update low-risk workflow metadata | Mutation intent must be durable; dispatch is blocked if the required intent record cannot be persisted. |
| `PRECOMMIT_REQUIRED` | High-impact privileged or destructive operations where independent evidence is part of the authorization contract | Production deployment/restart, IAM or permission changes, destructive cloud operations, high-impact governance changes | External dispatch fails closed until authorization and intent evidence are acknowledged. |

For example:

```yaml
tool: kubernetes.deployment.restart
risk: critical

evidence:
  mode: PRECOMMIT_REQUIRED
```

If the evidence plane is unavailable for a `PRECOMMIT_REQUIRED` operation, the external mutation is not dispatched.

### 14.4 Privacy and evidence minimization

Complete model transcripts and tool payloads may contain sensitive information. They should not automatically receive the same retention policy as lightweight security metadata.

A useful split is:

```text
longer retention:
  identities
  hashes
  policy versions
  revocation epochs
  action metadata
  approvals / declassifications
  external result identifiers

shorter or policy-specific retention:
  raw prompts
  tool payloads
  retrieved documents
  model output
```

Retention periods should come from legal, privacy, incident-response, and business requirements rather than from the agent platform itself.

---

