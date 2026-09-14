# Tenant provisioning

Creates a new managed-tenant record in the endpoint management platform
for a business being onboarded, without a technician manually clicking
through the platform's own tenant-creation flow.

## What it does

1. Verifies the parent (aggregating) tenant referenced in the request
   actually exists before attempting anything — an invalid parent
   reference gets flagged for manual review rather than a failed create
   attempt.
2. Re-verifies the submitter's admin/developer role on the platform
   itself before creating anything (see [reusable identity
   verification](../../../security-and-access-control/ai-assisted-admin-actions/concepts/reusable-identity-verification.md)).
3. Creates the tenant record under the verified parent.
4. Posts a start note, a completion note with the new tenant's identity
   and a direct link to it, and logs the automation's time against the
   originating ticket — or, on any failure along the way, a note
   specific to what failed (parent not found, not authorized, or the
   create call itself failing) so the next step is always clear.

## Why validate the parent before checking authorization

Checking authorization first and then discovering the parent tenant
doesn't exist would burn a permission check on a request that could never
have succeeded anyway. Validating the more fundamental precondition first
means a bad request fails fast, with a note that says exactly what's
wrong, before spending effort on anything else.
