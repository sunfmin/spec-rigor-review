# Finding code-level verification candidates

A proof costs several times the code it covers. Report a candidate only when all three
hold, and say in the finding how each one is met.

1. **A violation is expensive** — money, authorization, data loss, or memory safety.
2. **The logic is stable** — it has not changed in recent history and no change is
   planned.
3. **It is small and self-contained** — one function or module with no I/O and few
   dependencies. If it is not, look for a refactor in `refactors.md` first.

## Signals

- Arithmetic on money or quantities where overflow, rounding, or sign matters.
- Index and length arithmetic in parsers, codecs, and buffers.
- `unsafe` Rust.
- A function that decides whether an action is permitted.
- A core algorithm other code trusts: a rate limiter, a scheduler, a merge, an
  allocator.

## What is available per language

| Code | Option | Guarantee |
|---|---|---|
| Rust | A bounded model checker run on the function itself | Every input, within stated bounds on loops |
| Rust, a small core | A deductive verifier with specifications in the code | Every input |
| Any language | A reference model in a proof assistant, with properties proved about the model, plus tests that compare the production code against the model | Proven for the model; tested for the code |
| Go, TypeScript, Python | No code-level verifier fit for routine use | Report a property test instead and name `test-rigor-review`, or propose a reference model |

`tools.md` names the tools.

## What each finding says

- The specification in one sentence: what must hold of the result, given what about
  the input.
- For a bounded check, the bound and why it is large enough.
- For a reference model, the differential test that will compare the production code
  against it, and where that test will run. A model nobody compares to the code
  proves nothing about the code.

## Do not flag

- Code that changes often.
- Glue, I/O, and anything whose correctness is a matter of calling other systems.
- Concurrent code as a candidate for a bounded checker that treats threads as
  sequential. Model the design instead.
