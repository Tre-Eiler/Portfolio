# Ambiguity is a stop condition

**The idea:** an automation acting on an AI-extracted request has to
resolve fuzzy human input (an email, a role name) down to one concrete
record before it can safely act. When that resolution comes back with
more than one plausible answer, guessing which one was meant is worse
than doing nothing — so ambiguity itself is treated as a reason to halt
and ask a human, not a case to resolve automatically.

## Where this shows up

- **More than one account matches a single identifier.** If a lookup
  meant to find exactly one user instead returns several, the automation
  doesn't pick the first, the most recent, or any other implicit
  tiebreaker — it stops and posts a note asking for manual review, since
  guessing wrong here means granting or denying access to the wrong
  person.
- **More than one permission group requested in a single submission.**
  The automation is built to assign one group per request. If the
  extracted request actually asks for multiple groups at once, that's
  treated as outside what a single automated run should decide on its
  own, and it hard-stops rather than applying all of them or picking one.

## Why not just apply a tiebreaker

A plausible-sounding tiebreaker (most recent account, first alphabetical
group) would make the automation's behavior consistent but not
necessarily *correct* — and because the actions involved touch access and
tenant data, a wrong guess is the expensive kind of mistake. Refusing to
guess keeps the automation's decisions limited to cases where the right
answer is actually unambiguous, and pushes every other case to a human
who has the context to resolve it correctly.
