# Reusable identity verification

**The idea:** every automation that can take a privileged action on the
submitter's behalf needs the same answer to the same question — "is this
person actually an admin?" — so that check exists exactly once, as a
shared building block, and every privileged-action automation calls it
rather than re-implementing its own version.

## What it resolves

Given an email address, the check:

1. Looks up the platform's user directory for an account matching that
   email.
2. If no match exists, reports the user as not found and stops there —
   there's nothing further to evaluate.
3. If a match exists, inspects that account's role/group memberships for
   anything indicating admin- or developer-level access, and reports
   whether it found one.

A calling workflow gets back three independent pieces of information —
whether the user exists at all, whether they're an admin, and the
underlying account details — so it can branch on exactly the distinction
it cares about, instead of collapsing everything into a single yes/no.

## Fails closed, not open

If the directory lookup itself errors out — the platform is unreachable,
for instance — the check doesn't leave admin status undefined or throw an
exception a caller might mishandle. It explicitly reports "not found" and
"not admin," so any failure in the check itself defaults to the least
privileged outcome. A calling workflow can never end up granting
privileged access as a side effect of the verification step itself
failing.

## Why centralize this instead of checking inline

Every automation that needs this answer asks it the same way, so a change
to what "counts" as admin (a new role name, a different directory field)
only has to change in one place. It also means the definition of "admin"
can't quietly drift between one automation and another.
