# ADR 0003 — Build the first version around one concrete customer

- **Status:** Accepted
- **Date:** 2023 (reconstructed)
- **Deciders:** CTO, product lead, business owner

## Context

The company entered digital twins as a new product line: there was no existing code, no settled
requirements and a team that still had to be hired. The temptation was to build a general platform
that could serve any future customer — which is attractive on a slide and expensive in practice.

A 0 → 1 product needs three things an abstract platform cannot provide on its own: a real customer
to validate requirements against, a shipped version to build credibility on, and feedback fast
enough to change direction while it is still cheap.

## Decision

**Ship the first version for one concrete customer, and generalise only when a second real use
case demands it.**

- Scope the first release around one deployment's actual data sources, views and workflows.
- Keep architecture decisions extension-friendly (see [ADR 0001](0001-data-path-vs-rendering-path.md)
  and [ADR 0002](0002-twin-state-model-as-contract.md)), but do **not** build configuration or
  abstraction for customers that do not exist yet.
- Re-evaluate the platform boundary when a second real deployment introduces a genuinely different
  requirement — not before.

## Consequences

**Positive**

- A real version shipped early, which made the product credible and the requirements concrete.
- Effort went into what the customer actually needed instead of a speculative feature matrix.
- Feedback arrived while the architecture was still cheap to change.

**Negative**

- The first version risks overfitting to one deployment's peculiarities; some of it gets reworked
  when the second customer arrives.
- Saying "no" to generic features takes discipline, especially when the request sounds reasonable.
- Growth pressure arrives later and faster: the second customer reveals the platform work that was
  deliberately deferred.

**Neutral**

- The delivery plan favours a narrow shipped version over a broad unshipped one — a trade-off the
  business side accepted explicitly.

## Alternatives considered

| Option | Why not chosen |
| --- | --- |
| Build the generic platform first | No customer to validate against; high risk of building the wrong abstractions |
| Run several customer pilots in parallel | Team size at 0 → 1 could not support multiple fronts without diluting quality |
| Buy and integrate an existing platform | Did not fit the product direction or the company's reason for entering the market |

## Notes

The discipline this required: **every generic feature needed a named customer and a real use case
behind it, or it waited.** That is also the simplest test to apply to the next round of requests.
