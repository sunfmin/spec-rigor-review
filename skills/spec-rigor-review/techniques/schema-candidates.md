# Finding schema and type candidates

## Patterns

Flag a location when one of these applies, and name the pattern in the finding.

| Pattern | Signal in the code | Enforcement |
|---|---|---|
| Duplicated shape | The same fields declared by hand in two languages, or in code and in an API document | One schema as the source; generate the rest |
| Drifting generated code | Generated files edited by hand, or no check that regenerating changes nothing | Regenerate in CI and fail on a difference |
| State as strings or booleans | A string compared against literals, a `switch` that needs a default, two or more booleans that cannot all be true | An enumeration or a tagged union, with exhaustive matching |
| Bare primitives | IDs, money, units, or durations typed as `string`, `int`, or `float`; adjacent parameters of the same primitive type | A dedicated type per meaning |
| Unparsed boundary | A handler reads fields out of a map or untyped JSON; the same validation repeated in several callers | Parse once at the boundary into a type that cannot hold invalid data |
| Required only sometimes | A nullable field that must be set in some states, with a comment saying when | One type per state, each with exactly its fields |
| Unguarded contract | An API, Protobuf, or SQL schema that other systems depend on, with no breaking-change check | A breaking-change check against the main branch |
| SQL in strings | Queries built as strings, with rows mapped to structs by hand | Typed access generated from the SQL |
| Rules for configuration in prose | Allowed values and combinations described in a README or comment | A schema or constraint file the config is checked against |
| Lax validation | A validator in its permissive mode, an `any` type, a cast that skips the check | Strict mode; remove the escape hatch |

## What each finding says

- **How the rule is enforced today**: by a comment, a convention, a runtime check, a
  test, or nothing. This is what the user compares the proposal against.
- **Where the two copies disagree**, when the pattern is a duplicated shape. A
  disagreement is a defect in its own right. Say which copy the running system uses
  and do not choose the winner.

## Do not flag

- A type used inside one package with one writer, where a stricter type would guard
  nothing a reader cannot already see.
- Replacing one schema technology with another. Review how the project uses what it
  has.
- A rule with no evidence behind it. List it under "Not covered" with what would
  settle it.
