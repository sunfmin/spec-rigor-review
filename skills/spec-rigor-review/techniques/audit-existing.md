# Auditing existing checks

Check every schema check, model, and proof already in the project against this list.
For each failed check, quote the offending line or command and give the smallest
change that fixes it.

1. **The command cannot fail.** It prints findings and exits 0. Known cases:
   `oasdiff breaking` without `--fail-on`, `sqlc vet` with no rules configured, an
   Alloy `check` with no `expect`, `dafny audit`. For any other tool, find out what it
   returns on a violation before trusting it.
2. **Not wired in.** The check exists and nothing runs it: not CI, not a pre-commit
   hook, not the project's test command. Or CI runs it and ignores the result.
3. **Generated code can drift.** Generated files were edited by hand, or nothing
   verifies that regenerating leaves them unchanged.
4. **Validation is permissive.** A validator runs in the mode that coerces instead of
   rejecting; a type is `any` or `interface{}`; a cast or suppression comment skips
   the check.
5. **A gap in the proof.** `sorry`, `admit`, `assume`, an external or trusted body, an
   added axiom, a build that treats these as warnings. List each one with its
   location.
6. **Preconditions that exclude the case.** An assumption in a proof harness, a
   narrow initial state in a model, or a strengthened `requires` leaves no execution
   in which the rule could break. Look for a reachability check; if there is none,
   that is the finding.
7. **A weaker guarantee reported as a stronger one.** Sampling called verification, a
   bounded run called a proof, a bound nobody stated. Quote the claim and the command
   side by side.
8. **A coverage switch is off.** Overflow, memory, or unwinding checks disabled; only
   some modules or functions verified; a stub in place of the function under test.
9. **The model no longer matches the code.** An action in the model has no
   counterpart in the code, or the code has a transition the model lacks. List both
   directions.
10. **The rule restates the implementation, or anything satisfies it.** An invariant
    copied from the code, or one so weak that a wrong implementation would pass.
11. **The specification was changed to fit.** History shows a postcondition weakened
    or a precondition strengthened in the same change that made the check pass.
12. **Unpinned tool.** The checker's version floats, so a release can change the
    result without a change to the code.
