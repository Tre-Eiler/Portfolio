# Portfolio

Selected automation and integration work — described conceptually.

**About these write-ups:** this repo intentionally describes systems at
the level of *what problem they solve and how they're architected*, not as
literal build documentation. Internal identifiers, specific field/variable
names, exact workflow steps, and vendor record numbers are omitted or
generalized on purpose. The goal is to show the thinking and design
patterns behind the work, not to reproduce operating procedures for the
underlying systems.

Organized below by what each automation does, not by the tool it's built
in — several of these happen to be built in Rewst, but that's an
implementation detail, not the organizing principle.

These concepts live across the broader MSP tool stack — ConnectWise RMM,
Rewst, ConnectWise PSA, IT Glue, ImmyBot, SentinelOne, and many others
common in managed services — orchestrated together rather than confined
to any single platform.

## Billing & reconciliation

- [Agreement Sync Platform](billing-and-reconciliation/agreement-sync-platform) —
  a system for keeping vendor billing in sync with what's actually
  deployed, across a portfolio of managed businesses.
  - [Reconciliation reporting](billing-and-reconciliation/agreement-sync-platform/concepts/aggregation-and-reporting.md)
  - [Per-vendor sync pattern](billing-and-reconciliation/agreement-sync-platform/concepts/per-vendor-sync-pattern.md)
  - [Bulk, non-billing onboarding](billing-and-reconciliation/agreement-sync-platform/concepts/bulk-onboarding.md)
- [Usage-Based Billing Sync](billing-and-reconciliation/usage-based-billing-sync) —
  a recurring automation that turns a cloud provider's consumption data
  into a single accurate billing charge each period, distinct from the
  seat/license reconciliation above.
  - [Period-based idempotent sync](billing-and-reconciliation/usage-based-billing-sync/concepts/period-based-idempotent-sync.md)
  - [Exclusion list filtering](billing-and-reconciliation/usage-based-billing-sync/concepts/exclusion-list-filtering.md)
  - [Configurable cost-to-price translation](billing-and-reconciliation/usage-based-billing-sync/concepts/cost-to-price-translation.md)
  - [Traceable single-line billing](billing-and-reconciliation/usage-based-billing-sync/concepts/traceable-single-line-billing.md)

## Identity management

- [Workforce-to-Directory Identity Sync](identity-management/workforce-directory-sync) —
  a recurring job that keeps directory user attributes in sync with a
  workforce management system's employee roster.
  - [HR-driven identity sync](identity-management/workforce-directory-sync/concepts/hr-driven-identity-sync.md)
  - [Ticketed batch run](identity-management/workforce-directory-sync/concepts/ticketed-batch-run.md)

## Security & access control

- [AI-Assisted Admin Actions](security-and-access-control/ai-assisted-admin-actions) —
  lets a conversational AI ticket-intake assistant carry out privileged
  platform actions while a zero-trust identity check, re-verified at
  execution time, decides what's actually allowed to happen.
  - [Reusable identity verification](security-and-access-control/ai-assisted-admin-actions/concepts/reusable-identity-verification.md)
  - [Privilege-gated execution](security-and-access-control/ai-assisted-admin-actions/concepts/privilege-gated-execution.md)
  - [Ambiguity is a stop condition](security-and-access-control/ai-assisted-admin-actions/concepts/ambiguity-is-a-stop-condition.md)

## Security operations

- [Threat Detection Incident Tracking](security-operations/threat-detection-incident-tracking) —
  turns a security detection into a tracked incident automatically: filed
  against the right business whenever possible, linked back to the
  source detection, and kept updated until it resolves.
  - [Layered ownership resolution](security-operations/threat-detection-incident-tracking/concepts/layered-ownership-resolution.md)
  - [Diff-based incremental polling](security-operations/threat-detection-incident-tracking/concepts/diff-based-incremental-polling.md)
  - [Bidirectional system linking](security-operations/threat-detection-incident-tracking/concepts/bidirectional-system-linking.md)

## Service delivery operations

- [NOC Ticket Routing](service-delivery-operations/noc-ticket-routing) — a
  recurring automation that triages helpdesk tickets across a portfolio of
  managed businesses by priority and technician activity, instead of a
  human scanning every ticket by hand.
  - [Config-driven, multi-org setup](service-delivery-operations/noc-ticket-routing/concepts/config-driven-multi-org-setup.md)
  - [Keyword-based priority routing](service-delivery-operations/noc-ticket-routing/concepts/keyword-priority-routing.md)
  - [Human-activity detection](service-delivery-operations/noc-ticket-routing/concepts/human-activity-detection.md)
- [Deployment Time Logging](service-delivery-operations/deployment-time-logging) —
  an endpoint-triggered automation that finds out how long an automated
  device deployment actually took and logs it back on the ticket that
  requested it.
  - [Dual-fallback identity resolution](service-delivery-operations/deployment-time-logging/concepts/dual-fallback-identity-resolution.md)
  - [Poll until the session completes](service-delivery-operations/deployment-time-logging/concepts/poll-until-session-completes.md)
  - [Configurable time disposition](service-delivery-operations/deployment-time-logging/concepts/configurable-time-disposition.md)

## Monitoring & alerting

- [Real-Time Incident Alerting](monitoring-and-alerting/real-time-incident-alerting) —
  a recurring automation that turns a monitoring alert into multi-channel
  notifications and automatically sends the all-clear once it resolves.
  - [Multi-channel fan-out](monitoring-and-alerting/real-time-incident-alerting/concepts/multi-channel-fan-out.md)
  - [Channel-specific quiet hours](monitoring-and-alerting/real-time-incident-alerting/concepts/channel-specific-quiet-hours.md)
  - [Self-resolving alert loop](monitoring-and-alerting/real-time-incident-alerting/concepts/self-resolving-alert-loop.md)
- [Platform Health Heartbeat](monitoring-and-alerting/platform-health-heartbeat) —
  a synthetic uptime check that verifies the automation platform's own
  trigger pipeline is alive, not just that a server is running.
- [Cross-Platform Synthetic Monitoring](monitoring-and-alerting/cross-platform-synthetic-monitoring) —
  a portfolio of synthetic checks, one per platform in the toolchain,
  that continuously exercises every system the business's service
  delivery depends on from the outside.
  - [One check per platform](monitoring-and-alerting/cross-platform-synthetic-monitoring/concepts/one-check-per-platform.md)
  - [Realistic multi-step checks](monitoring-and-alerting/cross-platform-synthetic-monitoring/concepts/realistic-multi-step-checks.md)
  - [Multiple vantage points per check](monitoring-and-alerting/cross-platform-synthetic-monitoring/concepts/multi-vantage-point-checks.md)
  - [Parallel monitoring during migration](monitoring-and-alerting/cross-platform-synthetic-monitoring/concepts/parallel-monitoring-during-migration.md)
  - [Expected-failure assertions](monitoring-and-alerting/cross-platform-synthetic-monitoring/concepts/expected-failure-assertions.md)

## Endpoint provisioning

- [Tenant Agent Auto-Provisioning](endpoint-provisioning/tenant-agent-auto-provisioning) —
  keeps every managed tenant's endpoint agent deployment policy current
  automatically, including the install credential embedded in it.
  - [Freshly minted install credentials](endpoint-provisioning/tenant-agent-auto-provisioning/concepts/freshly-minted-install-credentials.md)
  - [Idempotent deployment upsert](endpoint-provisioning/tenant-agent-auto-provisioning/concepts/idempotent-deployment-upsert.md)
