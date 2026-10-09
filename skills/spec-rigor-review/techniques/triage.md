# Triage of counterexamples and unproven goals

Keep a ledger of every violation, counterexample, and unproven goal you observe while
working.

## Counterexamples

A checker that returns a counterexample has found something. First restate the trace
in the project's own terms: which actor did what, in which order, and which rule
broke. Then each entry ends in exactly one of two verdicts:

- **Flaw in the design or code** — the trace describes something the real system can
  do. Report it to the user with the trace and the minimal steps.
- **Flaw in the model or specification** — the model allows a step the real system
  cannot take, or the rule was stated wrongly. Note what was wrong and how you fixed
  it. If the fix changes a rule the user approved, the user decides.

There is no third verdict. "Unlikely in practice" and "needs unusual timing" describe
how severe a finding is. They do not make it disappear.

## Unproven goals

A prover that gives up has not found a counterexample and has not proved the rule.
Report the goal as **unproven**, with what the tool said and what you tried. It is
never reported as passing.

## Forbidden moves

- Weakening an invariant or a postcondition.
- Adding an assumption or strengthening a precondition to exclude the trace.
- Lowering a bound, shrinking the model, or narrowing its initial states.
- Marking a step as trusted or skipped.
- Switching the check to a weaker mode, such as sampling in place of verification.
- Changing production behavior without the user's decision.

Each of these is allowed only after the finding has been reported and the user has
chosen that outcome.

## Final report

Write it from a fresh run of every check. Every violation and unproven goal in that
run appears in the report, and every earlier one that no longer appears has its
ledger entry cited.
