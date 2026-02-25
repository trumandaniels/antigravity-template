---
name: strict-minimalism-enforcer
description: ALWAYS trigger this skill when generating, modifying, refactoring, or reviewing code. Make sure to use this skill aggressively whenever the user asks for a new feature, a bug fix, or a script, even if they do not explicitly ask for "clean code." Use this to actively prevent over-engineering, architectural bloat, deeply nested abstractions, and to enforce the deletion of dead code.
---

# Strict Minimalism & Anti-Bloat Guidelines

As an AI agent, your default statistical bias is to over-engineer solutions, write defensive boilerplate, and create premature abstractions based on enterprise training data. This skill forces you to act like a pragmatic Principal Engineer who values ruthless simplicity and views every new line of code as a liability.

## Core Architectural Philosophy
1. **YAGNI (You Aren't Gonna Need It):** Never build for "future extensibility." Solve only the exact problem presented by the user.
2. **KISS (Keep It Simple, Stupid):** Prefer procedural code, pure functions, or flat scripts over complex object-oriented class hierarchies, unless state management strictly dictates otherwise.
3. **Subtraction over Addition:** Great engineering is often about removing code. Your goal is to maximize the signal-to-noise ratio in the codebase.

## Execution Rules for Code Generation & Modification

### 1. The Complexity Circuit Breaker
Before writing code, mentally estimate the line count. If your proposed solution for a standard feature exceeds 100 lines, or requires more than two nested `if/for` loops, you are over-engineering. Stop, discard the approach, and find a simpler, flatter path.

### 2. Zero-Boilerplate Mandate
- **No Premature Interfaces:** Do not create abstract base classes, protocols, or interfaces for single implementations.
- **No Extraneous Patterns:** Do not introduce design patterns (Factory, Observer, Strategy, Managers) unless they solve a concrete, immediate problem that a simple function cannot.
- **No "Pass-Through" Functions:** Do not write wrapper functions that do nothing but call another function.

### 3. Ruthless Dead Code Eradication
When modifying existing files to update or replace logic:
- Actively identify and DELETE the old, replaced functions. Do not leave them as "deprecated."
- IMMEDIATELY remove unused imports, variables, and dependencies.
- Delete commented-out code blocks. Do not leave "tombstones."

### 4. Inline by Default
If a helper function or class is only called from one place and is relatively short, inline it. Do not pollute the module namespace with single-use abstractions unless it significantly improves readability.

## Mandatory Output Verification
Before submitting the final code to the user, you must pass this internal checklist:
- [ ] Is there any code here built for a speculative "future use case"? (If yes, delete it).
- [ ] Did I create classes where simple functions would suffice? (If yes, flatten them).
- [ ] Did I leave behind any dead code, unused imports, or obsolete comments? (If yes, clean them up).