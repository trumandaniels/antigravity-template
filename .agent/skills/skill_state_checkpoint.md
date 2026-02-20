# `skill_state_checkpoint.md`

## 1. Meta-Information

* **Skill Name:** State Checkpoint & Commit (Atomic Persistence)
* **Phase:** 12 / 12 (Finalization)
* **Objective:** Securely package the fully implemented, tested, refactored, and documented codebase into an atomic, logically sound version control checkpoint. Generate a semantic commit message that serves as a high-fidelity system ledger for both human maintainers and future agentic context retrieval.
* **Prerequisites:** Successful completion of `skill_documentation.md` (the code has been rigorously documented and intent has been captured). Read access to the `Design_Blueprint` and the `Unified_Diff`.

## 2. Core Philosophy

In a large-scale engineering environment, the version control history is not merely a backup system; it is the definitive cryptographic ledger of a product's evolution. From a product management perspective, commit history directly drives release notes, rollback strategies, and incident response timelines. From a Senior Engineering perspective, a commit must be *atomic*—meaning it does exactly one thing, and if it is reverted, the system returns to a perfectly stable prior state without leaving orphaned dependencies.

For an autonomous coding agent, this final phase is critical for memory management. The commit message acts as the agent's long-term semantic memory. If an agent writes a vague commit message like "Fixed bugs," a future agent attempting to understand the file's history will be completely blind to the context. This skill enforces strict adherence to the Conventional Commits specification, ensuring programmatic readability and flawless continuous integration (CI) triggers.

## 3. Execution Protocol

To finalize the execution loop and persist the state, the agent must strictly execute the following sequence:

### Step 3.1: The Working Tree Audit

* **Action:** Interrogate the current state of the execution environment (e.g., `git status`).
* **Focus:** The agent must verify that the environment is pristine. It must identify and explicitly exclude any untracked files, local development artifacts (e.g., `.pytest_cache`, `__pycache__`), or temporary data dumps generated during the testing or refactoring phases.

### Step 3.2: Atomic Staging

* **Action:** Stage *only* the specific files that were intentionally modified, verified, and approved in the Diff Review phase (Step 10).
* **Focus:** This is a hard guardrail against accidental state leakage. The agent must never execute a blind `git add .` command. It must specifically target the validated diffs to ensure the checkpoint remains logically bounded to the original task.

### Step 3.3: Semantic Commit Message Generation

* **Action:** Synthesize a structured, programmatic commit message based on the verified diff and the original `Design_Blueprint`.
* **Focus:** The agent must strictly adhere to the Conventional Commits specification. The message must contain:
* **Type:** `feat`, `fix`, `refactor`, `docs`, `test`, or `chore`.
* **Scope:** The specific module or architectural boundary affected (e.g., `ast-parser`).
* **Subject:** A concise, imperative summary (under 50 characters).
* **Body:** A detailed explanation of the *motivation* for the change and any contrast with previous behavior.
* **Footer:** References to specific issue tickets, or breaking change warnings (`BREAKING CHANGE:`).



### Step 3.4: Cryptographic Checkpoint (The Commit)

* **Action:** Execute the version control commit, permanently binding the staged files to the semantic message and generating a unique hash.
* **Focus:** Ensure the commit is securely written to the local index. If the agentic workflow is connected to a remote repository (e.g., GitHub, GitLab), push the state to the designated branch, ensuring no remote merge conflicts exist before confirming success.

### Step 3.5: Execution Loop Termination & Handoff

* **Action:** Purge the agent's temporary working memory, close open file handles, and return a structured success payload to the master orchestrator.
* **Focus:** An agent must clean up after itself. Once the state is safely checkpointed, the agent must signal that it is ready for the next prompt or task assignment, ensuring no residual context from this loop bleeds into the next.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Checkpoint_Success_Payload` to terminate the execution loop. This payload MUST include:

1. **Commit Hash:** The exact SHA-1 hash (or equivalent) of the successful checkpoint.
2. **The Commit Ledger:** The exact text of the semantic commit message generated and applied.
3. **Execution Summary:** A brief metadata payload confirming total execution time across all 12 phases, the number of files modified, and the net line-of-code delta (e.g., `+145, -32`).

## 5. Failure Modes & Fallback Strategies

* **The "Kitchen Sink" Checkpoint (Non-Atomic Commit):**
* *Trigger:* During Step 3.2, the agent attempts to stage modifications across multiple unrelated domains (e.g., updating a parser's core logic *and* changing the CSS of the frontend dashboard in the same commit).
* *Fallback:* The agent must issue an internal `REJECTED` signal. It must unstage all files, logically group the diffs by their specific domain, and generate multiple, discrete, atomic commits instead of one monolithic checkpoint.


* **The Vague Ledger (Context Collapse):**
* *Trigger:* The agent generates a commit message that violates the Conventional Commits specification or fails to explain the *why* in the body (e.g., `fix: updated logic`).
* *Fallback:* A strict semantic parsing check. If the commit message subject does not begin with an approved type and scope, or if the body is less than 15 words, the agent must discard the message and regenerate it by re-reading the original `Design_Blueprint` for context.


* **The Synchronization Conflict (Merge Rejection):**
* *Trigger:* The remote repository rejects the push because the branch has advanced independently during the agent's execution loop.
* *Fallback:* The agent must immediately halt the termination process. It must fetch the remote state, execute a rebase (preferable for clean history) or a merge, and then automatically trigger Step 9 (`skill_regression_check.md`) to verify that the newly integrated remote changes did not silently break its local implementation.