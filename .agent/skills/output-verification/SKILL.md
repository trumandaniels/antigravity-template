# Output Verification

## 1. Meta-Information

* **Skill Name:** Output Verification (The "Smoke Test")
* **Phase:** 5 / 12 (Execution & Sanity Check)
* **Objective:** Isolate the fully linted `Verified_Code_Payload` and execute it in a controlled environment. Confirm that the script interprets, compiles, and completes a primary top-to-bottom execution path without throwing fatal runtime or initialization errors.
* **Prerequisites:** Successful completion of `skill_static_analysis.md`, resulting in a cleanly linted and typed `Verified_Code_Payload`. Access to a secure, isolated execution environment (e.g., Docker container, WebAssembly sandbox, or restricted local subprocess).

## 2. Core Philosophy

Static analysis (Step 4) proves that the code is *syntactically and structurally* sound. Output Verification (Step 5) proves that the code is *operationally* viable. In agentic pipelines, an LLM must never construct behavioral tests for an artifact that cannot even instantiate itself. By forcing the agent to execute a dummy invocation of the code in an isolated boundary, we establish a deterministic baseline: the engine turns on, the dependencies resolve, and the function returns an object rather than a fatal stack trace.

## 3. Execution Protocol

To validate the runtime viability of the implemented logic, the agent must systematically perform the following operations:

### Step 3.1: Environment Isolation & Sandboxing

* **Action:** Instantiate a secure, ephemeral boundary for execution. The agent must never run unverified code directly in its own primary memory space or OS environment.
* **Focus:** Restrict file system access, disable unapproved network calls, and enforce strict memory and CPU limits.
* **Validation:** Confirm the sandbox is active before injecting the `Verified_Code_Payload`.

### Step 3.2: Module Initialization & Import Check

* **Action:** Attempt to import the newly written module or class into the execution environment without invoking any specific methods.
* **Focus:** This step catches runtime circular dependencies, missing underlying C-bindings, configuration errors, and module-level exceptions that static analysis cannot foresee.
* **Output:** A boolean success metric. If the import fails, the code is dead on arrival.

### Step 3.3: The Happy-Path Mock Invocation

* **Action:** Construct a minimal, perfectly formed "dummy input" that strictly adheres to the data contracts defined in Step 2 (`Design_Blueprint`).
* **Mechanism:** Invoke the primary entry-point function using this dummy data. Do *not* test edge cases, null values, or malicious inputs here. This is strictly a "happy path" sanity check.
* **Validation:** The function must execute from the first line to the `return` statement without halting.

### Step 3.4: Trace & Output Capture

* **Action:** Intercept all `stdout`, `stderr`, and the final returned object.
* **Focus:** Verify that the output shape matches the predicted type signature. For instance, if the blueprint dictates returning a JSON string or a Pydantic model, verify that the execution actually produced that structure, not a `NoneType` or an empty string.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Runtime_Verification_Report` to be passed to Step 6 (Test Generation). This report MUST include:

1. **The Execution Trace:** The captured `stdout` and `stderr` logs from the sandbox.
2. **The Output Snapshot:** The actual payload returned by the happy-path invocation.
3. **Resource Metrics (Optional):** Time taken to execute (to catch infinite loops) and peak memory usage.

## 5. Failure Modes & Fallback Strategies

* **The Infinite Execution Loop:**
* *Trigger:* The agent wrote a `while True` loop with a flawed exit condition, or a recursive function lacking a base case.
* *Fallback:* Enforce a strict timeout at the sandbox level (e.g., `TIMEOUT = 5.0 seconds`). If the execution hits the timeout, kill the process, capture the stack trace, and send the agent back to Step 3 (Implementation) to fix the halting logic.


* **Environment Dependency Mismatch:**
* *Trigger:* The code relies on a system-level library or an OS-specific path that does not exist in the execution sandbox (e.g., trying to write to `C:\temp\` on a Linux container).
* *Fallback:* Flag the violation of environmental neutrality. Force the agent to refactor the code to use abstract, cross-platform pathing (like `pathlib` in Python) or mock the required system resource.


* **Silent Failures:**
* *Trigger:* The code executes completely and exits with status code `0`, but returns nothing or fails to perform its core duty (e.g., an empty try/except block that swallows a fatal error).
* *Fallback:* The Output Snapshot validation must catch this. If the returned payload is unexpectedly empty or `None`, the verification is marked as a failure, forcing the agent to review its error-handling mechanisms.