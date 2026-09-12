## 7. Pipeline identity and treatment reproducibility

The treatment is represented by a complete pipeline definition.

For example:

```yaml
pipeline:
  name: support-agent
  version: 18

components:

  runtime:
    artifact: agent-runtime@sha256:...

  memory:
    artifact: memory-service@sha256:...
    configuration:
      strategy: hybrid
      top_k: 8

  model:
    route: support
    model: provider/model-x
    routing_policy: support-route@sha256:...
    fallback_policy: support-fallback@sha256:...
    parameters:
      temperature: 0.2

  prompt:
    artifact: sha256:...

  tools:
    - artifact: customer-lookup@sha256:...
    - artifact: refund-tool@sha256:...

  retrieval:
    index_snapshot: support-kb@sha256:...
    embedding_model: provider/embed-x

  guardrails:
    artifact: support-policy@sha256:...
```

The manifest also identifies treatment-relevant application code, feature configuration, tool contracts, routing and fallback policy, retrieval corpus or index snapshot, and environment compatibility constraints. It references secret identities and policy versions, never secret values.

### 7.1 Resolved manifests

Experiments should reference a **resolved** pipeline manifest.

If a higher-level definition includes aliases:

```text
support-model-latest
```

publication resolves the alias to the configuration that is intended to be evaluated.

A running experiment must not silently follow an alias to another implementation.

### 7.2 Content addressing

Artifacts should use immutable identifiers where the ecosystem supports them:

```text
container digest
Git commit
prompt digest
tool package digest
configuration digest
dataset snapshot
```

A digest is meaningful only when the serialization and digest algorithm are fixed. The registry therefore defines canonical serialization, includes the algorithm in the identifier, verifies referenced artifacts at publication, and retains or escrows them for the required audit period. Published manifests are signed or otherwise authenticated across trust boundaries.

The system should distinguish:

```text
intended treatment
```

from:

```text
realized runtime provenance
```

This distinction is important for external model providers that may change server-side implementations without publishing a new model identifier.

Consequently, an immutable manifest guarantees reconstructability of the intended treatment, not perfect behavioral replay. Reproducibility is best-effort when a provider does not expose an immutable model revision or deterministic execution mode; the experiment record states that limitation rather than claiming that an exact rerun is possible.

### 7.3 Runtime provenance

Traces should therefore capture runtime evidence where available:

```text
provider
model identifier
provider request ID
region
gateway route
fallback
runtime component version
retrieval index ID
tool implementation version
```

The Pipeline Manifest answers:

```text
What was B supposed to execute?
```

Runtime provenance answers:

```text
What actually executed?
```

Both are necessary.

---
