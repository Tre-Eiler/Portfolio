# M365 User Sync

The most multi-purpose of the vendor syncs: one workflow that acts as a
router across four different jobs, depending on which context variables are
populated when it's invoked — first-time org setup, ad hoc user billing
changes, a read-model for an App Builder page, and a portfolio-wide
scheduled sync.

## What it does

Maps M365/Entra ID users to PSA billing products via a custom directory
schema extension attached directly to each user's Azure AD account, storing
which ConnectWise products that user should be billed for. This avoids
needing a separate database to track user-to-product mappings — the
mapping lives on the identity itself.

## The four branches

Routed from a single decision node based on which inputs are present:

1. **First-time org setup** (an agreement ID is provided) — captures the
   relevant setup inputs into a JSON blob (filtering out internal/noise
   context keys), writes it to the org's M365-to-PSA mapping variable, then
   runs an initial sync (autobilling controlled by a setup flag, no
   confirmation email sent).
2. **Ad hoc user modification** (a list of users to modify is provided) —
   sends a "changes in progress" branded email, sets each affected user's
   directory extension attribute (billable flag + product mapping) for
   every selected user (concurrency 5), runs a sync with emails suppressed,
   then sends a "changes complete" branded email and logs the submission.
3. **App Builder read-model** (an org ID is provided, no modification
   requested) — pulls every M365 user in the tenant along with their
   product-mapping extension data, looks up each referenced PSA addition's
   invoice description, and joins the two into a flat table (user →
   invoice descriptions) for a page that shows what a person is billed for
   and why.
4. **Portfolio-wide scheduled sync** (none of the above match) — lists
   every managed org and fans out a sync run to each one (one org at a
   time), with emails enabled so each MSP is notified when their sync
   completes.

## Design notes

- **One workflow, four entry conditions**, rather than four separate
  workflows — the routing node inspects which fields are populated and
  picks a branch, so a single "sync users" trigger endpoint serves setup,
  manual changes, reporting, and cron.
- **Billing metadata lives on the identity, not a side table.** Storing the
  product mapping as a custom Azure AD user extension means it travels with
  the user object itself and is queryable directly from Graph, rather than
  needing to join against an external store.
- **User-facing branded emails bracket manual changes** — before/after
  emails (a shared HTML template with company branding and a support
  address swapped in) give the requester visibility into an otherwise
  asynchronous, fanned-out operation, and point them at the Rewst execution
  log if something looks wrong.
- **One branch is a placeholder.** A "single user via table selection"
  trigger condition exists in the routing logic, but its action list is
  empty — that path isn't implemented yet.
