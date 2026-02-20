# `skill_test_verification.md`

## 1. Meta-Information

* **Skill Name:** Test Verification (Mutation & Quality Audit)
* **Phase:** 7 / 12 (Validation & Defense)
* **Objective:** Execute the newly generated test suite against the implementation. Prove that the tests pass under normal conditions, achieve the required coverage, and—most importantly—fail when intentional faults are injected into the source code.
* **Prerequisites:** Successful completion of `skill_test_generation.md`, resulting in an executable `Test_Suite_Payload`. Access to the `Verified_Code_Payload` (Step 5) and an isolated execution environment.

## 2. Core Philosophy

A test suite is a structural contract. If you can alter the logic of the implementation without breaking the contract, the contract is void. This phase strictly enforces **Mutation Testing** principles. The agent must assume its own tests are flawed tautologies until proven otherwise. Code coverage tells you what lines were executed; mutation testing tells you if those lines were actually evaluated.

## 3. Execution Protocol

To definitively prove the validity of the test suite, the agent must execute the following sequential operations:

### Step 3.1: The Baseline Run (The Green Phase)

* **Action:** Execute the `Test_Suite_Payload` against the unmodified `Verified_Code_Payload` using the standard test runner (e.g., `pytest`).
* **Validation:** All tests must pass. If a test fails on the unmodified code, the agent must halt, analyze the traceback, and determine if the implementation (Step 3) or the test (Step 6) is flawed. Do not proceed until the baseline is green.

### Step 3.2: Coverage & Branch Analysis

* **Action:** Re-run the test suite with strict coverage tracking enabled (e.g., `pytest-cov`).
* **Focus:** Verify that the primary execution paths, error handlers, and boundary conditions defined in the `Design_Blueprint` are demonstrably executed.
* **Validation:** Enforce a hard coverage floor (e.g., 90% branch coverage). Reject the suite if critical logical nodes are bypassed.

### Step 3.3: Fault Injection (The Red Phase)

* **Action:** Systematically inject "mutants" (intentional bugs) into the `Verified_Code_Payload`.
* **Mechanism:** The agent must perform localized AST manipulations or text-level substitutions. Examples include:
* Swapping mathematical operators (e.g., changing `+` to `-`).
* Inverting conditionals (e.g., changing `if x == y:` to `if x != y:`).
* Nullifying return values (e.g., replacing `return data` with `return None`).


* **Execution:** Run the test suite against the mutated code.

### Step 3.4: Mutant Eradication Audit

* **Action:** Analyze the test results from the Red Phase.
* **Focus:** At least one test *must* fail for every injected mutant. If the mutated code passes the test suite, the tests are identified as "surviving mutants" and flagged as invalid, tautological, or improperly mocked.
* **Validation:** The agent must isolate tests that allowed mutants to survive and immediately rewrite them to enforce stricter structural assertions.

### Step 3.5: State Reversion & Finalization

* **Action:** Purge all mutated code from the environment. Restore the `Verified_Code_Payload` to its pristine, baseline state.
* **Validation:** Run the test suite one final time to guarantee the environment is stable and no residual faults remain.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Verified_Test_Suite` to be passed to Step 8 (Refactoring & Cleanup). This payload MUST include:

1. **The Hardened Test Code:** The finalized `test_*.py` files that successfully killed all injected mutants.
2. **Coverage Report:** A definitive log proving the required branch and line coverage was achieved.
3. **Mutation Score:** A brief audit log demonstrating which faults were injected and which specific tests caught them.

## 5. Failure Modes & Fallback Strategies

* **The Invincible Test (Tautology Trap):**
* *Trigger:* A mutant is injected (e.g., the function now returns an empty dict instead of populated data), but the test still passes because it only asserts that `type(response) == dict` or merely checks that a mock was called.
* *Fallback:* The agent must discard the failing test. It must review the `Design_Blueprint` to understand the exact expected data shape and rewrite the test using deep, recursive object comparisons (e.g., validating specific keys and values).


* **Flaky Tests (Non-Deterministic Failures):**
* *Trigger:* A test passes on the baseline run, fails, and then passes again without any code changes. This is common when LLMs use `datetime.now()` or uncontrolled random seeds.
* *Fallback:* Flag the test as non-deterministic. The agent must inject a mock for time, seed the random number generator, or rewrite the test to be entirely pure and hermetic.


* **Coverage Blind Spots:**
* *Trigger:* The coverage report reveals that an entire `except` block or edge-case branch is never hit by the test suite.
* *Fallback:* The agent must pause verification, step back to Step 6 (Test Generation), and deliberately construct a malicious input specifically designed to trigger that exact branch before re-attempting Step 7.