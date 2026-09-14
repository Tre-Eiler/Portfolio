# Human-activity detection

**The idea:** before the automation treats a ticket as stalled — worth
escalating or rerouting — it has to be sure a real technician genuinely
hasn't acted on it. Otherwise a technician quietly working a ticket could
get their in-progress work rerouted out from under them. The check looks
at the ticket's own activity history rather than its status alone, since
status can lag behind what's actually happening.

## What counts as "activity"

- The ticket's full audit trail (status changes, assignments) and its
  logged time entries are both pulled and checked for entries attributed
  to any technician — **excluding the automation's own service account**,
  so the automation never mistakes its own prior actions for human
  attention.
- If either source shows a real technician touched the ticket, it's
  considered actively worked and skipped for stall-based escalation, even
  if its status or priority would otherwise qualify.
- Only when both sources come back empty of human activity does the
  automation treat the ticket as genuinely unattended.

## Why check two sources instead of one

Status and assignment changes (the audit trail) capture *some* forms of
attention, but a technician can log time against a ticket without
necessarily changing its status first. Checking both closes that gap —
a ticket isn't marked "unattended" just because nobody happened to move
its status.

## Retrying the lookups

Fetching a ticket's audit trail or time entries is a live call to the
underlying system and can fail transiently. Rather than let one failed
lookup either block the whole run or silently be treated as "no
activity found," a failed fetch retries on a short, increasing backoff up
to a small maximum number of attempts before falling back to a failure
path — so a brief upstream hiccup doesn't get misread as evidence nobody
worked the ticket.
