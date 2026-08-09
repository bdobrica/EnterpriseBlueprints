## 11. Skill packages and supply-chain governance

Skills are reusable instructional or executable artifacts: prompts, procedures, scripts, schemas, templates, and reference material.

They should be managed independently from agents.

```text
skill = reusable content or procedure
agent = governed actor with identity and execution policy
```

OCI registries are a reasonable distribution mechanism because OCI 1.1 explicitly supports artifacts other than container images.[11]

Sigstore/Cosign can provide artifact signatures and verification bundles containing the signing certificate, timestamp, and transparency-log inclusion proof.[12]

A skill manifest might contain:

```yaml
schema_version: v1

metadata:
  name: sre/kubernetes-diagnostics
  version: 2.1.0
  digest: sha256:...

publisher:
  team: sre-platform

capabilities:
  required:
    - kubernetes.pod.read
    - kubernetes.logs.read
  optional:
    - datadog.query

execution:
  sandbox_required: true

provenance:
  sigstore_bundle: bundle.sigstore.json
```

### Capability non-expansion

A skill declaration is a compatibility requirement, not an authorization grant.

```text
run has:
  kubernetes.pod.read

skill requires:
  kubernetes.pod.read
  kubernetes.pod.delete
```

The runtime does not grant `kubernetes.pod.delete` because the skill requested it.

Required capabilities outside the run's authority should normally make the skill ineligible for that run. Optional capabilities may be omitted.

The relevant invariant is:

\[
A_{after\ skill\ load} \subseteq A_{before\ skill\ load}
\]

The same rule applies to subagents.


### Skill restriction policy

A signed skill package may include a machine-readable restriction policy, including a Rego/OPA-compatible bundle if the enterprise chooses that mechanism. Its semantics must be subtractive only.

A useful decision form is:

\[
Allow = PlatformPolicy \land AgentPolicy \land RunDelegation \land SkillRestriction
\]

The skill restriction may narrow where or how the skill is used. It must never be combined as an additive `OR` that permits a skill to grant authority unavailable to the agent or run.

---

