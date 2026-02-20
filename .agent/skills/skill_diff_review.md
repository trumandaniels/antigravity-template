# `skill_diff_review.md`

## 1. Meta-Information

* **Skill Name:** Self-Critique & Diff Review (Holistic Quality Gate)
* **Phase:** 10 / 12 (Validation & Defense)
* **Objective:** Perform a rigorous, adversarial inspection of the complete changeset (the unified diff) against the original architectural blueprint. Identify security vulnerabilities, scope creep, leftover scaffolding, and logical leaps before the code is codified into documentation and version control.
* **Prerequisites:** Successful completion of `skill_regression_check.md` (the global test suite passes green, and no systemic regressions exist). Read access to the `Design_Blueprint` from Step 2.

## 2. Core Philosophy

In elite engineering organizations, the code review is the ultimate crucible. As a senior engineer, I can assure you that most catastrophic production failures are not caused by syntax errors (which linters catch) or localized logic bugs (which tests catch). They are caused by *contextual drift*—when the implementation solves the immediate problem but violates the system's broader architectural constraints or introduces subtle security vectors.

For an autonomous agent, LLM tunnel vision is a critical risk. During implementation and refactoring, the agent operates deep in the weeds. The Diff Review phase forces a mandatory context switch. The agent must step back, adopt the persona of an adversarial Staff Engineer or pragmatic Product Manager, and evaluate purely the *delta* (what is being added, changed, or removed). If a human reviewer would reject the pull request, the agent must reject its own diff.

## 3. Execution Protocol

To execute a comprehensive self-critique, the agent must systematically analyze the proposed changeset through the following strict lenses:

### Step 3.1: Blueprint Reconciliation

* **Action:** Generate a unified diff of all modified files. Place this diff side-by-side with the original `Design_Blueprint` from Step 2.
* **Focus:** Did the implementation strictly adhere to the agreed-upon plan? If the blueprint called for an  hash map lookup, but the diff shows an  nested loop, the agent has drifted. Every structural deviation from the blueprint must be aggressively interrogated.

### Step 3.2: Security & Boundary Audit

* **Action:** Scan the diff exclusively for security vulnerabilities and unhandled failure states.
* **Focus:** The agent must actively hunt for:
* Hardcoded credentials or API keys.
* Path traversal vulnerabilities or unsafe deserialization.
* Unvalidated inputs passed to external systems (e.g., shell executions, SQL queries, or LLM prompts susceptible to injection).
* Suppressed exceptions (e.g., `except Exception: pass`).



### Step 3.3: Scope Creep & "Drive-by" Edits

* **Action:** Analyze the diff for changes that, while potentially beneficial, are entirely unrelated to the core objective of the task.
* **Focus:** This is the Product Manager's lens. Did the agent try to "clean up" unrelated formatting in a file it happened to be touching? Did it add a speculative feature "just in case"? All drive-by edits must be stripped. Commits must remain strictly atomic to ensure clean rollbacks if necessary.

### Step 3.4: Scaffolding & Artifact Eradication

* **Action:** Perform a line-by-line sweep for residual development artifacts.
* **Focus:** Development leaves footprints. The agent must ensure that zero `print()` statements, `console.log` calls, `TODO:` comments, `debugger` breakpoints, or hardcoded test variables have accidentally leaked into the production code.

### Step 3.5: The Cognitive Load Assessment

* **Action:** Read the diff as a narrative.
* **Focus:** Is the intent of the change immediately obvious? If a newly introduced block of logic requires the reader to jump back and forth between three different files to understand the execution flow, the cognitive load is too high. The agent must evaluate whether the code is "clever" (bad) or "clear" (good).

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Review_Audit_Payload` to be passed to Step 11 (Documentation Update). This payload MUST include:

1. **The Final Unified Diff:** The pristine, finalized text showing exactly what will be committed.
2. **Alignment Checklist:** A boolean matrix confirming that the diff satisfies all architectural constraints defined in Step 2, contains no security flaws, and has zero leftover scaffolding.
3. **Review Verdict:** A final `APPROVED` or `REJECTED` status. If rejected, it must include a specific, line-referenced actionable critique.

## 5. Failure Modes & Fallback Strategies

* **The "Rubber Stamp" (Confirmation Bias):**
* *Trigger:* The agent reviews its own work and immediately issues an `APPROVED` verdict with a generic summary like "Code looks good and tests pass," without actually citing any lines or structural checks.
* *Fallback:* Force the agent to output a "Devil's Advocate" critique. Before being allowed to approve, the agent *must* formulate at least two plausible reasons why a Senior Engineer might reject the diff. If it cannot, the review is invalid and must be re-run with higher strictness.


* **Discovery of Unintended Scope Creep:**
* *Trigger:* During Step 3.3, the agent realizes the diff contains modifications to files or functions outside the bounds of the original objective.
* *Fallback:* The agent must issue a `REJECTED` verdict. Execution immediately reverts to Step 8 (`skill_refactoring.md`) with explicit instructions to `git checkout` the unrelated files and isolate the diff strictly to the required changes.


* **Security Vulnerability Detected:**
* *Trigger:* The agent identifies a potential injection vector or unhandled extreme edge case in the diff that the test suite missed.
* *Fallback:* The agent must issue a `REJECTED` verdict. Execution reverts all the way back to Step 6 (`skill_test_generation.md`). The agent must first write a failing test that exploits the discovered vulnerability, and then proceed through the loop to patch it.