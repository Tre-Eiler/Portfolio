# Diff-based incremental polling

**The idea:** a bounded polling loop checks in on a live incident
periodically, but instead of re-reporting the whole history every time,
each cycle compares what it sees now against what it already knew and
surfaces only the difference. The loop also carries its own exit
condition, checked independently on every tick — not just the bounded
attempt count, but whether the incident itself has already resolved.

```mermaid
sequenceDiagram
    participant Loop as Polling loop
    participant Source as Live incident record
    participant Ticket

    Loop->>Source: Check current status
    alt Resolved
        Loop->>Ticket: Post resolution note, stop
    else Still open
        Loop->>Source: Fetch current activity
        Loop->>Loop: Diff against what's already known
        alt New activity found
            Loop->>Ticket: Post note with just the new items
        else Nothing new
            Loop->>Loop: Skip the note
        end
        Loop->>Loop: Wait, then repeat (bounded attempts)
    end
```

## Why diff instead of re-reporting everything

An incident's activity history only grows longer the more times it's
polled. Re-posting the full history on every cycle would make the ticket
noisier and less useful with every check-in, burying the one thing that
actually matters right now — what's new — under everything that was
already reported. Diffing against a running set of what's already known
keeps every posted update meaningful.

## Why check for resolution separately from checking for new activity

An incident can resolve without generating any new activity in the same
tick that resolution happens — so folding "is it resolved" into the same
check as "is there anything new to report" would risk missing the
resolution, or delaying it a full cycle. Checking it as its own
condition, every tick, means resolution is caught as soon as it's checked
for, not as a side effect of something else.

## Why bound the loop, and what happens at the boundary

Some incidents never reach a clean "resolved" state within any reasonable
window — this shares the same reasoning as [Real-Time Incident
Alerting](../../../monitoring-and-alerting/real-time-incident-alerting/concepts/self-resolving-alert-loop.md)'s
bounded polling: an unbounded wait can't tell "still legitimately in
progress" from "nobody's watching this anymore." When the bound is
reached here, though, the loop doesn't just stop quietly — it posts a
note telling whoever's watching exactly where to pick up manually,
because unlike a simple back-online check, a still-open security incident
genuinely needs a human to take it from there.

## Design principles

- **A running "known" set makes diffing possible.** Every reported item
  gets folded into what's already known, so the next comparison is always
  against everything seen so far, not just the last cycle.
- **Silence is a valid, correct outcome for a tick.** A cycle with
  nothing new posts nothing — the absence of a note is itself
  informative (no news), rather than the automation manufacturing an
  update to prove it's still running.
- **Reaching the bound ends in a handoff, not a dead stop.** The closing
  note names what's known so far and points explicitly at the source
  system for anyone continuing to track it by hand.
