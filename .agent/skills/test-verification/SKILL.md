---
name: test-verification
description: Executes tests against the implementation to ensure they accurately catch broken logic.
tags: [testing, validation, qa]
---

# Test Verification

## 1. Meta-Information

* **Skill Name:** Test Verification (Falsifiability & Stress Analysis)
* **Phase:** 7 / 12 (Validation & Defense)
* **Objective:** Execute, audit, and rigorously challenge the generated test suite to ensure it effectively guarantees the system contracts, provides meaningful coverage, and—most importantly—is actually capable of failing when the underlying implementation is compromised.
* **Prerequisites:** Successful completion of `skill_implementation.md` (the target code) and `skill_test_generation.md` (the executable test suite).

## 2. Core Philosophy

In rigorous software engineering, a test suite that has never been observed to fail is fundamentally untrustworthy. Tests are not merely checklists; they are tripwires. The core philosophy of this phase relies on the scientific principle of falsifiability. If a test passes, we must prove that its passing is a direct result of correct implementation logic, not an artifact of tautological assertions, excessive mocking, or suppressed exceptions. Furthermore, at scale, non-deterministic "flaky" tests degrade pipeline trust and developer velocity. Therefore, this agentic skill treats the test suite with deep skepticism, subjecting it to mutation analysis, coverage audits, and environmental stress before certifying the code for production integration.

## 3. Execution Protocol

To certify the test suite as a valid defensive boundary, the agent must execute the following strictly ordered operations:

### Step 3.1: The Baseline Execution (The Green Phase)

* **Action:** Execute the test suite in a clean, isolated environment against the current implementation.
* **Focus:** Verify that all unit and integration tests pass cleanly on the first run. Any failure at this stage indicates either a flawed implementation (Step 3) or an overly rigid test generation (Step 6). If the baseline fails, the agent must halt and explicitly route the execution loop back to the appropriate prior step.

### Step 3.2: Structural Coverage & Branch Audit

* **Action:** Execute the test suite wrapped in a coverage analysis tool (e.g., `coverage.py` or `pytest-cov`) to generate a quantitative execution map.
* **Focus:** Do not rely solely on line coverage, which is a vanity metric. The agent must enforce *branch* and *condition* coverage. Every `if/else` block, `try/except` handler, and loop termination condition must be actively traversed by the test data.
* *Requirement:* Ensure exception handlers defined during the design phase are explicitly triggered by the tests.

### Step 3.3: Falsifiability via Mutation Testing (The Red Phase)

* **Action:** Systematically inject localized, intentional regressions into the implementation source code (mutations) and re-run the test suite.
* **Focus:** The agent must verify that breaking the code *breaks the test*. For example, when validating a test suite for a code explainer that relies on parsing an Abstract Syntax Tree (AST), changing a `Return` node to a `Pass` node in the source logic must trigger an immediate assertion failure in the test suite.
* *Mechanism:* Invert boolean conditionals (`==` to `!=`), increment/decrement integer boundaries, or comment out variable assignments. If the test suite remains "green" despite these mutations, the tests are dead code and must be rewritten.

### Step 3.4: Determinism & Flakiness Validation

* **Action:** Stress-test the suite to guarantee hermeticity and strict determinism.
* **Focus:** Run the test suite multiple times (e.g., 10x) using randomized execution orders (via tools like `pytest-randomly`). Verify that no test relies on the residual state of a previous test. Ensure all mocks instantiated in Step 6 are properly tearing down, and no localized I/O operations are leaking across the test runner boundary.

### Step 3.5: The Assertion Quality Audit

* **Action:** Perform a static scan of the test source code to evaluate the density and specificity of the assertions.
* **Focus:** Reject tests that execute complex logic but only assert trivial outcomes (e.g., `assert result is not None`). Ensure the assertions are checking precise structural contracts, state mutations, and accurate data transformations.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Verification_Certificate_Payload` to be passed to Step 8 (Refactoring & Cleanup). This payload MUST include:

1. **Execution Trace Log:** The raw standard output of the baseline test run, proving a 100% pass rate.
2. **Coverage Matrix:** A detailed mapping of branch and line coverage, specifically highlighting any edge cases that were intentionally excluded with justification.
3. **Mutation Score / Falsifiability Proof:** A log documenting at least one intentional mutation introduced to the core logic, and the corresponding stack trace of the test suite correctly catching the error and failing.

## 5. Failure Modes & Fallback Strategies

* **The "Evergreen" Test (False Negative Failsafe):**
* *Trigger:* During the mutation phase (Step 3.3), the agent deliberately breaks the implementation logic, but the test suite continues to pass.
* *Fallback:* The agent must flag the specific test as "Tautological." Execution must revert to Step 6 (`skill_test_generation.md`) with strict instructions to rewrite the test's `Assert` phase to explicitly check the mutated output variable rather than a mocked or hardcoded constant.


* **The "Tourist" Test (Execution Without Verification):**
* *Trigger:* The coverage audit (Step 3.2) shows 100% line coverage, but the assertion audit (Step 3.5) reveals that the tests are simply calling functions without verifying the returned state or data structures.
* *Fallback:* Mandate a minimum assertion-to-action ratio. The agent must inject strict type-checking assertions and boundary-value validations into the existing test blocks before proceeding.


* **State Leakage / Flakiness (The Order Dependency):**
* *Trigger:* Tests pass when run sequentially but fail when randomized in Step 3.4, indicating shared state or improper teardown of mocks.
* *Fallback:* The agent must inject strict `setUp` and `tearDown` methods (or heavily scoped `pytest` fixtures) to force state resets. No test is permitted to write to a shared disk or global variable without an explicit, guaranteed cleanup block.