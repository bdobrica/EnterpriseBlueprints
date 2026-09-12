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
    parameters:
      temperature: 0.2

  prompt:
    artifact: sha256:...

  tools:
    - artifact: customer-lookup@sha256:...
    - artifact: refund-tool@sha256:...

  guardrails:
    artifact: support-policy@sha256:...
```

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

The system should distinguish:

```text
intended treatment
```

from:

```text
realized runtime provenance
```

This distinction is important for external model providers that may change server-side implementations without publishing a new model identifier.

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
