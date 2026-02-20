---
name: regression-check
description: Executes the repository's full test suite to ensure new integrations do not break existing functionality.
tags: [testing, regression, ci-cd]
---

# `skill_regression_check.md`

## 1. Meta-Information

* **Skill Name:** Regression Check (Systemic Integration & Blast Radius Mitigation)
* **Phase:** 9 / 12 (Systemic Validation)
* **Objective:** Execute the entire repository's global test suite to guarantee that localized implementations, test generations, and structural refactoring have not inadvertently violated existing contracts or degraded unrelated functionalities elsewhere in the system.
* **Prerequisites:** Successful completion of `skill_refactoring.md` (optimized, locally verified code) and access to the complete, repository-wide test suite and continuous integration (CI) configuration.

## 2. Core Philosophy

In large-scale, distributed software engineering, a localized success is meaningless if it triggers a systemic failure. The "Blast Radius" of a code change often extends far beyond the immediate module. From a product management perspective, shipping a new feature that breaks a legacy workflow instantly destroys user trust. From an engineering perspective, this phase is the final, non-negotiable gatekeeper before a commit is even considered for integration.

For an autonomous agent, regression checking requires shifting from a micro-focus (the specific file just edited) to a macro-focus (the holistic state of the application). The agent must understand that modifying an upstream data structure—for example, tweaking the AST extraction logic in a tool like a Code Explainer—can silently devastate a downstream module that strictly expects the older schema format.

## 3. Execution Protocol

To rigorously certify that no systemic regressions have been introduced, the agent must execute the following protocol:

### Step 3.1: Global Environment Initialization

* **Action:** Spin up the full, clean integration environment required by the repository.
* **Focus:** The agent must not rely on the cached, localized environment used in Step 7. It must re-install dependencies, flush temporary databases, and ensure the execution context perfectly mirrors the production CI/CD pipeline. Flaky environments produce false regressions.

### Step 3.2: The Full Suite Execution

* **Action:** Invoke the global test runner (e.g., `pytest --runall`, `npm test`) across the entire repository.
* **Focus:** This step demands patience. The agent must passively monitor the stdout/stderr streams, capturing the output without intervening. It must specifically watch for cascading failures, where a single altered variable signature causes dozens of seemingly unrelated tests to panic.

### Step 3.3: Cross-Module Boundary Verification

* **Action:** Analyze the integration endpoints between the newly modified code and its immediate neighbors.
* **Focus:** If the global test suite lacks deep integration tests for the specific boundary just modified, the agent must synthetically verify the contract. It must prove that the output of the refactored module is still perfectly consumable by downstream dependencies without requiring type coercion or implicit conversions.

### Step 3.4: Performance & Latency Spot-Check (The Silent Regression)

* **Action:** Evaluate the execution time and memory footprint of the global test run compared to the known baseline.
* **Focus:** Not all regressions throw exceptions. If a refactoring pass accidentally introduced an  loop into a critical path, the unit tests might still pass, but the system will choke in production. The agent must flag significant degradations in test execution time as a hard regression.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Regression_Clearance_Payload` to be passed to Step 10 (Self-Critique & Diff Review). This payload MUST include:

1. **Global Execution Trace:** A truncated or summarized log proving 100% passage of the global test suite, including the total number of tests run.
2. **Blast Radius Report:** A brief analysis detailing which external modules were explicitly touched or tested by the integration suite as a result of the local change.
3. **Performance Delta:** A boolean flag confirming that the test execution time remains within acceptable standard deviations of the repository baseline.

## 5. Failure Modes & Fallback Strategies

* **Spooky Action at a Distance (The Global State Mutation):**
* *Trigger:* The agent modified a seemingly isolated pure function, but the global test suite fails in a completely unrelated directory because both modules secretly relied on a shared mutable global state or singleton.
* *Fallback:* The agent must halt, trace the stack of the failing global test, and identify the shared dependency. Execution must revert to Step 3 (`skill_implementation.md`) to decouple the localized code from the global state, enforcing true hermetic execution.


* **The Dependency Drift (Version Conflict):**
* *Trigger:* The agent updated a third-party library or framework version to support a new feature, causing widespread breakage in legacy modules that rely on deprecated APIs of that same library.
* *Fallback:* Strict reversion. The agent must roll back the dependency bump. If the new feature absolutely requires the updated library, the agent must formally expand the scope of the task to include updating all legacy calls across the repository before proceeding.


* **The Pre-existing Rot (Flaky Global Tests):**
* *Trigger:* The global test suite fails, but upon inspection, the failure is highly non-deterministic and entirely unrelated to the agent's recent changes (a pre-existing flaky test).
* *Fallback:* The agent must use its judgment to isolate the failure. It should run the failing specific test in isolation 5-10 times. If it passes intermittently, the agent must log the flaky test in a separate technical debt tracker, bypass that specific test's failure, and clear the regression check based on the deterministic tests.