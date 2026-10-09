# Finding model-checking candidates

A model checks the design, not the code. It pays off where a bug needs several steps
or several actors to appear, because those are the cases tests and reviews miss.

## Signals

| Signal in the code | What to model |
|---|---|
| A status field with rules about which transition may follow which | The states, the events, and the guards |
| A workflow across several systems that can fail halfway: payment then stock, write then publish | Each step and each failure point, including a crash between steps |
| Retries, timeouts, idempotency keys, at-least-once delivery | Duplicate and reordered messages |
| Two writers to one record, or read-then-write without a transaction | The interleavings of the two writers |
| Locks, leases, leader election, queues with visibility timeouts | Expiry while the holder is still working |
| A cache or replica beside its source | The window in which they differ |
| Permissions that change over time: grant, revoke, delegate | The sequence of changes and who can act after each |

## Invariants to state

State each one in a sentence and give its evidence.

- **Safety** — something that must never hold: a shipped order that is unpaid, a
  negative balance, two holders of one lock.
- **Reachability** — a state the design must be able to reach: an order can be
  completed. Always state at least one. A model in which nothing can happen satisfies
  every safety rule.
- **Liveness** — something that must eventually happen. Report it as a candidate only
  when the evidence is explicit; it is harder to state and to check.

## Size

Propose the smallest model that can show the bug: two or three actors, small value
ranges, only the fields the invariants mention. Name the actions and the invariants in
the finding. Do not write the model during the review.

## The connection to the code

Name the test that will connect the model to the code: usually traces from the model
replayed against the function that holds the transitions. If no single function holds
them, the candidate needs that refactor first; say so.

## The guarantee

Say which one the proposed check gives:

- **Sampled** — random executions; finds shallow bugs fast and proves nothing.
- **Bounded** — every execution up to N steps.
- **Exhaustive** — every execution of a finite model.
- **Inductive** — an invariant shown to be preserved by every step.

## Do not flag

- Sequential create, read, update, delete with no intermediate state.
- A status field that a single function writes, with a handful of states, where an
  enumeration and an exhaustive match are enough. Report it as a schema and type candidate.
- Pure computation. That is a proof candidate or a property test.
