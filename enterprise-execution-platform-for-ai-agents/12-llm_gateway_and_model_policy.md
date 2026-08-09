## 12. LLM gateway and model policy

Model-provider access is a separate control plane from tool authorization.

An LLM gateway can enforce:

- approved providers and models;
- data-classification policy;
- region and residency requirements;
- logging and retention rules;
- rate and cost budgets;
- model fallback policy;
- input/output scanning;
- secret and PII detection;
- prompt-injection heuristics.

The request context might include:

```text
agent = incident-investigator
run = run-991823ab
tenant = engineering
data_classification = confidential
content_taint = high
security_epoch = 193
purpose = production-incident
```

The gateway maps that context to an allowed model policy. Content-trust metadata can tighten model, tool-autonomy, or approval policy, but it does not replace the MCP gateway's independent authorization decision.

Guardrails remain defense in depth. A classifier that fails to detect prompt injection must not permit the harness to perform an unauthorized tool action, because the MCP gateway independently evaluates the action.

---

