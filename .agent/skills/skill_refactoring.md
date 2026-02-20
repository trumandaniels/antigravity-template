# `skill_refactoring.md`

## 1. Meta-Information

* **Skill Name:** Refactoring & Cleanup (Cognitive Load Reduction)
* **Phase:** 8 / 12 (Optimization & Sustainability)
* **Objective:** Enhance the internal structure of the verified code to maximize readability, maintainability, and performance without altering its external behavior.
* **Prerequisites:** A passing, hardened test suite from `skill_test_verification.md` and the `Verified_Code_Payload`.

## 2. Core Philosophy

Refactoring is not about adding features; it is about paying down technical debt. An agentic coder must prioritize **Cognitive Earnestness**—making the code's intent so obvious that a junior engineer could debug it at 3 AM. We adhere to the **Red-Green-Refactor** mantra, where the "Green" (the tests) acts as an immovable guardrail. If a refactor causes a test to fail, the refactor is objectively wrong.

## 3. Execution Protocol

To optimize the implementation without regression, the agent must execute these operations:

### Step 3.1: Code Smell Detection

* **Action:** Scan the `Verified_Code_Payload` for architectural anti-patterns.
* **Focus:** * **DRY (Don't Repeat Yourself):** Identify near-identical logic blocks that can be unified.
* **Complexity:** Look for deeply nested conditionals (Arrow Anti-pattern) or "God Functions" that exceed 30 lines.
* **Naming:** Identify variables with vague names (e.g., `data`, `temp`, `res`) and replace them with domain-specific nomenclature.



### Step 3.2: Behavior-Preserving Transformations

* **Action:** Apply surgical improvements to the code.
* **Mechanism:**
* **Extraction:** Move complex logic into small, private helper functions with descriptive names.
* **Simplification:** Replace complex `if/else` chains with guard clauses and early returns.
* **Standardization:** Ensure the code uses the latest idiomatic features of the language (e.g., Python list comprehensions, f-strings, or `contextlib`).


* **Validation:** The agent must not change method signatures or public API contracts established in Step 2.

### Step 3.3: Performance Optimization (Pragmatic)

* **Action:** Identify "low-hanging fruit" performance bottlenecks.
* **Focus:** * Reduce O(n²) operations to O(n) where possible (e.g., using sets for lookups instead of lists).
* Eliminate redundant API calls or database queries identified during implementation.
* Ensure resource handles (files, sockets) are managed via context managers to prevent leaks.



### Step 3.4: Dead Code & Artifact Removal

* **Action:** Purge the codebase of "scaffolding."
* **Focus:** Remove all `print()` statements used for debugging, commented-out code blocks, and unused imports that may have survived Step 4.
* **Validation:** Ensure that "TODO" comments are either addressed or converted into formal issue tracking requirements.

### Step 3.5: Recursive Verification

* **Action:** Re-execute the `Verified_Test_Suite` from Step 7 after every significant structural change.
* **Validation:** A refactor is only successful if the mutation-tested suite remains 100% green. If a test fails, the agent must `git checkout` (or revert state) to the last known good version and attempt a smaller, more atomic transformation.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate an `Optimized_Code_Payload` to be passed to Step 9 (Regression Check). This payload MUST include:

1. **The Refactored Source Code:** Clean, idiomatic, and highly readable logic.
2. **Refactoring Log:** A concise summary of changes (e.g., "Extracted AST parsing to `_parse_node` helper; replaced nested loops with set intersection").
3. **Green-State Confirmation:** A timestamped log showing the tests still pass.

## 5. Failure Modes & Fallback Strategies

* **Feature Creep disguised as Refactoring:**
* *Trigger:* The agent adds a "convenience" parameter or a new error case during refactoring.
* *Fallback:* Reject the change. Refactoring is strictly a 1:1 behavioral mapping. Any new features must be sent back to Step 2 (Design).


* **The "Clever Code" Trap:**
* *Trigger:* The agent replaces readable logic with a complex, one-liner lambda or a cryptic regex to "save space."
* *Fallback:* Enforce a **Readability Score**. If the new code increases the cognitive load (measured by nesting depth or variable naming clarity), revert the change.


* **Breaking the Test Guardrail:**
* *Trigger:* The agent modifies a private method that a test was unfortunately "reaching into" to mock.
* *Fallback:* The agent must either update the test fixture to match the new internal structure or—preferably—refactor the test to only use public interfaces, ensuring the refactor is truly decoupled from the test implementation.