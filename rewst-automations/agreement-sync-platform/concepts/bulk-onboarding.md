# Bulk, non-billing onboarding

**The idea:** let an operator configure several vendor integrations for a
business in one pass — creating the billing-side placeholders each
integration's sync pattern needs — without turning billing on yet. The
integrations start syncing on their normal schedule immediately; whether
and when to actually bill is a separate decision made later.

## Why separate "configured" from "billing"

Onboarding a business's integrations and deciding to start charging for
them are different decisions, often made by different people on different
timelines — a technician can finish technical setup the same day a
customer signs up, without waiting on a pricing or contract conversation.
Coupling the two would either delay technical setup unnecessarily or force
billing to start before it should.

## How it works, conceptually

1. An operator selects which of the supported vendor integrations to set
   up for a business in one submission, rather than running each vendor's
   setup separately.
2. For each selected vendor, the appropriate billing-side placeholder is
   created at zero quantity and explicitly marked non-billable — present
   in the billing system, structurally correct, but generating no charge.
3. Each vendor's normal [sync pattern](./per-vendor-sync-pattern.md) then
   picks up the mapping on its regular schedule, the same as if it had
   been set up individually — the only difference is the placeholder
   started at zero/non-billable instead of immediately reflecting real
   usage.
4. One tracking ticket covers the whole multi-vendor submission, with work
   logged per vendor as each one finishes, so a single onboarding action
   stays auditable per integration.
5. Turning on billing later is a deliberate, separate step — not something
   this process does automatically.

## Design principles

- **Setup and the billing decision are decoupled** — a business can be
  fully wired up technically well before (or well after) a decision to
  bill for it.
- **The non-billing state is structural, not a note someone has to
  remember.** The billing system itself is told the item isn't billable,
  rather than relying on a person to remember not to invoice it.
- **Reuses the same per-vendor sync pattern** — bulk onboarding doesn't
  introduce a second way of reconciling a vendor; it just seeds the
  mapping the regular sync already knows how to pick up.
