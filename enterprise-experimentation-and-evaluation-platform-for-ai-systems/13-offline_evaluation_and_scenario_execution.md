## 14. Offline evaluation and scenario execution

Offline evaluation runs pipeline variants against controlled scenarios.

A scenario contains more than a prompt.

Example:

```yaml
scenario:
  id: duplicate-charge-001

input:
  message: >
    I was charged twice for order 9811.

environment:
  fixture: duplicate-charge-customer@v3

expected:
  refund_count: 1
  ticket_status: resolved

limits:
  max_turns: 15
  max_tool_calls: 20
  max_cost: 0.50
```

### 14.1 Dataset snapshots

A dataset consists of versioned scenario definitions.

Every experiment resolves to:

```text
dataset_snapshot_id
```

so later edits do not change historical results.

### 14.2 Resettable environments

Each trial starts from known state.

The initial platform should use:

```text
fixture-backed services
test databases
containerized dependencies
or equivalent resettable sandboxes
```

The environment is reset between trials.

This is particularly important for stateful agents. Anthropic's current guidance for agent evaluation similarly emphasizes controlled environments, tasks, repeated trials, trajectories, and graders.

### 14.3 Repeated trials

Because agent behavior is stochastic:

```text
scenario × pipeline
```

may be executed several times.

For example:

```text
A: 5 trials
B: 5 trials
```

The trial ID and repetition number are retained.

### 14.4 Paired scenarios

A and B should normally execute against the same scenario snapshot.

That prevents accidental dataset-composition differences from being confused with treatment differences.

### 14.5 Replay

General multi-turn replay is not a first-release requirement.

Simple trace replay is valid only while the candidate requests interactions present in the recorded trajectory.

When the candidate branches:

```text
REPLAY_DIVERGED
```

is the correct result.

The system should not invent tool responses and continue calling that execution a replay.

Stateful branching evaluation belongs in resettable environments.

Future implementations may introduce snapshot-and-fork environment virtualization without changing the scenario and trial model.

---
