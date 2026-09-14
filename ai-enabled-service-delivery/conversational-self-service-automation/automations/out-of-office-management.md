# Out-of-office management

Sets, schedules, or clears a mailbox's automatic reply configuration,
gated on the submitter being either the mailbox's own owner or that
owner's verified manager.

## What it does

1. Branches on the requested action — enable now, disable now, or
   schedule a future start/stop — each mapping to the same underlying
   mailbox configuration command with different parameters.
2. Before applying anything, checks whether the submitter is modifying
   their own mailbox or someone else's. Modifying your own is
   self-authorizing; modifying someone else's requires that the
   submitter actually be that person's manager on record in the
   directory — checked fresh, not assumed. Anything that doesn't match
   either case is flagged for manual review, taking no action (the same
   [ambiguity-is-a-stop-condition](../../../security-and-access-control/ai-assisted-admin-actions/concepts/ambiguity-is-a-stop-condition.md)
   instinct applied to a self/manager check instead of a role check).
3. Confirms the change to both the ticket and the requester directly,
   including the specific messages that were set.

## Why check self-or-manager instead of just checking role

Setting someone else's out-of-office message isn't a platform-admin
action — it's a personal one, so the right question isn't "is this
person an administrator" but "does this person have a legitimate
relationship to the mailbox being changed." Checking the actual
management relationship, live, answers that directly instead of
substituting a broader permission check that wouldn't actually capture
what should and shouldn't be allowed here.
