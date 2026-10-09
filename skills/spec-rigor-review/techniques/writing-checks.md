# Writing the checks the user picked

## Before writing

Read how the project already uses the tool. If it does not, read the tool's
documentation for the installed version. Check the name and syntax of everything you
write; most of these tools changed syntax within the last year.

**Ask before any install or download.** Name the size when you know it. Several of
these tools download on first use without saying so in advance: a model checker
fetched by the first verification run, a toolchain fetched when a project is first
built. `tools.md` lists the ones that were measured.

## Wire it in

A check that only runs when someone remembers it guards nothing.

- Give the check one command, and add that command to the place the project already
  runs its checks: the test target, the pre-commit hook, the CI workflow.
- Make the command exit non-zero on a violation. Add the flag that does this when the
  tool needs one.
- Pin the tool's version in the project.
- For generated code, follow the project's convention on committing it, and add a
  step that regenerates and fails on a difference.

## Prove the check can fail

After wiring a check, break the rule on purpose, run the command, and confirm a
non-zero exit. Then restore the code. Record in the summary which break you used and
the output.

For a model, do both:

- Break the design in the model and confirm the invariant is reported as violated.
- Check a reachability claim, so you know the model can reach the states the
  invariant is about.

## State the guarantee

Write beside each check what it establishes: sampled, bounded to N steps, exhaustive
on a finite model, or proven. For a bounded check, write the bound and why it is
enough.

## Tie the model to the code

Say which of these holds, in the summary and in a comment at the top of the model:

- Traces generated from the model are replayed against the code as tests.
- Logs from the running code are checked against the model.
- The correspondence is manual. Then list which function implements each action, so
  a reader can check it and a later change can keep it.

## Accepting a proof

Before reporting a proof as done:

- Build in the mode that turns warnings into errors.
- Search for every way the tool allows a step to be skipped, and use the tool's own
  listing where it has one. `tools.md` gives the flag or command per tool.
- Compare the final specification with the one the user approved. A proof of a
  weakened statement is not a proof of the rule.

## When a check fails or will not go through

Follow `triage.md`.
