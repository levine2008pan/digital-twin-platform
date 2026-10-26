# ADR 0002 — The twin state model is the contract

- **Status:** Accepted
- **Date:** 2023 (reconstructed)
- **Deciders:** CTO, product lead

## Context

The product has several consumers of the same reality: the 3D client, the query API, operator
dashboards, and external systems that integrate with the twin. If each one reads raw source feeds
and interprets them itself, "what the current value is" gets re-implemented per consumer, and the
interpretations drift.

The twin also has to answer a question raw feeds cannot: **what was the state before** — operators
compare now with earlier, and that comparison is a core part of the product, not a reporting
afterthought.

## Decision

**The twin state model is the single contract.** It holds current values and their time dimension
(history), and every consumer reads from it:

- ingestion owns writes to the model;
- the 3D client, API and dashboards read from it;
- schema changes are versioned and treated as contract changes, not incidental refactors.

## Consequences

**Positive**

- Every consumer sees one consistent definition of "current value" and of "value over time".
- History is available by construction, so comparison and playback do not need a separate system.
- Consumers can be built or replaced without touching ingestion.

**Negative**

- The schema becomes a coordination point: changes need migration and consumer review.
- The model must be right enough up front; a naive "flat values" schema becomes expensive to
  evolve once consumers depend on it.
- Someone must own the contract explicitly — it cannot be a byproduct of whoever wrote the last
  importer.

**Neutral**

- New consumers (mobile, reports, integrations) become cheap; new sources must map into the same
  semantics, which is the desired pressure.

## Alternatives considered

| Option | Why not chosen |
| --- | --- |
| Pass raw feeds to consumers | Fastest first demo, but every consumer re-implements meaning and drifts |
| Per-consumer projections built ad hoc | Works until the second consumer, then definitions multiply |
| Event log only, no current-state model | Excellent for history, but every read becomes a replay — unacceptable for real-time views |

## Notes

The rule that made this sustainable: **ingestion writes, everyone else reads.** No consumer is
allowed to correct or reinterpret state on its way to the screen.
