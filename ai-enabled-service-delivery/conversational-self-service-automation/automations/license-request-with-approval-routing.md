# License request with approval routing

Grants one of a small set of pre-approved software licenses to a user,
routed through that person's actual approver — looked up from the
organization's own system of record — rather than a self-declared
approver or a blanket auto-approval.

## What it does

1. Checks the requested license against a short allowlist of specific,
   pre-approved SKUs (see [constrained
   auto-processing](../concepts/constrained-auto-processing.md));
   anything outside that list is flagged for manual handling instead of
   attempted.
2. For an eligible request, looks up the requesting employee's actual
   approver from the organization's HR/workforce system of record —
   rather than trusting anyone the requester names — and routes the
   approval step to that person.
3. Once approved, applies the license assignment and logs the outcome
   back to the originating ticket.

## Why route approval through HR data instead of the request itself

A request that names its own approver is trusting the requester to
correctly (and honestly) identify who's allowed to sign off on their own
spending. Looking the approver up from the same system that already
tracks organizational reporting structure removes that trust requirement
entirely — the automation asks an authoritative source who the approver
is, rather than asking the person requesting the license.
