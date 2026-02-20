# `skill_documentation.md`

## 1. Meta-Information

* **Skill Name:** Documentation Update (Contextual Preservation & Intent Mapping)
* **Phase:** 11 / 12 (Knowledge Transfer)
* **Objective:** Codify the *intent*, *context*, and *architectural decisions* behind the verified code changes into long-lasting artifacts (docstrings, inline comments, and READMEs) to ensure seamless maintainability for both human engineers and future autonomous agents.
* **Prerequisites:** Successful completion of `skill_diff_review.md` (the code has been strictly audited, secured, and approved for integration). Read access to the finalized codebase and the original `Design_Blueprint`.

## 2. Core Philosophy

In my two decades spanning engineering and product management, the most expensive technical debt I have consistently observed is not poorly written code; it is missing context. Code executes the *how*. Tests verify the *what*. Documentation is the only mechanism that preserves the *why*.

When managing autonomous agents or large engineering teams, outdated documentation is worse than no documentation at all because it actively lies to the reader. For an agentic coding framework, high-quality documentation serves a dual purpose: it allows human maintainers to trust the agent's output, and it acts as high-density semantic memory for the LLM's future context windows. If an agent builds a complex feature—like a multi-agent Code Explainer tool—without explaining *why* it chose a specific parsing strategy or LangChain routing mechanism, the next agent that touches that file will inevitably rewrite it or break it.

## 3. Execution Protocol

To ensure the codebase remains a source of truth, the agent must execute the following sequential documentation protocols:

### Step 3.1: Docstring & Signature Synchronization

* **Action:** Audit and update all function, class, and module-level docstrings touched by the diff.
* **Focus:** The agent must enforce a strict, standardized format (e.g., Google Style Python Docstrings). It must verify that the documented `Args`, `Returns`, and `Raises` sections perfectly match the newly verified type hints and implementation logic. Any discrepancy between the signature and the docstring is a critical failure.

### Step 3.2: The "Why Over What" Inline Audit

* **Action:** Sweep the implementation for complex algorithms, edge-case handling, or seemingly counter-intuitive logic, and inject concise inline comments.
* **Focus:** The agent is strictly forbidden from writing "parrot" comments that restate the code (e.g., `# Increment counter by 1` above `counter += 1`). Instead, it must document the business logic or constraints (e.g., `# Limit AST depth traversal to prevent stack overflows on heavily nested legacy enterprise code`).

### Step 3.3: Architectural Decision Records (ADR) & README Updates

* **Action:** Evaluate whether the change alters the system's macro-architecture, external dependencies, or primary data flows.
* **Focus:** If the agent introduced a new dependency, altered an API contract, or significantly changed a core execution loop (like shifting a Code Explainer module from synchronous processing to an asynchronous agentic framework), it must update the relevant `README.md` or generate an Architectural Decision Record (ADR). This explains the trade-offs considered and why the final path was chosen.

### Step 3.4: Deprecation and Tombstoning

* **Action:** Identify documentation referencing systems, functions, or workflows that were removed or heavily altered during the Refactoring phase (Step 8).
* **Focus:** Erase or formally deprecate old documentation. Leaving a detailed explanation of a function that no longer exists pollutes the repository and hallucinates future agents.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Documentation_Payload` to be passed to the final step, Step 12 (State Checkpoint & Commit). This payload MUST include:

1. **The Documented Source Code:** The final code files with perfectly synchronized docstrings and high-value inline comments.
2. **Supplemental Updates:** Any modified `README.md`, API specification files (e.g., OpenAPI/Swagger), or newly generated ADRs.
3. **Doc Diff Review:** A specialized diff confirming that no logic was accidentally altered during the documentation injection process.

## 5. Failure Modes & Fallback Strategies

* **The "Parrot" (Redundant Noise):**
* *Trigger:* The agent generates inline comments that simply translate Python syntax into English, wasting tokens and human attention without adding contextual value.
* *Fallback:* Implement a semantic density check. If the comment contains the exact same verbs and nouns as the code line beneath it, the agent must delete the comment. Execution reverts to Step 3.2 to write a comment focused strictly on the *intent* of the block, or leave it blank if the code is self-evident.


* **Contextual Desync (The Lying Docstring):**
* *Trigger:* The agent updates a function's logic to return a `Dict` instead of a `List` during Step 3, but fails to update the `Returns:` section of the docstring in Step 11.
* *Fallback:* Enforce strict AST-to-Docstring validation. The agent must parse the final code's Abstract Syntax Tree, extract the actual return types and raised exceptions, and cross-reference them against the generated docstring. If a mismatch is detected, the agent must rewrite the docstring to reflect reality.


* **The "Novelist" (Context Bloat):**
* *Trigger:* The agent writes a massive, 50-line essay in a function's docstring explaining the entire history of the project, severely bloating the file size and degrading the LLM's future context window efficiency.
* *Fallback:* Enforce token limits on documentation blocks. The agent must summarize the context into a concise, bulleted TL;DR. If deep architectural explanations are necessary, the agent must move that text to an external ADR document and leave a simple hyperlink or reference in the code.