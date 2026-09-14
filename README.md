# Portfolio

Selected automation and integration work — described conceptually.

**About these write-ups:** this repo intentionally describes systems at
the level of *what problem they solve and how they're architected*, not as
literal build documentation. Internal identifiers, specific field/variable
names, exact workflow steps, and vendor record numbers are omitted or
generalized on purpose. The goal is to show the thinking and design
patterns behind the work, not to reproduce operating procedures for the
underlying systems.

## Rewst automations

- [Agreement Sync Platform](rewst-automations/agreement-sync-platform) — a
  system for keeping vendor billing in sync with what's actually deployed,
  across a portfolio of managed businesses.
  - [Reconciliation reporting](rewst-automations/agreement-sync-platform/concepts/aggregation-and-reporting.md)
  - [Per-vendor sync pattern](rewst-automations/agreement-sync-platform/concepts/per-vendor-sync-pattern.md)
  - [Bulk, non-billing onboarding](rewst-automations/agreement-sync-platform/concepts/bulk-onboarding.md)
- [NOC Ticket Routing](rewst-automations/noc-ticket-routing) — a recurring
  automation that triages helpdesk tickets across a portfolio of managed
  businesses by priority and technician activity, instead of a human
  scanning every ticket by hand.
  - [Config-driven, multi-org setup](rewst-automations/noc-ticket-routing/concepts/config-driven-multi-org-setup.md)
  - [Keyword-based priority routing](rewst-automations/noc-ticket-routing/concepts/keyword-priority-routing.md)
  - [Human-activity detection](rewst-automations/noc-ticket-routing/concepts/human-activity-detection.md)
- [Workforce-to-Directory Identity Sync](rewst-automations/workforce-directory-sync) —
  a recurring job that keeps directory user attributes in sync with a
  workforce management system's employee roster.
  - [HR-driven identity sync](rewst-automations/workforce-directory-sync/concepts/hr-driven-identity-sync.md)
  - [Ticketed batch run](rewst-automations/workforce-directory-sync/concepts/ticketed-batch-run.md)
