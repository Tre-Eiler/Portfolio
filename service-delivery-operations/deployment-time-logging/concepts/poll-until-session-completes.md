# Poll until the session completes

**The idea:** the webhook fires from the endpoint at the end of the
deployment script, but that's not necessarily the same moment the
platform's own record of the session considers it complete. Rather than
trusting the trigger's timing, the automation goes and finds the actual
session record for that device and waits on the platform's own status
until it reports done.

## The wait loop

1. Find the device's most recent deployment session on the endpoint
   platform.
2. Ask the platform for that session's current status.
3. If it isn't complete yet, wait a fixed interval and check again — up
   to a bounded number of attempts.
4. Once the platform reports the session complete, read its actual
   duration and who initiated it directly from the session record, rather
   than estimating either from the webhook's own timing.
5. If the bounded number of attempts is exhausted without the session
   ever completing, that's treated as a suspected outage rather than "the
   work isn't done yet" — and opens a tracking issue instead of waiting
   forever.

## Why not just trust the webhook's timestamp

A session can still be finishing up platform-side bookkeeping after the
script that triggers the webhook has already exited, and the platform is
the only source that actually knows the true start time, end time, and
who ran it. Reading the duration from the session record instead of
approximating it means the logged time reflects what really happened,
and it's the same reason the automation looks up *who* ran the session
from the platform rather than trusting anything the endpoint reported
about itself.

## Why bound the retries

An unbounded wait can't distinguish "still legitimately running" from
"something on one side is actually down." Capping the number of attempts
means a real outage surfaces as an actionable tracking issue within a
predictable window, instead of the automation just hanging indefinitely.
