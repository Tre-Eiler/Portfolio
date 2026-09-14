# SentinelOne Agreement Sync

Two-mode workflow: an interactive **setup** wizard per MSP, and a **weekly
scheduled sync** across the whole portfolio. Both write into the same
`sentinelone_agreement_config` per-org variable, which is also what feeds
the SentinelOne row in the [Agreement Automation
Overview](../agreement-automation-overview.sample.html) report.

## What it does

- **Setup:** given a PSA agreement ID and one or more addition IDs (a single
  addition, or a desktop/server split), verifies they exist in ConnectWise,
  resolves the correct CW product to bill by checking the SentinelOne
  site's license SKU tier, sets that product on the addition(s), and saves
  the resulting config to an org variable.
- **Weekly sync:** re-reads every org's saved config and re-runs the same
  addition-verification/product-update logic, so the agreement's billed
  product stays correct as SKU tiers change over time.
- Both paths open a PSA ticket to log the work (and close it with a time
  entry), so the automation shows up as trackable technician time instead
  of running invisibly.

## Setup path

1. **Entry check** — branches on whether a setup form has been submitted
   (an addition method is specified) vs. a normal invocation.
2. **Open a ticket** for the setup work, with a link back to the Rewst
   execution log in the description.
3. In parallel: look up the SentinelOne site (to read its license SKU) and
   list agents at that site — zero agents is a signal something's
   misconfigured.
4. **Resolve the CW product to bill from the SKU** — the higher license
   tier maps to one product, everything else to another.
5. **Verify the PSA agreement exists**, then branch on the addition method:
   - *Single addition* → verify it exists → set its product.
   - *Desktop/server split* → verify and update each addition
     independently, joining once both are done.
6. Once all additions are confirmed and updated: post a
   verification-summary note to the ticket, assemble the final config
   object (agreement ID, addition ID(s), SentinelOne site ID, org ID, who
   ran setup), write it to the org's `sentinelone_agreement_config`
   variable, log setup time to the ticket, and hand the config off to the
   same sync routine the weekly job uses.

## Weekly sync path

1. List every `sentinelone_agreement_config` org variable across the whole
   managed-org portfolio.
2. If any exist, open one PSA ticket for the sync run.
3. Fan out (concurrency 5) over every org's saved config, re-running the
   addition-verification/product-update logic per org.
4. Log time to the ticket proportional to how many orgs were synced, and
   close it.

## Design notes

- **Setup and cron converge on the same routine.** Both paths end by
  calling the same addition-sync logic with the same shape of input
  (config JSON, org ID, ticket ID) — setup runs it once immediately after
  building the config; the cron job runs it in bulk from saved configs.
  There's one place that knows how to reconcile a SentinelOne SKU against a
  PSA addition, not two.
- **SKU tier drives product selection, not a static mapping.** Since
  licensing tier changes what should be billed, the product ID is resolved
  fresh from the live site SKU every run, never cached from setup.
- **PSA time is logged for both automation runs**, scaled to work done (a
  fixed setup entry vs. a per-org multiplier on the weekly run), so the
  automation's labor savings are visible in the PSA rather than silent.
