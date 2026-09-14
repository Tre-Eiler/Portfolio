# Duo Agreement Sync

Same setup/cron split as the [SentinelOne
sync](./sentinelone-sync.md), driving a `duo_agreement_sync` per-org
variable that stores which PSA agreement/addition(s) map to a customer's
Duo account.

## What it does

- **Setup:** given an agreement ID, a Duo account ID, and one or more
  addition IDs, verifies the agreement exists, optionally updates each
  addition to a fixed Duo product, and saves that mapping (plus the Duo
  account ID) as org variables. Immediately triggers a sync using the
  just-saved config.
- **Scheduled sync** ("Weekend Sync"): re-reads every org's saved
  `duo_agreement_sync` config and re-runs the sync logic for each, fanned
  out with limited concurrency.

## Flow

1. **Entry branch** — a setup form submission (or a specific manual
   trigger) goes down the setup path; anything else goes down the
   scheduled path.
2. **Setup path** — build a config object (addition IDs, agreement ID, Duo
   account ID, who submitted it, target org), verify the PSA agreement
   exists, and — if the mapping option requests it — update each addition
   to a fixed Duo product ID (fanned out with limited concurrency). Write
   both `duo_agreement_sync` (the full config, JSON-encoded) and
   `duo_account_id` as cascading org variables, so child orgs inherit them
   too. Finally, hand the config to the sync sub-workflow for an immediate
   first run.
3. **Scheduled path** — list every org variable named `duo_agreement_sync`
   across the portfolio, and fan out (concurrency 3) over each one into the
   same sync sub-workflow.

## Design notes

- **Cascading org variables** — unlike the SentinelOne/M365 configs, these
  are written with cascade enabled, so child orgs under the org they're set
  on inherit the same Duo mapping automatically. Useful when a Duo account
  is shared across a parent/child org structure.
- **Same setup→sync handoff pattern as SentinelOne** — setup ends by
  invoking the identical sub-workflow the cron job calls per org, so
  there's one implementation of "how to sync a Duo mapping," whether it's
  triggered once (setup) or on a schedule (cron).
