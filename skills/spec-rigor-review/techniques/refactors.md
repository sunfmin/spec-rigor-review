# Refactors that unblock these checks

Report these in their own table. Each finding names the blocker, the change, and the
check the change unlocks. A refactor with no check waiting behind it is out of scope.

A change that is itself the enforcement, such as replacing a string with an
enumeration or generating a type from a schema, is a schema and type candidate.
Report it there and not here. This table is for changes that enforce nothing on their
own.

| Blocker | Refactor | Unlocks |
|---|---|---|
| State transitions spread across handlers and mixed with database calls | Extract one pure function from state and event to new state; keep the I/O in a thin caller | A model can mirror it, and traces from the model can test it |
| An invariant that exists only in a comment | Extract it as a predicate the code can call | The model and the tests share one statement of the rule |
| Ordering of concurrent or retried steps left implicit | Name the steps and the messages explicitly | The interleavings can be modeled |
| A core algorithm tangled with framework code | Isolate it in a small module with no dependencies | Code-level verification |
| Nondeterminism read from inside: time, randomness, map order | Pass it in as a parameter | The function becomes a deterministic step a model can replay |

## Limits

- Propose the smallest change that unblocks the check. Do not bundle other cleanup.
- Keep behavior identical. If existing tests cover the code, they must pass unchanged
  after the refactor; if none do, say so, because the refactor then has no safety net.
- Do not propose moving code to another language to reach a verifier.
- When a blocker is costly to remove, say what weaker check is possible without it.
