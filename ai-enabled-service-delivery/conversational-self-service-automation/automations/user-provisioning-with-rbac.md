# User provisioning with role assignment

Adds a user to the endpoint management platform and assigns them to a
permission group, resolved from a short list of known, auto-processable
roles.

## What it does

1. Looks up the requested permission tier against a small allowlist (see
   [constrained
   auto-processing](../concepts/constrained-auto-processing.md)); a tier
   outside that list still adds the user, but flags the group assignment
   itself for manual completion rather than guessing.
2. Requesting more than one role at once is treated as an unsupported,
   ambiguous request and hard-stops entirely, taking no action.
3. Checks whether the target user already exists in the platform: found
   once, it reuses that record; found zero times, it creates a new one;
   found more than once, it hard-stops for manual review rather than
   guessing which record is the real one (the same
   [ambiguity-is-a-stop-condition](../../../security-and-access-control/ai-assisted-admin-actions/concepts/ambiguity-is-a-stop-condition.md)
   instinct, applied to a duplicate-record lookup instead of a role
   check).
4. Re-verifies the submitter's own admin/developer role before assigning
   any permission group at all — a submitter who isn't verified still
   gets the user added, just without a group assigned (see
   [privilege-gated
   execution](../../../security-and-access-control/ai-assisted-admin-actions/concepts/privilege-gated-execution.md)
   for the same "the low-risk part proceeds either way, only the
   privileged part is gated" shape).

## Why let user creation proceed without verified admin, but not group assignment

Creating a bare user record is reversible and low-risk; assigning a
permission group is what actually grants access. Splitting the two means
an unverified submission still makes forward progress — the user exists
and is ready — without ever letting an unverified request grant
permissions on its own.
