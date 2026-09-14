# Reconciliation reporting

**The idea:** every vendor integration, for every managed business,
independently reports what it sees — whether the integration is installed,
whether it's healthy, and how much of what it tracks (companies, seats,
licenses) isn't mapped to something billable. A separate process
periodically collects all of those independent reports, rolls them into
one view, and publishes it both as a live report and as a recurring
digest.

## Why collect independently and aggregate separately

Each vendor integration has different data, different failure modes, and a
different install footprint across the portfolio — some businesses use a
given vendor, some don't. Keeping each integration's reporting logic
self-contained means one vendor's data quirks or outages don't block the
others, and the aggregation step doesn't need vendor-specific knowledge —
it just collects whatever's been reported and renders it uniformly.

## What the aggregation step does

1. Collects every business's latest self-reported status across every
   vendor integration.
2. Normalizes it into a common shape, even though different integrations
   report at different levels of resolution (e.g. one vendor's offering
   split into multiple sub-products under one umbrella).
3. Renders one report: portfolio-wide totals, a per-business breakdown,
   and a way to filter down to just the gaps that need a human look.
4. Publishes it two ways — a live page an operator can browse anytime, and
   a periodic digest, so a finding reaches someone even if nobody opens
   the page.

## Design principles

- **Tolerant of shape drift.** The rendering layer accepts several
  reasonable input shapes and field-name variants, rather than requiring
  the upstream data producers and the report to change in lockstep.
- **Everything on the page is derived, not hardcoded** — totals, rankings,
  and filter counts are all computed from whatever data shows up, so
  adding or removing a business or a vendor needs no template change.
- **Unmapped is a prompt to verify, not a verdict.** The report is explicit
  that "not automatically reconciled" isn't the same as "not billed" —
  some things are legitimately handled manually.
