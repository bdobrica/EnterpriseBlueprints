## 3. Design invariants

The most important platform properties should be stated independently of implementation technology.

| Invariant | Required property |
|---|---|
| Treatment identity | Every variant resolves to an immutable pipeline definition. |
| Historical reproducibility | Published experiment definitions are never silently rewritten. |
| Stable randomization | The same experiment iteration and randomization unit produce the same assignment. |
| Stable assignment identity | One iteration, namespace, subject type, and subject key resolve to one assignment ID independent of the variant returned. Conflicting decisions for that ID are detectable. |
| Serving independence | Experiment assignment requires no synchronous control-plane request. |
| Pre-treatment admission | Eligibility and triggering used for confirmatory analysis are evaluated before treatment-specific behavior can influence them. |
| Explicit exposure | Assignment and actual treatment participation are recorded separately. |
| ITT preservation | Primary online analysis retains the subject's randomized treatment even if execution falls back or deviates. |
| Correct analysis unit | Model calls or spans are not mistaken for independent randomized observations. |
| Workflow stability | Long-running workflows retain their pinned treatment unless policy explicitly permits reevaluation. |
| Trace/experiment separation | Traces describe executions; the experiment ledger establishes experimental bookkeeping. |
| Evaluator provenance | Every automated evaluation identifies the exact evaluator version that produced it. |
| Dataset reproducibility | Offline runs resolve to immutable dataset snapshots and fixtures. |
| Environment reset | Offline trials do not inherit accidental mutable state from previous trials. |
| Evidence durability | Assignment, exposure, and outcomes receive stronger durability guarantees than optional diagnostic telemetry. |
| Evidence before exposure | Assignment is durably spooled before execution, and a new exposure is admitted only when its record can be durably appended at the published dispatch boundary. |
| Event-time correctness | Late, duplicated, corrected, and out-of-order facts are handled without silently changing metric meaning. |
| Statistical health before interpretation | Invalid randomization or evidence quality prevents a result from being represented as decision-grade. |
| Operational/statistical separation | Safety circuit breakers do not masquerade as statistical conclusions. |
| Tenant isolation | Tenant identity is established by trusted platform context, not arbitrary caller-supplied fields. |
| Privacy by policy | Prompt, response, memory, and tool payload capture is configurable rather than implicitly mandatory. |
| Shadow non-interference | Shadow execution cannot mutate production state, contact users, or consume production-only credentials. |

Several of these invariants are difficult to retrofit after historical data exists. Stable identifiers and semantic contracts should therefore precede UI and advanced analytical capabilities.

---
