# Self-resolving alert loop

**The idea:** firing an alert isn't the end of the job — someone still
needs to know when the problem goes away. Rather than relying on a human
to remember to send an all-clear, the automation keeps checking the
underlying record on an interval, and the moment it flips to resolved,
automatically fans out a recovery notification through the same channels
the original alert used.

```mermaid
sequenceDiagram
    participant Event as Alert event
    participant Loop as Polling loop
    participant Record as Underlying record
    participant Notify as Notification fan-out

    Event->>Notify: Fire initial alert
    Event->>Loop: Start polling
    loop Every few minutes, up to a cap
        Loop->>Record: Check current status
        Record-->>Loop: Still open
    end
    Record-->>Loop: Now resolved
    Loop->>Notify: Fire recovery notification
```

## Why bound the polling

An unresolved problem shouldn't poll forever — that risks a runaway
background job outliving any reasonable relevance of the original alert.
Capping the number of check-ins, at an interval long enough not to hammer
the underlying system, means the loop always terminates on its own, even
if the underlying issue never gets marked resolved through the expected
path.

## Design principles

- **The recovery notification reuses the alert's own
  [fan-out](./multi-channel-fan-out.md)**, not a separate notification
  path — so the "problem's over" message reaches exactly the audiences
  who were told about the problem in the first place.
- **Polling has a ceiling.** A bounded number of attempts at a fixed
  interval means the automation always stops on its own, rather than
  running indefinitely against a record that may never change state.
- **Detection is state-based, not time-based.** The loop doesn't guess how
  long a fix should take — it watches for the actual state change and
  reacts to it whenever it happens, within the polling ceiling.
