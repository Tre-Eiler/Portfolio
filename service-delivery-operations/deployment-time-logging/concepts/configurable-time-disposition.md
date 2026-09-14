# Configurable time disposition

**The idea:** not every deployment should be logged the same way, and
whoever kicked off the deployment is in the best position to know which
way it should go — so the triggering script passes a billing choice
along with the ticket and device identity, and the automation branches
on it rather than applying one fixed rule to every run.

## The three outcomes

- **Bill it** — the computed duration is logged as billable time against
  the ticket.
- **Log it, don't bill it** — the same duration is recorded for
  visibility and reporting, but marked non-billable.
- **Skip it entirely** — the automation stops immediately after
  receiving the trigger; no session lookup, no time entry, no note.
  Useful when the deployment itself doesn't warrant a logged entry at
  all, and it avoids doing any of the more expensive lookup work for a
  run that doesn't need it.

## An explicit override, when the computed duration isn't the right number

The trigger can also supply its own duration value instead of the one the
automation would otherwise compute from the session record. This exists
for cases where the actual billable time genuinely differs from how long
the platform's own session took — the automation defers to the explicit
value when one is given, and only falls back to the computed duration
when it isn't.

## Why push this decision to the edge

The script triggering the automation runs at the moment the deployment
actually finishes, when the context for "should this be billed" is
freshest. Deciding it there — as a value passed along with the trigger —
means the automation doesn't need its own rules engine for billing
policy, and the same automation serves every disposition without a
separate workflow per outcome.
