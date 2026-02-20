# `skill_refactoring.md`

## 1. Meta-Information

* **Skill Name:** Refactoring & Cleanup (Structural Optimization)
* **Phase:** 8 / 12 (Optimization)
* **Objective:** Restructure the verified implementation to minimize cognitive load, enforce DRY (Don't Repeat Yourself) principles, and reduce cyclomatic complexity—all without altering the system's external behavior or invalidating the test suite.
* **Prerequisites:** Successful completion of `skill_test_verification.md` (a robust, passing test suite that guarantees falsifiability).

## 2. Core Philosophy

In production-grade engineering, getting the code to work is only half the battle; the other half is ensuring the code can be maintained by others (or by an autonomous agent in a future context window) six months down the line. Refactoring is a strict discipline of semantics-preserving transformations. It is explicitly *not* the time to add new features or fix lingering bugs.

For an agentic workflow, refactoring serves a dual purpose: it improves human readability, and it optimizes the token density of the codebase, ensuring that future prompts analyzing this file waste zero context on redundant logic, dead code, or monolithic functions.

## 3. Execution Protocol

To execute a safe and effective refactoring pass, the agent must strictly adhere to the following sequence, leaning heavily on the test suite generated in Step 6 as a safety net.

### Step 3.1: The Safety Net Check

* **Action:** Before touching a single line of code, the agent must verify that the test suite is currently in a "green" state.
* **Focus:** Refactoring a broken system is a fatal error. If the tests do not pass, the agent must immediately halt and revert to Step 3 (Implementation). The tests represent the immutable contract of the system; refactoring is merely reorganizing the internals of that contract.

### Step 3.2: Cyclomatic Complexity Reduction (Extract Method)

* **Action:** Scan the implementation for functions that violate the Single Responsibility Principle (SRP) or contain deeply nested conditionals (`if` statements inside `for` loops inside `try` blocks).
* **Focus:** Isolate and extract independent logic blocks into private helper methods. For instance, if a module acting as a code explainer contains a monolithic function that both traverses an Abstract Syntax Tree (AST) to analyze snippets and subsequently formats the markdown output, the agent must split these into two pure, isolated functions: one for analysis, one for presentation.

### Step 3.3: DRY Enforcement & Deduplication

* **Action:** Identify repeated patterns, duplicated strings, or identical logic scattered across multiple branches.
* **Focus:** Consolidate repeated logic into centralized utilities, loops, or shared constants. If the same validation check is performed at the top of three different methods, extract it into a decorator or a dedicated validation function.

### Step 3.4: Semantic Naming & Type Hinting Precision

* **Action:** Audit all variables, classes, and function signatures.
* **Focus:** Replace generic names (`data`, `temp`, `processor`, `result`) with highly specific, domain-accurate identifiers (`ast_node_mapping`, `fallback_token_limit`, `snippet_parser`). Ensure strict Python type hints (e.g., `Dict[str, Any]` rather than just `dict`) are correctly applied to all inputs and outputs to self-document the data structures.

### Step 3.5: Dead Code Elimination

* **Action:** Remove any unused imports, commented-out legacy code, unreachable `else` blocks, or redundant variables that were created during the initial "trial and error" phase of Step 3.
* **Focus:** The codebase must be stripped down to its essential logic. Every line of code is a liability that must be read, parsed, and maintained; if it is not executed, it must be deleted.

### Step 3.6: The Continuous Verification Loop

* **Action:** After *every* isolated structural change (e.g., renaming a variable, extracting a function), the agent must instantly re-run the test suite.
* **Focus:** Refactoring is a micro-iterative process. If a change turns the test suite "red," the agent must immediately undo the last atomic action. Never pile multiple refactoring changes on top of a failing test suite.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Refactored_State_Payload` to be passed to Step 9 (Regression Check). This payload MUST include:

1. **The Optimized Source Code:** The final, cleaned implementation files.
2. **Refactoring Diff:** A clean unified diff showing the exact structural changes made.
3. **Green Test Trace:** The standard output proving that the test suite still passes at 100% after all structural transformations.

## 5. Failure Modes & Fallback Strategies

* **Feature Creep / Scope Bloat:**
* *Trigger:* While refactoring, the agent notices an edge case that wasn't handled and attempts to add new logic to fix it, altering the system's behavior.
* *Fallback:* Strict behavioral enforcement. If the test suite fails because the agent *changed* the expected output (rather than preserving it), the agent must revert the code, document the missed edge case as a separate issue, and maintain the current verified contract.


* **"Spaghetti" Abstraction (Over-Refactoring):**
* *Trigger:* The agent breaks down a simple 15-line function into five separate 3-line functions scattered across multiple files, drastically increasing cognitive load and making the execution path impossible to follow.
* *Fallback:* Implement a fragmentation threshold. Refactoring should clarify, not obscure. If a function fits entirely on one screen and handles a single intuitive concept, the agent should leave it intact.


* **The Broken Contract (Regression Injection):**
* *Trigger:* The agent alters a variable name or data structure that is implicitly expected by an external, un-mocked dependency, causing a silent failure that the isolated unit tests miss.
* *Fallback:* This highlights a failure in Step 6's test coverage. The agent must revert the change, write a new failing test that captures this previously hidden contract, and only then re-attempt the refactor.