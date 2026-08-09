# Designing an Enterprise Execution Platform for AI Agents

**Current version:** 0.5.0

This document describes a vendor-neutral architecture for governing, executing, and auditing enterprise AI agents. It is organized into chapter files for straightforward navigation and machine parsing.

## Table of contents

1. [Executive summary](00-executive_summary.md)
2. [Goals, assumptions, and non-goals](01-goals_assumptions_and_non_goals.md)
3. [Reference architecture and separation of concerns](02-reference_architecture_and_separation_of_concerns.md)
4. [Threat model and security invariants](03-threat_model_and_security_invariants.md)
5. [Identity and delegation](04-identity_and_delegation.md)
6. [MCP gateway and enterprise tool plane](05-mcp_gateway_and_enterprise_tool_plane.md)
7. [The agent governance plane](06-the_agent_governance_plane.md)
8. [Durable execution: Kubernetes plus a workflow engine](07-durable_execution_kubernetes_plus_a_workflow_engine.md)
9. [External side effects, idempotency, and the Side-Effect Ledger](08-external_side_effects_idempotency_and_the_side_effect_ledger.md)
10. [Sandbox isolation for generated code](09-sandbox_isolation_for_generated_code.md)
11. [Content lineage, taint, and memory governance](10-content_lineage_taint_and_memory_governance.md)
12. [Skill packages and supply-chain governance](11-skill_packages_and_supply_chain_governance.md)
13. [LLM gateway and model policy](12-llm_gateway_and_model_policy.md)
14. [Human approval as an action-bound authorization grant](13-human_approval_as_an_action_bound_authorization_grant.md)
15. [Independent evidence and audit architecture](14-independent_evidence_and_audit_architecture.md)
16. [Subagent delegation](15-subagent_delegation.md)
17. [Failure semantics and availability decisions](16-failure_semantics_and_availability_decisions.md)
18. [Architectural trade-offs](17-architectural_trade_offs.md)
19. [What should be standardized internally](18-what_should_be_standardized_internally.md)
20. [A practical implementation path](19-a_practical_implementation_path.md)
21. [Conclusion](20-conclusion.md)
22. [References](21-references.md)

## Versioning

The document uses semantic versioning and starts at `0.5.0`, representing the fifth minor revision. To increment the revision (patch version), run this command from this directory:

```bash
bump2version patch
```

Use `bump2version minor` or `bump2version major` when the scope of a future change warrants it. The bump configuration updates the version in this README, the executive summary, and `.bumpversion.cfg`.
