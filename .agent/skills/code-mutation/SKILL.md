---
name: code-mutation
description: Structured execution loop for all code modification tasks. Use this skill whenever the user asks to write, edit, refactor, debug, or otherwise mutate code in any file. Triggers on: implementing features, fixing bugs, refactoring, adding tests, editing scripts, or any task that results in changes to source files.
---

# Code Mutation Execution Loop

## Tier Selection

Before starting, classify the change and select the appropriate tier. State the chosen tier explicitly.

| Tier | When to Use | Phases Required |
|------|-------------|-----------------|
| **Micro** | Typo, comment, config value, one-liner, variable rename | 3 → 4 → 5 → 7 → 10 |
| **Standard** | Bug fix, small feature, isolated refactor, targeted edit | 1 → 2 → 3 → 4 → 5 → 7 → 8 → 10 → 12 |
| **Full** | New module, new API surface, cross-cutting refactor, anything with broad blast radius | All 12 phases |

When in doubt, go one tier up. Do not skip phases within the selected tier.

## Tier Escalation Protocol

Tier selection happens at the start, but re-evaluate if either condition is met mid-task:

| Escalation Trigger | From | To |
|--------------------|------|----|
| Change touches more than 1 file, or existing tests break | Micro | Standard |
| Any public API surface, external contract, or cross-module type signature changes | Micro or Standard | Full |

When escalating, resume from the earliest skipped phase of the new tier — do not restart from Phase 1 if context analysis was already completed adequately.

## Phase Failure Rollback Map

When a phase fails, route back to the correct prior phase — never skip forward hoping the problem resolves itself.

| Failing Phase | Root Cause | Roll Back To |
|---------------|------------|--------------|
| Phase 4 (Static Analysis) | Implementation defect | Phase 3 |
| Phase 5 (Output Verification) | Runtime initialization error | Phase 3 |
| Phase 7 (Test Verification) | Tests fail on existing suite | Phase 3 (fix code) |
| Phase 7 (Test Verification) | Tests are inadequate / tautological | Phase 6 (Full tier only) |
| Phase 9 (Regression Check) | Systemic breakage detected | Phase 3 |
| Phase 2 (Design) — discovery during Phase 3 | Blueprint is fundamentally unimplementable | Phase 2 |

If the same phase fails three times, halt and escalate to the user — do not loop indefinitely.

---

## Phases

### Phase 1 — Context & Dependency Analysis
*(Standard, Full)*

Map upstream dependencies, downstream consumers, and architectural patterns before touching any file.

- Follow: [context-analysis skill](../context-analysis/SKILL.md)

### Phase 2 — Design & Planning
*(Standard, Full)*

Draft the implementation blueprint — data structures, control flow, edge cases — before writing code.

- Follow: [design-planning skill](../design-planning/SKILL.md)

### Phase 3 — Implementation
*(All tiers)*

Translate the design into clean, idiomatic, single-responsibility code. No improvisation beyond the blueprint.

- Follow: [implementation skill](../implementation/SKILL.md)

### Phase 4 — Static Analysis & Type Checking
*(All tiers)*

Run linters and type checkers immediately. Fix all flagged warnings before proceeding.

- Follow: [static-analysis skill](../static-analysis/SKILL.md)

### Phase 5 — Output Verification
*(All tiers)*

Verify the code executes from top to bottom without fatal runtime errors.

- Follow: [output-verification skill](../output-verification/SKILL.md)

### Phase 6 — Test Generation
*(Full only)*

Write unit and integration tests covering primary paths, edge cases, and failure states.

- Follow: [test-generation skill](../test-generation/SKILL.md)

### Phase 7 — Test Verification
*(All tiers — run existing tests; Full also runs newly generated tests from Phase 6)*

Run the tests. Confirm they pass and are capable of failing when logic is broken.

- Follow: [test-verification skill](../test-verification/SKILL.md)

### Phase 8 — Refactoring & Cleanup
*(Standard, Full)*

Optimize for readability and DRY principles. Remove dead code. Do not alter behavior.

- Follow: [refactoring skill](../refactoring/SKILL.md)

### Phase 9 — Regression Check
*(Full only)*

Run the full repository test suite to ensure no existing functionality is broken.

- Follow: [regression-check skill](../regression-check/SKILL.md)

### Phase 10 — Diff Review
*(All tiers)*

Review the complete diff against the original plan. Look for security issues, unhandled exceptions, and logical gaps.

- Follow: [diff-review skill](../diff-review/SKILL.md)

### Phase 11 — Documentation Update
*(Full only)*

Update docstrings, inline comments, and READMEs. Explain *why*, not just *what*.

- Follow: [documentation skill](../documentation/SKILL.md)

### Phase 12 — State Checkpoint & Commit
*(Standard, Full)*

Package changes into an atomic commit with a clear, descriptive message.

- Follow: [state-checkpoint skill](../state-checkpointing/SKILL.md)
