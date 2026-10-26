# ADR 0001 — Separate the live-data path from the rendering path

- **Status:** Accepted
- **Date:** 2023 (reconstructed)
- **Deciders:** CTO, product lead

## Context

A digital twin is only useful if what the operator sees is current. Two very different workloads
sit behind that requirement:

- **Live data** arrives from external systems at heterogeneous rates, with gaps, duplicates,
  late updates and occasional outages. None of that is under our control.
- **Rendering** runs at a fixed frame budget and must never stutter because a source is late.

Binding the 3D client directly to source feeds couples the weakest, most variable part of the
system to the part with the tightest timing requirement. It also means every new data source
becomes a client change.

## Decision

**Separate the two paths, with the twin state model between them.**

- **Ingestion** connects to sources, normalises values into a common model, handles gaps and
  retries, and writes to the **twin state model**.
- **Rendering and query consumers** read from that model only — never from source feeds directly.
- The render path must remain responsive when ingestion degrades; it degrades by showing stale or
  missing values, not by stalling.

## Consequences

**Positive**

- A slow or broken source cannot freeze the 3D client; freshness becomes a displayed property, not
  a hidden coupling.
- New data sources are an ingestion change, not a client change.
- The same state feeds multiple consumers (3D view, API, dashboards) without duplicating logic.

**Negative**

- One more layer to operate: the state model must be stored, versioned and monitored.
- Freshness becomes something the UI has to communicate explicitly (timestamps, staleness states),
  which is design work, not just plumbing.
- Ingestion back-pressure and ordering need real handling; a naive queue will silently accumulate
  stale data.

**Neutral**

- The data path and the render path can now be scaled, tested and released independently.

## Alternatives considered

| Option | Why not chosen |
| --- | --- |
| Bind the 3D client directly to source APIs | Every source change becomes a client release; client stalls on source latency |
| Poll sources from the rendering loop | Latency spikes hit the frame budget and cannot be retried without freezing |
| One central "adapter" per source with no shared model | Handles transport, but each consumer still re-implements meaning — see [ADR 0002](0002-twin-state-model-as-contract.md) |

## Notes

The rule that kept this honest in practice: **freshness is a visible attribute of the state, not
an assumption held by the renderer.**
