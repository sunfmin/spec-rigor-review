---
name: spec-rigor-review
description: >
  Load this FIRST, before reading any code, whenever the user asks which rules in a
  codebase should be enforced by a schema, a type, a model checker, or a proof instead
  of by convention; where illegal states can be built; whether a state machine,
  protocol, or concurrent design has holes; whether existing schema checks, models, or
  proofs actually guard anything; or how to refactor code so these tools can be applied.
  Also triggers on "spec rigor review", "哪些规则该变成硬约束", "让非法状态无法表示",
  "这个状态机有没有漏洞", "哪里值得上形式化验证", "这些证明到底证了什么". It reviews existing
  code for candidates, audits the checks already present, finds the refactors that
  unblock them, reports ranked findings, and writes only what the user then picks.
  Not for property, fuzz, or mutation testing (test-rigor-review), and not for general
  design review (complexity-review).
---

# Spec rigor review

A rule that lives in a comment, a naming convention, or someone's memory is enforced
by nobody. This skill reviews an existing codebase for rules that a machine could
enforce, and for the refactors that make that possible.

- **Schema and types** — one source describes the shape of data; the compiler and
  generated code reject anything else.
- **Design-level model checking** — a small model of a state machine or protocol; a
  checker explores its executions and returns a counterexample.
- **Code-level verification** — a proof or bounded check that a piece of code meets
  its specification for every input.

## Process

1. **Fix the scope.** Use the path, package, or diff the user named. If they named
   nothing, pick the packages that hold business rules, state transitions, and
   boundaries with other systems, and say which ones you picked. Do not review a whole
   repository by default.
2. **Read what exists.** Detect the language, the schemas and generators in use, and
   any models or proofs. Find the command that runs each check and where it is wired
   in. Run a check only if it changes nothing and downloads nothing, and record its
   exit code. `techniques/tools.md` says how each tool behaves, including what it
   downloads and what it returns on a violation.
3. **Find candidates** with `techniques/schema-candidates.md`,
   `techniques/model-candidates.md`, and `techniques/proof-candidates.md`.
4. **Audit existing checks** with `techniques/audit-existing.md`. A check that cannot
   fail is worse than no check, because it reports safety that is not there.
5. **Find blockers** with `techniques/refactors.md`.
6. **Rank** within each table by (cost of a violation) × (chance this check catches
   one) ÷ effort. Money, permissions, state machines, and boundaries with other
   systems rank first. Prefer the cheapest layer that can express the rule: a type
   before a schema check, a schema check before a model, a model before a proof.
7. **Report** in the format below, then stop. The review is read-only: do not edit
   files, install tools, or trigger downloads until the user picks findings.
8. **Write what the user picked**, following `techniques/writing-checks.md`. Handle
   every counterexample and every unproven goal with `techniques/triage.md`.

## Report format

Open with **What exists**: language, schemas and generators, models and proofs, the
command that runs each check, and whether it is wired into CI.

Then these sections, each ranked with the most valuable finding first. Give every
finding an ID (`S1`, `V1`, `W1`, `R1`). When a section has no findings, say so in one
line. Use a table where every field fits in a short cell, as it usually does for weak
existing checks; otherwise give each finding a labelled block.

- **Schema and type candidates** (`S`) — location, the rule in one sentence, the
  evidence for it, how it is enforced today, the proposed enforcement, effort (S/M/L).
- **Verification candidates** (`V`) — location, the invariant in one sentence, the
  evidence for it, the tool, the guarantee it would give (sampled, bounded, exhaustive
  on a finite model, or proven), effort. A candidate that needs a refactor first says
  "needs R1"; its effort excludes the refactor.
- **Weak existing checks** (`W`) — location, which audit check failed, the offending
  line or command, the smallest fix. One row per failed check.
- **Refactors** (`R`) — location, blocker, proposed change, the checks it unlocks,
  effort.
- **Not covered** — rules left out for lack of evidence and what would settle each,
  rules better served by a property or fuzz test (name `test-rigor-review`), and
  defects you noticed that none of these tools would catch.

Locations are `file:line`, or a line range when the finding spans one. Mark every
counterexample, and every claim that a check cannot fail, as predicted or confirmed;
it is confirmed only if you ran it. To confirm one during the review, work in a
throwaway copy outside the repository.

End with the three things to do first and the reason for each. Findings that depend on
each other, such as a refactor and the check it unlocks, count as one.

## Rules that hold throughout

- **Every rule needs evidence.** Take it from documentation, names and signatures,
  assertions already in the code, existing tests, or a business rule the user stated.
  The user owns the rules. Never invent one, and never weaken one to make a check
  pass. When a stated rule can be read two ways, report both readings and ask which
  one is meant.
- **A finding is a rule a person can check in one sentence.** If you cannot state it
  that briefly, it is not ready to report.
- **A check counts only after it has been seen to fail.** For a check you write,
  break the rule once on purpose and record the command, the non-zero exit code, and
  the output. For a check you audit, try to make it fail in a throwaway copy.
- **State the guarantee exactly.** Sampling is not exhaustive, a bound is not a proof,
  and a proof covers the written specification, not the intent behind it.
- **Say how a model stays tied to the code.** If the link is manual, say so.
- **Learn each tool's syntax from the project and its documentation.** Do not write
  syntax from memory; these tools change every month.

## Before stopping

Check the work against each technique file you used. Anything in those files that you
skipped gets a stated reason under **Not covered**.

- `techniques/schema-candidates.md` — signals that a rule belongs in a schema or type
- `techniques/model-candidates.md` — what to model and which invariants to state
- `techniques/proof-candidates.md` — when a code-level proof is worth its cost
- `techniques/audit-existing.md` — ways an existing check passes without checking
- `techniques/refactors.md` — blockers and the smallest change that removes each
- `techniques/writing-checks.md` — wiring, downloads, proving a check can fail
- `techniques/triage.md` — verdicts for counterexamples and unproven goals
- `techniques/tools.md` — how each tool behaves, which to choose, what was verified when
