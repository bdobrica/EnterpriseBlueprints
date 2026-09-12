## 16. Datasets and the production feedback loop

Evaluation datasets should evolve from production evidence without becoming uncontrolled collections of failures.

Potential sources include:

```text
thumbs-down traces
human escalations
tool failures
low automated scores
support tickets
high-cost trajectories
random production samples
new product requirements
reported incidents
```

### 16.1 Dataset provenance

Each scenario records:

```text
source
collection date
sampling method
author
labels
privacy classification
difficulty/tags
source population and inclusion rule
lineage and deduplication group
consent or other governing data basis
```

A dataset snapshot records its complete scenario membership.

### 16.2 Sampling bias

A dataset containing only user complaints does not represent normal production traffic.

Datasets should therefore distinguish:

```text
regression cases
failure cases
representative samples
adversarial cases
capability tests
```

Evaluation reports should expose dataset composition and results by declared slice. When a snapshot is intended to represent production, the sampling weights or population mismatch must also be reported.

### 16.3 Development and release sets

To reduce evaluation overfitting, organizations can maintain:

```text
development dataset
calibration dataset
held-out release dataset
```

Access to held-out sets can be restricted, with labels hidden from candidate authors and access logged. Split assignment occurs by deduplication group so the same user, conversation, incident, or near-duplicate does not cross development, calibration, and release sets. Published snapshots and split membership are immutable.

### 16.4 Production loop

The intended lifecycle is:

```text
production
    │
    ▼
interesting traces / outcomes
    │
    ▼
scenario authoring
    │
    ▼
dataset snapshot
    │
    ▼
offline paired comparison
    │
    ▼
shadow candidate
    │
    ▼
production experiment
    │
    ▼
new production evidence
```

Offline evidence does not replace the production experiment.

It reduces the set of candidates that need production exposure.

---
