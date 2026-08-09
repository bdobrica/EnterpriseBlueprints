## 13. Human approval as an action-bound authorization grant

High-risk operations may require a human approval obligation.

A common mistake is to resume the run using the approver's fresh OAuth token. That changes the principal context of the whole run and can unintentionally increase the authority available to subsequent actions.

Approval should instead be specific to the pending operation.

```text
pending action:
  run = run-991823ab
  tool = kubernetes.deployment.restart
  resource = payments/prod/api
  args_hash = sha256:...
```

The approval service authenticates the human approver and produces a short-lived approval grant bound to those fields.

```json
{
  "run_id": "run-991823ab",
  "tool_call_id": "call_3821",
  "action_hash": "sha256:...",
  "approver": "alice@corp.example",
  "decision": "approve",
  "expires_at": "2026-08-08T13:15:00Z"
}
```

Execution resumes only if:

1. the original run delegation is still valid;
2. current policy still permits the action subject to approval;
3. the approver is currently authorized to approve it;
4. the action and arguments still match the approved hash;
5. the approval has not expired or been consumed;
6. applicable revocation and authorization-freshness requirements are satisfied.

Changing the target resource or arguments invalidates the approval.

### 13.1 Approval operations at scale

Action binding does not require every low-level operation to create a poor human experience. Policy can define constrained approval grants that remain narrower than session-wide authority.

For example:

```yaml
approval_grant:
  agent: github-pr-reviewer
  run: run-991823ab
  tool: github.pull.comment
  repository: engineering/payments
  max_uses: 10
  expires_in: 15m
  argument_constraints:
    pull_request: 842
```

Such a grant is still an attenuation object. It does not replace the run's principal token or authorize unrelated actions.

The approval service should also support policy-defined:

- backup approver groups;
- separation-of-duty rules;
- escalation on timeout;
- explicit approval deadlines;
- clear resource and argument diffs;
- bounded batch approval where risk policy permits it.

Timeout should default to fail closed for operations that require approval.

---

