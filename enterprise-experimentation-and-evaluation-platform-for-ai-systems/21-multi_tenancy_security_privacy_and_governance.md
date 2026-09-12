## 22. Multi-tenancy, security, privacy, and governance

Every governed resource belongs to:

```text
organization
project/workspace
environment
```

That context applies to:

```text
experiments
pipelines
datasets
evaluators
metrics
traces
events
analysis results
credentials
```

### 22.1 Identity

Enterprise deployments should support:

```text
OIDC
SAML
service identities
API credentials
SCIM where required
```

### 22.2 Authorization

Typical roles include:

```text
viewer
analyst
experiment author
experiment approver
dataset curator
evaluator administrator
platform administrator
```

High-risk actions may require separate permissions:

```text
publish experiment
increase production exposure
override circuit breaker
access raw prompts
export dataset
change retention
```

### 22.3 Audit

The audit log records:

```text
experiment publication
pipeline changes
start/pause/stop
traffic ramps
metric changes
evaluator changes
dataset publication
manual decision
access to sensitive artifacts
```

### 22.4 Payload privacy

Prompt and response capture should be independently configurable.

Possible policy:

```text
metadata only
redacted content
full content
no content
```

The same applies to:

```text
memory
retrieval documents
tool arguments
tool outputs
```

OpenTelemetry guidance treats verbose or sensitive fields as information that should not automatically be required telemetry.

### 22.5 Evaluator data policy

Model-based evaluators may send user content to another model provider.

Evaluation policy must therefore specify which:

```text
data classifications
providers
regions
models
```

are permitted.

### 22.6 Encryption

The platform should support:

```text
TLS in transit
encryption at rest
KMS-backed keys
tenant-scoped encryption domains where required
```

Content-addressed payload IDs should not accidentally provide cross-tenant equality oracles.

### 22.7 Data deletion

The architecture should allow deletion of sensitive payloads while retaining non-identifying aggregate experiment results when policy permits.

---
