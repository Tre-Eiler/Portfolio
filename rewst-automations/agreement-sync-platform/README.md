# Agreement Sync Platform

A cross-vendor billing-reconciliation system built in Rewst for an MSP
portfolio. It answers one question per managed company, per vendor product:
*"Is this company on a billed PSA agreement addition for something it's
actually using?"*

## Why

Each MSP in the portfolio has some subset of vendor integrations installed
(RMM, SentinelOne, Duo, Breach Secure Now, Axcient Cloud, Axcient Recover).
Vendor platforms know who's actually using the product; the PSA knows who's
being billed for it. Those two lists drift apart over time — a company gets
onboarded to a tool but never added to the billing agreement, or an
agreement goes stale after an offboarding. This system surfaces that drift
without claiming to resolve it: unmapped doesn't necessarily mean unbilled,
since some companies are still invoiced manually.

## Components

- **Per-vendor sync/audit workflows** (one per integration, per MSP) —
  compare vendor-side company/seat counts against PSA agreement mappings and
  write the result to a per-MSP org variable.
- **Aggregation + render + publish workflow** — collects every MSP's audit
  blob, renders one HTML report, and publishes it both as a live App
  Builder page and a weekly email digest. See
  [workflow-overview.md](./workflow-overview.md) for the node-by-node
  breakdown.
- **App Builder landing page** — the report itself: a portfolio summary,
  per-MSP cards with an installed/unmapped/not-installed breakdown, and a
  CSV export that mirrors whatever filter is on screen. See
  [agreement-automation-overview.sample.html](./agreement-automation-overview.sample.html)
  for a sanitized snapshot (client names and org IDs replaced with
  `MSP-01`..`MSP-17` placeholders; the underlying report logic and layout
  are unmodified from production).
- **Setup forms** — per-MSP configuration for which vendor integrations are
  in scope and how their companies map to PSA agreements. Not yet
  documented here; a future addition.

## Notes on the sample file

`agreement-automation-overview.sample.html` is a real rendered output of the
platform with client-identifying data scrubbed: MSP names and org IDs are
synthetic, and the two links back to the live Courser tenant/hosted logo
were replaced with placeholders. Everything else — the stats, the
per-product gap numbers, the filtering/export behavior — is unchanged and
fully functional if you open the file in a browser.
