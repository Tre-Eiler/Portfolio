# Agreement Sync Platform

A system for keeping vendor billing in sync with what's actually deployed,
across a portfolio of managed businesses.

## The problem

Vendor platforms know who's actually using a product. The billing system
knows who's being invoiced for it. Those two views drift apart over time —
a business gets onboarded to a tool but never added to the billing
agreement, an integration only gets partially rolled out, or an agreement
goes stale after an offboarding. Left unchecked, that drift either costs
revenue (used but not billed) or costs trust (billed but not used).

## How the pieces fit together

```mermaid
flowchart LR
    B[Bulk, non-billing onboarding] -->|seeds mappings for| P[Per-vendor sync pattern]
    P -->|each vendor reports status to| R[Reconciliation reporting]
```

## What it does

- **Reconciliation reporting** — aggregates usage/mapping data across
  every vendor integration and every managed business into one recurring
  report, so drift is visible instead of hidden in each vendor's own
  console. See
  [concepts/aggregation-and-reporting.md](./concepts/aggregation-and-reporting.md).
- **A repeatable per-vendor sync pattern** — the same two-mode approach
  (an interactive setup path and a scheduled reconciliation path) is
  reused across every vendor integration rather than building bespoke
  logic per vendor. See
  [concepts/per-vendor-sync-pattern.md](./concepts/per-vendor-sync-pattern.md).
- **Bulk, non-billing onboarding** — lets an operator configure several
  vendor integrations for a business at once without turning billing on
  immediately, so technical setup and the billing decision can happen on
  separate timelines. See
  [concepts/bulk-onboarding.md](./concepts/bulk-onboarding.md).

## Sample artifact

[agreement-automation-overview.sample.html](./agreement-automation-overview.sample.html)
is a sanitized snapshot of the reconciliation report's UI — real report
logic and layout, synthetic business names and identifiers.
