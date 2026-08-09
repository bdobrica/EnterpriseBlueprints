## 1. Goals, assumptions, and non-goals

The platform is designed for an enterprise in which many teams can build or invoke agents and where more than one model provider or harness will be used.

### Goals

The platform should provide:

- a first-class identity for each governed agent;
- preservation of the initiating human or service identity;
- least-privilege delegation bounded by both user and agent policy;
- centralized tool discovery and authorization;
- credentials that are not exposed to prompts, arbitrary harness code, or sandboxes;
- execution that survives laptop shutdowns, process crashes, deployments, and long waits;
- bounded code execution for agents that require shells, browsers, or interpreters;
- signed and attributable agent and skill artifacts;
- explicit controls for subagent delegation;
- human approval for policy-defined sensitive operations;
- reconstructable evidence for consequential side effects.

### Non-goals

The platform should not prescribe:

- a reasoning algorithm;
- a prompt format;
- ReAct versus planning;
- single-agent versus multi-agent orchestration;
- a memory or context-compaction strategy;
- a particular model vendor;
- a single agent framework.

Those decisions belong to the harness implementation and will change more quickly than enterprise security and execution infrastructure.

---

