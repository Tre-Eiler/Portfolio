# Backend workflow: aggregate, render, publish, email

This describes the Rewst workflow that produces the [Agreement Automation
Overview](./agreement-automation-overview.sample.html) page. The workflow
itself isn't checked in (Rewst's export is a large JSON blob of node
positions, internal action-pack IDs, etc. that isn't meaningful outside a
Rewst tenant) — this is a plain-language description of what it does and how
the nodes connect.

## Context

Courser (the parent org) manages a portfolio of separate MSP businesses.
Each MSP has its own set of vendor integrations installed in its Rewst
sub-org — RMM, SentinelOne, Duo, Breach Secure Now, Axcient Cloud, Axcient
Recover, etc. A separate per-vendor sync/audit workflow (out of scope here)
runs per MSP and writes a JSON blob to a well-known org variable name
(`agreement_sync_audit_json`) summarizing, per integration: whether it's
installed, whether it's failing, and how many companies/seats it sees that
aren't mapped to a billed PSA agreement addition.

This workflow's job is to collect all of those per-MSP blobs, roll them up
into one report, and publish that report two ways: as a live page in an App
Builder app, and as a weekly email digest.

## Nodes

**1. Trigger (`core_noop`)**
Fires on two paths:
- Whenever an upstream per-MSP sync/audit workflow finishes successfully →
  continues to node 2 to refresh the report.
- On a weekly schedule (Monday, matched via a cron-string comparison) →
  branches straight to the email step (node 5) using the most recently
  rendered report.

**2. `rewst_list_organization_variables`**
Queries organization variables across every managed sub-org, filtered to
just the `agreement_sync_audit_json` variable name (one internal/test org is
explicitly excluded by ID). The result is reshaped inline into a
`geo_results` array — one entry per MSP — of `{ org_id, org_name, json:
<parsed audit blob> }`.

**3. `rewst_bulk_upsert_agreement_html`**
Renders the Agreement Automation Overview HTML template (Jinja) with
`CTX.geo_results` serialized via `tojson` into a `window.REPORT_DATA`
assignment at the top of the page, then upserts the rendered HTML string
back into an org variable (`agreement_sync_overview_html`). This step is the
one that actually contains the HTML/CSS/JS shown in this folder.

**4. `rewst_get_organization_variable`**
Reads the freshly rendered `agreement_sync_overview_html` variable back out,
scoped to the hub org, so the App Builder page can serve it live without
re-rendering on every view.

**5. `microsoft_graph_send_mail_as_impersonated_user`**
On the weekly-schedule branch, sends the same rendered HTML as the body of
an email via Microsoft Graph to the ops recipient — same page, same data,
just delivered instead of browsed to.

## Design notes worth calling out

- **Shape tolerance in the render layer.** The JS in the page
  (`unwrapPayload`, `flattenEntry`, `normalizeGeo`) accepts several possible
  input shapes — a `{ json_objects: [...] }` envelope, a bare list, a single
  object, or a dict keyed by org ID — and multiple aliases per field. That
  means the upstream data-collection workflow can evolve (rename a field,
  change envelope shape) without needing a matching change to the rendered
  template.
- **Product-to-integration matching by slug prefix.** `axcient_cloud` and
  `axcient_recover` both roll up under the `axcient` integration's
  installed/failed state, matched by longest common slug prefix — so a
  vendor with multiple product lines doesn't need its own installed/failed
  flag duplicated per product.
- **Everything downstream of the data is derived, not hardcoded.** Card
  count, ranking (by unmapped seats, then unmapped companies), portfolio
  totals, and filter counts are all computed from whatever `REPORT_DATA`
  contains — no per-MSP-count assumptions baked into the template.
- **The CSV export mirrors the current on-screen filter**, not the full
  dataset, unless the viewer explicitly asks for "All rows."
