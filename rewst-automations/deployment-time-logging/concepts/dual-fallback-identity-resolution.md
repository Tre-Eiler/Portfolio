# Dual-fallback identity resolution

**The idea:** the automation is handed identity for two different things
— a ticket and a device — and in both cases it can't be sure up front
which lookup will actually work. Rather than requiring the trigger to
know the right answer in advance, each lookup tries the more reliable
path first and only falls back if that comes up empty.

## Resolving the ticket

The originating ticket could be either of two ticket types in the
ticketing system, and the trigger doesn't distinguish between them — it
just passes a number. The automation tries the first type; if that
lookup fails, it retries the same ID as the other type before giving up.
Only if both attempts fail does it treat the ticket as genuinely not
found.

## Resolving the device

The device is looked up first by its hardware serial number, which is
stable even if the device gets renamed. If that search comes back empty
— a device not yet inventoried under that serial, for instance — the
automation falls back to searching by the device's reported computer
name instead. Either path, once it narrows to exactly one match, resolves
to the same downstream device identity; if a lookup returns more than one
candidate or zero, that's treated as unresolved rather than guessed at.

## Why fall back instead of requiring the caller to know

The script running at the endpoint is intentionally simple — it reports
what it already knows about itself (its name, its serial number, the
ticket number it was given) without needing to know which ticket type or
which identifier will actually resolve on the other end. Pushing that
disambiguation into the automation means the triggering script stays
generic and doesn't need updating if the ticketing system's type split or
the device platform's preferred identifier ever changes.
