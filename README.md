# spec-rigor-review

An agent skill that reviews existing code for rules a machine could enforce: with a
schema or a type, with a design-level model checker, or with a code-level proof. It
also audits the checks of those kinds a project already has for ones that guard
nothing, and finds the refactors that would make the code checkable.

It produces a ranked findings report first, and writes checks or refactors only after
you pick findings.

## Install

```bash
npx skills add sunfmin/spec-rigor-review -g
```

## Use

```
/spec-rigor-review internal/order
```

or ask in your own words: "which rules here should be enforced by types?", "does this
state machine have holes?", "do these proofs actually prove anything?".

## Layout

- `skills/spec-rigor-review/SKILL.md` — process, report format, standing rules
- `skills/spec-rigor-review/techniques/` — one short guide per stage, loaded on demand

Tool recommendations in `techniques/tools.md` were verified on 2026-10-09.

## Related

[test-rigor-review](https://github.com/sunfmin/test-rigor-review) covers property
tests, fuzz tests, and mutation testing in the same way.
