## 26. References

1. Ron Kohavi, Diane Tang, Ya Xu. *Trustworthy Online Controlled Experiments: A Practical Guide to A/B Testing*. Cambridge University Press, 2020.

2. Somit Gupta, Liudmila Ulanova, Sumit Bhardwaj, Pavel Dmitriev, Paul Raff, Aleksander Fabijan. *The Anatomy of a Large-Scale Experimentation Platform*. IEEE ICSA, 2018. The paper describes experimentation portal, execution, log processing, and analysis as distinct platform concerns.

3. Alexander Ivaniuk, Weitao Duan. *A/B testing at LinkedIn: Assigning variants at scale*. LinkedIn Engineering, 2020. Relevant to deterministic hashing, local assignment, allocation correctness, and experiment independence.

4. Diane Tang, Ashish Agarwal, Deirdre O'Brien, Mike Meyer. *Overlapping Experiment Infrastructure: More, Better, Faster Experimentation*. KDD, 2010. Relevant to experiment layers and overlapping experimentation.

5. Aleksander Fabijan et al. *Diagnosing Sample Ratio Mismatch in Online Controlled Experiments: A Taxonomy and Rules of Thumb for Practitioners*. KDD, 2019. Relevant to SRM as an experiment trustworthiness and data-quality check.

6. Microsoft Experimentation Platform. *Alerting in Microsoft's Experimentation Platform*. Relevant to operational SRM monitoring and experiment safety.

7. Alex Deng, Ya Xu, Ron Kohavi, Toby Walker. *Improving the Sensitivity of Online Controlled Experiments by Utilizing Pre-Experiment Data*. WSDM, 2013. Introduces CUPED variance reduction using pre-experiment covariates.

8. Anthropic. *Demystifying evals for AI agents*. 2026. Relevant to tasks, trials, trajectories, environments, deterministic graders, model-based graders, and evaluation of stochastic agents.

9. OpenTelemetry. *Semantic Conventions*. Relevant to vendor-neutral tracing and common telemetry semantics.

10. OpenTelemetry. *Baggage*. Relevant to contextual propagation and its security and integrity limitations.

11. Cloud Native Computing Foundation. *CloudEvents*. Relevant to portable event-envelope semantics for the experiment event ledger.

12. Statsig. *Online Evals*. Current AI experimentation documentation describing production evaluation and shadow candidate execution.

13. Microsoft Experimentation Platform. *Patterns of Trustworthy Experimentation: During-Experiment Stage*. Relevant to SRM checks, monitoring, and auto-shutdown of harmful treatments.

14. Microsoft Experimentation Platform. *Experimentation Platform Publications*. Collection covering controlled rollout, metric design, variance reduction, trustworthy analysis, and experimentation at scale.

15. OpenTelemetry. *Signals*. Background on traces, metrics, logs, baggage, and telemetry signal separation.
