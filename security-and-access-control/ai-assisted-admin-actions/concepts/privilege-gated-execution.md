# Privilege-gated execution

**The idea:** not every action carries the same risk, so the identity
check (see
[reusable-identity-verification.md](./reusable-identity-verification.md))
isn't applied as one uniform on/off switch in front of every automation.
Instead, it's placed at whichever specific step in the action actually
grants or exercises privilege — and what happens when that check fails
depends on how reversible the action is.

## Fully destructive actions: blocked outright

Merging or permanently retiring a tenant can't be undone by re-running
the automation the other way. For an action like this, admin
verification gates the entire thing: if the submitter isn't confirmed as
an admin, the action never runs at all — not "runs cautiously," not
"runs with different values." Every non-admin outcome (submitter not
found, found but not an admin, or the check landing in an unexpected
state) resolves to the same shape: a note explaining what happened, and
zero mutation of anything.

## Lower-risk actions: gate only the privileged part

Creating a new baseline account is comparatively low-risk on its own — a
bare account with no permissions attached isn't inherently dangerous.
That step is allowed to proceed regardless of the submitter's admin
status. What *is* gated is the step that actually grants privilege:
assigning that account to an elevated permission group. If admin
verification fails, the automation still creates the account (so the
overall request isn't fully blocked over one unverified detail), but
skips the group assignment and leaves a note that a human needs to
finish that part by hand.

## Why place the gate this precisely

Blocking every action outright any time verification fails is simplest,
but it means a low-risk request gets stopped for the same reason a
high-risk one would be — which either trains people to expect friction
everywhere or, worse, pressures the gate itself to be loosened. Placing
the check at the exact point where privilege is actually granted keeps
the strictness proportional to what's actually at stake in each action.
