# Choosing tools

Read the row for every tool the project already uses: the "what to know" column says
what the tool returns on a violation and what guarantee it gives, which the audit
needs. Use what the project has; the choices here apply only when it has no tool for a
need. Release and maintenance status was checked on 2026-10-09. Tools marked
**ran** were executed that day on macOS arm64; the rest were checked against their
documentation and source only. Confirm the current release and the exact commands in
each tool's documentation before use.

## Schema and types

| Need | Choice | What to know |
|---|---|---|
| Interfaces between services | Protobuf with buf (**ran**, 1.69) | `buf lint`, `buf breaking --against`, `buf generate`. Exits 100 on a violation. `--error-format=json` gives one object per line; an entry of type `COMPILE` means the comparison did not run. buf trails new Protobuf editions; stay on the one the project uses. |
| HTTP interfaces | OpenAPI 3.1 with oasdiff | `oasdiff breaking <base> <revision> --fail-on ERR`. Without `--fail-on` it exits 0. Never pass `--open`, which uploads the comparison. OpenAPI 3.2 exists, and code generators do not yet support it. For Go types from the document, oapi-codegen is maintained (2.8.0); only its release status was checked. |
| Authoring HTTP interfaces | TypeSpec, optional | Emits OpenAPI 3.0 unless configured otherwise. Its 1.0 covers the compiler and the OpenAPI and JSON Schema emitters; client, server, and Protobuf emitters are previews. It has no breaking-change check; run oasdiff on its output. |
| SQL access from Go | sqlc (**ran**, 1.31.1) | `sqlc generate`; `sqlc diff` exits 1 when generated code is stale. `sqlc vet` passes unless rules are configured. PostgreSQL and MySQL are stable, SQLite is beta. Its online documentation describes the main branch, not the release. |
| Outside input in TypeScript | Zod 4 | A library; a failure shows up through `tsc --noEmit` and tests. Minor releases have tightened validation. |
| Outside input in Python | pydantic 2, strict mode | The default mode coerces: `'123'` passes an `int` field. Turn on strict per model, per field, or per call. |
| Configuration | CUE | `cue vet`. Still 0.x, with breaking changes in minor releases. Export to JSON Schema and to Go types is experimental. |

Go has no tagged unions. An enumeration there is a defined type with constants, and
exhaustive matching needs a linter.

## Design-level model checking

| Choice | When | What to know |
|---|---|---|
| Quint (**ran**, 0.33.0) | Default for a new model | `quint run` samples; `quint verify` checks every execution up to 10 steps by default; `--backend tlc` is exhaustive on a finite model. Exit code is 0 or 1 only, so read the output to tell a violation from another error. `--out-itf` writes the trace as JSON. Its repository ships agent skills; use them when installed. |
| TLA+ with TLC | The project already has TLA+, or distinct exit codes matter | Exit 12 for an invariant violation, 13 for liveness, 11 for deadlock. The stable release is from 2024; JSON traces need the rolling pre-release. It is a jar with no Homebrew formula. Its documentation includes a trace-validation guide for checking code logs against a model. |
| P | Message-passing systems written as state machines | `p check` samples schedules. "Found 0 bugs" is not a proof. Needs .NET. |

Do not add Alloy to a project. Its `check` exits 0 when it finds a counterexample
unless the command states an expectation, running it without a subcommand opens a
window, and it has had few changes in the past year.

Measured for Quint on a three-account example: `quint run` 0.3 seconds;
`quint verify` 7 seconds to find a violation and 18 seconds to pass.

## Code-level verification

| Choice | When | What to know |
|---|---|---|
| Kani (**ran**, 0.68.0) | Rust; bounds, overflow, `unsafe` | `cargo kani`, under a second on small functions. Reports overflow and out-of-bounds without being asked. Loops need a bound; a bound that is too small fails instead of passing. Function contracts and counterexample playback are behind `-Z` flags. Concurrent code is compiled as sequential. `cargo kani list` shows every harness. |
| Verus | Rust; a small core that must hold for every input | Weekly releases with no compatibility promise. `--no-cheating` rejects `assume`, `admit`, and external bodies. It reports which condition failed, not a counterexample. |
| Lean 4 (**ran**, 4.34.1) | A reference model with proved properties | `lake build --wfail`; without `--wfail`, a proof containing `sorry` builds successfully. `#print axioms <theorem>` shows `sorryAx` and any added axiom. Pin `lean-toolchain`. Its support for verifying imperative code is being replaced between 4.34 and 4.35. |

Do not add Dafny to a project: its last stable release is from August 2025. Gobra
describes itself as a prototype. Go, TypeScript, and Python have no code-level
verifier fit for routine use.

## Installs and downloads

Ask before each of these.

| Tool | Size | Side effects observed |
|---|---|---|
| Quint | A few MB from npm | The first `quint run` or `quint test` downloads an evaluator of about 1 MB into `~/.quint`. The first `quint verify` downloads Apalache, 192 MB, and needs JDK 21 or later. Behind a proxy the download fails unless `NODE_USE_ENV_PROXY=1` is set. |
| Kani | 352 MB plus a pinned Rust nightly | `cargo kani setup` sets that nightly as the rustup default and updates rustup itself. Restore the previous default afterwards. |
| Lean | 2.7 GB on disk for one toolchain | elan downloads a toolchain the first time a project that names it is built. |
| Verus | 428 MB archive | Not installed during verification of this file. |
