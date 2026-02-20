# Static Analysis

## 1. Meta-Information

* **Skill Name:** Static Analysis & Type Checking
* **Phase:** 4 / 12 (Validation & Remediation)
* **Objective:** Subject the raw `Code_Payload` to deterministic, automated scrutiny using strict linters and type checkers. Catch and automatically remediate syntax errors, type mismatches, and structural violations *before* any runtime execution is attempted.
* **Prerequisites:** Successful completion of `skill_implementation.md`, resulting in a complete `Code_Payload`. Access to configured environment tooling (e.g., `mypy`, `ruff`, `pylint`, or their language equivalents).

## 2. Core Philosophy

An agent must never execute code it has just written without first proving that the code is structurally sound. Trying to debug runtime errors that stem from basic typos or type mismatches wastes compute, bloats the context window, and confuses the agent's reasoning process. This phase enforces a "Fail-Fast, Fix-Fast" methodology. The agent must treat the output of static analysis tools as an absolute source of truth.

## 3. Execution Protocol

To effectively validate and remediate the implementation, the agent must execute the following sequential operations:

### Step 3.1: Abstract Syntax Tree (AST) Validation

* **Action:** Perform a rapid lexical parse of the generated code to ensure it is syntactically valid.
* **Mechanism:** Use native language compilers or parsers (e.g., Python's `ast.parse()`) to detect mismatched parentheses, unclosed strings, or invalid indentation.
* **Validation:** If the AST fails to parse, the agent must not proceed to deeper analysis. It must immediately read the syntax error traceback and correct the formatting.

### Step 3.2: Strict Type Checking

* **Action:** Execute the language's strictest type-checking tool (e.g., `mypy --strict` or `pyright` for Python, `tsc --strict` for TypeScript) against the code.
* **Focus:** This is the agent's reality check. Ensure that the returned types match the `Design_Blueprint` interface contract. Identify hallucinated attributes on objects, incorrect function arguments, and unhandled `Optional` or `None` types.
* **Validation:** Every single type error must be resolved. "Ignoring" type errors is strictly prohibited unless explicitly authorized in the design phase for interfacing with untyped legacy systems.

### Step 3.3: Linter & Stylistic Enforcement

* **Action:** Run configured linters (e.g., `ruff`, `flake8`) to enforce idiomatic style and catch logical anomalies.
* **Focus:** Detect unused imports (a common symptom of LLM context drift), undefined variables, shadowed built-ins, and overly complex cyclical dependencies.
* **Output:** A standardized report of warnings and stylistic violations.

### Step 3.4: The Automated Remediation Loop

* **Action:** Parse the `stdout` and `stderr` outputs from the tools utilized in Steps 3.1 through 3.3.
* **Mechanism:** The agent must map each specific error back to the corresponding line in the `Code_Payload` and generate a precise patch.
* **Validation:** After applying patches, the agent must recursively re-run the static analysis suite. The loop only terminates when the tools exit with a status code of `0`.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Verified_Code_Payload` to be passed to Step 5 (Output Verification). This payload MUST include:

1. **The Scrubbed Source Code:** The iteratively refined code that passes all static checks.
2. **The Analysis Report:** A log proving that the type checkers and linters were executed and returned a clean status.
3. **Remediation Audit (Optional but Recommended):** A brief log of the errors the agent caught and fixed itself during the remediation loop, providing observability into the agent's initial implementation accuracy.

## 5. Failure Modes & Fallback Strategies

* **The Infinite Remediation Loop:**
* *Trigger:* The agent patches an error, which causes a new error, which causes it to revert to the original code, creating an endless cycle.
* *Fallback:* Introduce a strict iteration limit (e.g., `MAX_RETRIES = 3`). If the agent fails to resolve static analysis errors within this limit, it must abort the loop, discard the implementation, and return to Step 2 (Design) or Step 3 (Implementation) with the unresolvable error log as a negative constraint.


* **Error Suppression (The "type: ignore" Anti-Pattern):**
* *Trigger:* The agent attempts to resolve strict type errors by simply appending `# type: ignore` or `@ts-ignore` to the problematic lines.
* *Fallback:* Configure the linter to explicitly forbid suppression comments during this phase. The agent must solve the underlying structural problem, not mask it.


* **Hallucinated Library Types:**
* *Trigger:* The type checker fails because the agent relies on an external library that lacks type stubs, resulting in `Any` types spreading throughout the codebase.
* *Fallback:* Mandate the isolation of untyped external boundaries. Require the agent to write explicit wrapper functions that enforce types at the boundary layer before the data enters the core logic.