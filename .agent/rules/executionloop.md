---
trigger: always_on
---

# Standard Execution Loop

**Objective:** This document defines the immutable, step-by-step execution loop for all code modifications. To guarantee high-quality, production-ready output, the agent must strictly follow these sequential phases. Do not skip steps.

## 1. Context & Dependency Analysis

Before modifying any files, you must establish a comprehensive mental model of the target environment. Identify upstream dependencies, downstream impacts, and the specific architecture of the modules involved. Failing to understand the context leads to fragmented, brittle solutions.

* You must use the context-analysis skill

## 2. Design & Planning

Never write code without a blueprint. Draft the architectural approach, specify the data structures, and define the algorithms to be used. Anticipate potential bottlenecks and ensure the proposed design aligns with the existing system architecture.

* You must use the design-planning skill

## 3. Implementation

Translate the approved design into code. Focus on clean, idiomatic syntax appropriate for the language environment. Ensure variable names are highly descriptive, functions do one thing well, and the logic is straightforward rather than unnecessarily clever.

* You must use the implementation skill

## 4. Static Analysis & Type Checking

Immediately subject the new code to automated scrutiny. Run configured linters and strict type checkers to catch syntax errors, type mismatches, and stylistic violations before executing the code. Fix all flagged warnings.

* You must use the static-analysis skill

## 5. Output Verification

Isolate the new logic and verify that it compiles or interprets without throwing fatal runtime errors. This is a foundational sanity check to ensure the script executes from top to bottom before moving to complex behavioral testing.

* You must use the output-verification skill

## 6. Test Generation

Write comprehensive unit and integration tests for the implemented logic. You must cover the primary execution paths, known edge cases, and failure states. Good tests define the exact boundaries of the expected behavior.

* You must use the test-generation skill

## 7. Test Verification

Run the newly generated tests against the implementation. Verify not only that the passing tests pass, but that the tests are actually capable of failing if the underlying logic is broken. A test that always passes is a liability.

* You must use the test-verification skill

## 8. Refactoring & Cleanup

Review the working code to optimize for readability, performance, and DRY (Don't Repeat Yourself) principles. Simplify complex conditionals and remove any redundant logic or dead code introduced during the implementation phase. Do not alter the core behavior.

* You must use the refactoring skill

## 9. Regression Check

Execute the entire repository's test suite. The new implementation must seamlessly integrate into the broader codebase without breaking existing, unrelated functionality. A localized fix is invalid if it causes a systemic regression.

* You must use the regression-check skill

## 10. Self-Critique & Diff Review

Perform a final, holistic review of the complete diff. Compare the actual changes against the original plan from Step 2. Look critically for security vulnerabilities, unhandled exceptions, or logical leaps. Be your own strictest reviewer.

* You must use the diff-review skill

## 11. Documentation Update

Code is read far more often than it is written. Update all relevant docstrings, inline comments, and architectural READMEs. Explain *why* a decision was made, not just *what* the code does. This is crucial for long-term maintainability.

* You must use the documentation skill

## 12. State Checkpoint & Commit

Package the finalized, verified changes into a logical, atomic commit. Write a clear, descriptive commit message that summarizes the problem, the solution, and any notable side effects. Save the state securely.

* You must use the state-checkpoint skill