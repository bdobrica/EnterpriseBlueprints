## 19. Concurrent experiments, persistent state, and advanced semantics

The initial platform should not require a complete solution to every interaction problem.

The architecture should avoid blocking their later introduction.

### 19.1 Concurrent experiments

Eventually one execution may participate in:

```text
agent experiment
+
memory experiment
+
model experiment
```

The trace should therefore support several simultaneous experiment assignments.

Google's published experimentation infrastructure uses layers and orthogonal diversion to support overlapping experiments while controlling situations that require mutual exclusion.

The MVP can avoid sophisticated overlapping experiments.

Later releases can introduce:

```text
experiment namespaces
mutually exclusive layers
factorial experiments
persistent holdouts
```

### 19.2 Persistent treatment state

Agent treatments may modify:

```text
memory
cache
vector stores
generated files
business records
workflow state
```

That state can survive beyond the original exposure.

For example:

```text
B writes memory
      │
experiment changes
      │
A later reads B-created memory
```

This is a form of treatment carryover.

The MVP documents the risk rather than automatically solving it.

Future versions can introduce a `state_policy` such as:

```text
reads:
  shared | variant_scoped | baseline_snapshot

writes:
  shared | variant_scoped | ephemeral

after_experiment:
  keep | merge | discard
```

### 19.3 Multi-agent pipelines

A trace can record explicit handoffs:

```text
Planner
   │
HANDOFF
   │
Retriever
   │
HANDOFF
   │
Executor
```

This enables failure localization and component diagnostics.

It should not automatically be called causal attribution.

A causal claim about one component generally requires a randomized intervention, controlled ablation, or another valid identification strategy.

### 19.4 Adaptive allocation

Bandit-style assignment can be introduced later.

If allocation probabilities depend on historical observations, the platform must record:

```text
assignment probability
policy version
decision timestamp
```

because the ordinary equal-probability A/B estimator may no longer apply.

---
