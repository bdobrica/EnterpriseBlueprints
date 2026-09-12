# Designing an Enterprise Experimentation and Evaluation Platform for AI Systems

**Current version:** 0.1.1

This document describes a vendor-neutral architecture for evaluating changes to AI systems through offline evaluation, shadow execution, and online controlled experimentation. The platform treats models, prompts, retrieval systems, memory, tools, agent runtimes, policies, and complete agent pipelines as versioned treatments that can be evaluated using a common evidence model.

The initial implementation intentionally supports a narrower set of capabilities than the complete architecture described here. The architecture preserves extension points for more advanced experimentation and evaluation methods without requiring those capabilities in the first release.

The document is organized into chapter files for straightforward navigation and machine parsing.

## Table of contents

1. [Executive summary](00-executive_summary.md)
2. [Goals, assumptions, and non-goals](01-goals_assumptions_and_non_goals.md)
3. [Design invariants](02-design_invariants.md)
4. [Domain model and identities](03-domain_model_and_identities.md)
5. [Reference architecture and separation of concerns](04-reference_architecture_and_separation_of_concerns.md)
6. [Experiment lifecycle and immutable configuration](05-experiment_lifecycle_and_immutable_configuration.md)
7. [Pipeline identity and treatment reproducibility](06-pipeline_identity_and_treatment_reproducibility.md)
8. [Randomization, local assignment, and workflow pinning](07-randomization_local_assignment_and_workflow_pinning.md)
9. [Exposure, realized treatment, and intention-to-treat](08-exposure_realized_treatment_and_intention_to_treat.md)
10. [Experiment context and distributed tracing](09-experiment_context_and_distributed_tracing.md)
11. [Experiment event ledger and outcome attribution](10-experiment_event_ledger_and_outcome_attribution.md)
12. [Online controlled experiment execution](11-online_controlled_experiment_execution.md)
13. [Shadow execution and rollout safety](12-shadow_execution_and_rollout_safety.md)
14. [Offline evaluation and scenario execution](13-offline_evaluation_and_scenario_execution.md)
15. [Evaluator architecture and calibration](14-evaluator_architecture_and_calibration.md)
16. [Datasets and the production feedback loop](15-datasets_and_the_production_feedback_loop.md)
17. [Metrics, guardrails, and operational budgets](16-metrics_guardrails_and_operational_budgets.md)
18. [Statistical analysis and experiment health](17-statistical_analysis_and_experiment_health.md)
19. [Concurrent experiments, persistent state, and advanced semantics](18-concurrent_experiments_persistent_state_and_advanced_semantics.md)
20. [Data architecture and telemetry retention](19-data_architecture_and_telemetry_retention.md)
21. [Scale, performance, and capacity](20-scale_performance_and_capacity.md)
22. [Multi-tenancy, security, privacy, and governance](21-multi_tenancy_security_privacy_and_governance.md)
23. [Failure semantics and availability decisions](22-failure_semantics_and_availability_decisions.md)
24. [APIs, SDKs, interoperability, and internal standards](23-apis_sdks_interoperability_and_internal_standards.md)
25. [Practical implementation path and conclusion](24-practical_implementation_path_and_conclusion.md)
26. [References](25-references.md)

## Versioning

The document uses semantic versioning and starts at `0.1.0`. To increment the revision (patch version), run this command from this directory:

```bash
bump2version patch
```

Use `bump2version minor` or `bump2version major` when the scope of a future change warrants it. The bump configuration updates the version in this README, the executive summary, and `.bumpversion.cfg`.
