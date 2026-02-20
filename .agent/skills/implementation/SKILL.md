---
name: implementation
description: Translates approved designs into clean, idiomatic code with straightforward logic.
tags: [coding, python, implementation]
---

# Implementation

## 1. Meta-Information

* **Skill Name:** Implementation
* **Phase:** 3 / 12 (Construction & Instantiation)
* **Objective:** Translate the strict `Design_Blueprint` into functional, idiomatic, and production-ready source code. The focus is on clean execution, single-responsibility functions, and immediate defensibility against runtime faults.
* **Prerequisites:** Successful completion of skill `design-planning` step, resulting in a finalized `Design_Blueprint` and Interface Contract.

## 2. Core Philosophy

The implementation agent must act as a strict compiler for the design document. "Clever" code is a liability; readable, maintainable code is an asset. When building tools like a code explainer or an AST analyzer, the agent must prioritize explicit state management and strict typing. The agent is strictly forbidden from improvising new features or altering the agreed-upon data contracts during this phase. If a fundamental flaw is discovered in the design, the agent must halt implementation and trigger a regression to the Design Phase.

## 3. Execution Protocol

To construct robust, maintainable code, the agent must execute the following sequential operations:

### Step 3.1: Environment & Dependency Verification

* **Action:** Review the target language version and the availability of external libraries specified in the blueprint (e.g., standard libraries vs. agentic coding frameworks).
* **Validation:** Ensure that the implementation does not introduce unauthorized dependencies. If a module requires an external parsing library to analyze code snippets, it must have been approved in the design phase.

### Step 3.2: Type-Driven Skeleton Construction

* **Action:** Instantiate the boundary layer first. Write the exact function signatures, class definitions, and data models (e.g., Pydantic `BaseModel` schemas) before writing any internal logic.
* **Mechanism:** heavily leverage static typing. For Python, this means exhaustive use of the `typing` module (`Dict`, `List`, `Optional`, `Callable`) to enforce the Interface Contract.
* **Output:** A non-functional but structurally complete skeleton of the module that successfully passes basic syntax checks.

### Step 3.3: Logic Instantiation (Core Translation)

* **Action:** Fill in the pure functions and state transitions defined in the blueprint.
* **Focus:** Adhere to the Single Responsibility Principle. A function that parses a code snippet should not also format the output string. If the agentic workflow requires routing, querying an LLM, and parsing a response, these must be isolated into discrete, testable units.
* **Output:** Executable logic that fulfills the algorithmic requirements of the design.

### Step 3.4: Error Handling & Boundary Defense

* **Action:** Implement the exception handlers and fallback strategies identified during the design phase's pre-mortem.
* **Focus:** Never silently swallow exceptions. Use explicit `try/except` blocks (or language equivalents) around high-risk operations, such as external API calls or parsing unstructured input. Ensure that failure states return well-defined error objects rather than crashing the execution loop.

### Step 3.5: Inline Documentation & Traceability

* **Action:** Embed docstrings for every public class and method. If utilizing tracing tools (like LangSmith or similar agentic observability platforms), ensure the appropriate decorators or context managers are in place.
* **Focus:** Comments should explain *why* a specific implementation choice was made, not *what* the code is doing. The syntax itself should explain the *what*.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Code_Payload` to be passed to Step 4 (Static Analysis & Type Checking). This payload MUST include:

1. **The Source Code:** The fully implemented, executable logic.
2. **Dependency Manifest Updates:** Any required additions to `requirements.txt`, `pyproject.toml`, or `package.json`.
3. **Implementation Notes:** A brief summary highlighting any minor, non-breaking deviations from the blueprint necessitated by syntax constraints.

## 5. Failure Modes & Fallback Strategies

* **Scope Creep / Blueprint Drift:**
* *Trigger:* The agent begins adding helper functions or features (e.g., adding syntax highlighting to a text-based code explainer) that were not specified in the `Design_Blueprint`.
* *Fallback:* Abort the current generation block. Force the agent to re-read the `Design_Blueprint` and execute a strict diff comparison to strip out unauthorized logic.


* **Type Contract Violation:**
* *Trigger:* The implemented function signature deviates from the required inputs/outputs defined in Step 2.
* *Fallback:* Reject the implementation payload. Mandate a rewrite that perfectly aligns with the established interface.


* **God Object Creation:**
* *Trigger:* The agent dumps all logic into a single, massive function or class.
* *Fallback:* Introduce a strict cyclomatic complexity or line-count limit. Force the agent to refactor the logic into smaller, discrete helper functions before proceeding to Step 4.