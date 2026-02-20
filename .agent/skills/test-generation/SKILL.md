---
name: test-generation
description: Writes comprehensive unit and integration tests covering primary paths and edge cases.
tags: [testing, pytest, test-generation]
---

# Test Generation

## 1. Meta-Information

* **Skill Name:** Test Generation (Contract Verification)
* **Phase:** 6 / 12 (Validation & Defense)
* **Objective:** Synthesize a comprehensive suite of unit and integration tests that prove the implementation strictly satisfies the architectural contracts, handles predicted edge cases, and fails gracefully under stress.
* **Prerequisites:** Successful completion of `skill_output_verification.md` (code is executable) and read access to the original `skill_design_planning.md` output (the `Design_Blueprint`).

## 2. Core Philosophy

Tests are the executable documentation of system boundaries. When an agent constructs a test suite, it must operate with an adversarial mindset. The goal is not to prove the code works; the goal is to systematically attempt to break it. In the context of agentic coding frameworks—where non-deterministic outputs from LLMs are common—tests must enforce hermetic boundaries. External calls must be aggressively mocked, and data transformations (like parsing a complex code snippet into an explanation object) must be validated against rigid Pydantic schemas.

## 3. Execution Protocol

To generate a robust, production-ready test suite, the agent must execute the following sequential operations:

### Step 3.1: Blueprint-to-Test Mapping (Contract Driven)

* **Action:** Ingest the original `Design_Blueprint` (Step 2). For every interface contract, required input, and expected output defined in the blueprint, generate a corresponding test signature.
* **Focus:** The agent must *not* read the implementation source code during this initial mapping. This prevents the agent from anchoring its expectations to potentially flawed logic. The tests must reflect what the system *should* do, not what it currently *does*.

### Step 3.2: Equivalence Partitioning & Boundary Value Analysis

* **Action:** Define the specific datasets that will be passed into the tests.
* **Mechanism:** Generate three distinct classes of data:
1. **The Happy Path:** Standard, well-formed inputs (e.g., a perfectly formatted Python function passed to a code explainer tool).
2. **Boundary Values:** Inputs at the extreme edges of allowed constraints (e.g., maximum token limits, empty strings, massive nested dictionaries).
3. **Invalid Inputs:** Deliberately malformed data designed to trigger the specific exception handlers defined in the pre-mortem of Step 2.



### Step 3.3: Mocking & Dependency Isolation

* **Action:** Construct deterministic mocks for all external dependencies, side-effects, and non-deterministic systems.
* **Focus:** If the target code interacts with an external LLM, an API, or a database, the agent must write standard `unittest.mock` (or `pytest-mock`) fixtures. The test suite must be able to run entirely offline in a hermetic environment. Network calls during unit tests are strictly prohibited.

### Step 3.4: Test Implementation

* **Action:** Write the executable test functions using the environment's standard testing framework (e.g., `pytest`).
* **Focus:** Enforce the "Arrange-Act-Assert" (AAA) pattern for every single test.
* *Arrange:* Setup the mock data and instantiate the class.
* *Act:* Invoke the target method.
* *Assert:* Verify the output type, value, and any expected state mutations.



### Step 3.5: Integration Pipeline Assembly

* **Action:** If the module is part of a larger agentic chain (e.g., a LangChain sequence or LangSmith traceable workflow), generate at least one localized integration test.
* **Focus:** Verify that data flows correctly from the mock input of the *upstream* node, through the newly implemented module, and outputs a structure cleanly readable by the *downstream* node.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Test_Suite_Payload` to be passed to Step 7 (Test Verification). This payload MUST include:

1. **The Test Source Code:** The executable `test_*.py` files containing the full suite.
2. **Mock Fixtures:** Any generated JSON payloads, dummy files, or mock classes required to run the tests.
3. **Coverage Target:** A stated expectation of what the test suite is meant to cover (e.g., "This suite targets 100% branch coverage for the parsing logic and explicitly tests the `ContextOverflowError` exception").

## 5. Failure Modes & Fallback Strategies

* **The Tautological Test (The "Mirror" Anti-Pattern):**
* *Trigger:* The agent reads a poorly implemented function that returns `True` instead of a calculated value, and writes a test that simply asserts `assertEqual(func(), True)`.
* *Fallback:* Introduce a strict separation of context. The agent generating the tests must rely primarily on the `Design_Blueprint` rather than the implementation file. If the implementation diverges from the blueprint, the test *must* fail in Step 7.


* **Over-Mocking (The "Empty Shell" Test):**
* *Trigger:* The agent mocks out the actual system under test, resulting in a test that only verifies the mock framework works.
* *Fallback:* Implement a strict architectural constraint: The primary class or function being tested must *never* be mocked. Only its upstream parameters and downstream external dependencies may be patched.


* **Non-Deterministic Assertions:**
* *Trigger:* The agent attempts to test an LLM output by asserting exact string matching (e.g., `assert response == "The code does X"`), which fails instantly due to token variation.
* *Fallback:* Force the agent to write structural assertions instead of semantic ones. Test that the output is of type `str`, contains specific required keys if it's JSON, or falls within a certain length constraint, rather than relying on exact verbatim matches.