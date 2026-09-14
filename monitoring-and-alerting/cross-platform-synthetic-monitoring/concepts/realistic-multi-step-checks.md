# Realistic multi-step checks

**The idea:** several checks don't stop at "did the server respond" —
they walk through an actual short sequence of operations a real
integration would perform (look something up, list related records,
create something, confirm it landed) and only pass if every step in that
sequence behaves as expected.

```mermaid
flowchart LR
    S1[Step 1: read something] --> S2[Step 2: list related records]
    S2 --> S3[Step 3: create a record]
    S3 --> S4[Step 4: confirm it exists]
```

## Why a sequence instead of a single request

A platform can respond successfully to a simple read while its write path
is broken, or vice versa — a single-request check only ever sees the one
thing it asked about. Chaining a realistic sequence of operations
together means the check fails at exactly the step that's actually
broken, and passes only when the platform can do the range of things
it's actually relied on for, not just answer a ping.

## Design principles

- **The sequence mirrors real usage**, not an arbitrary set of endpoints
  — it's built from the same kind of calls an actual automation or
  integration would make against that platform.
- **Failure location is diagnostic.** Because the steps run in a fixed
  order, knowing which step failed narrows down what's actually broken
  without needing to investigate further.
- **A passing check is a stronger guarantee** than a ping ever could be —
  it's evidence the platform can currently do the specific things it's
  depended on for, not just that it's reachable.
