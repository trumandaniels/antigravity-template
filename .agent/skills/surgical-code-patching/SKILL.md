---
name: surgical-code-patching
description: Make sure to use this skill whenever the user asks to modify, refactor, bug-fix, or update existing code files. Trigger this skill immediately if you are about to write to a file that already contains code, or if the user mentions "fixing", "updating", or "refactoring". This skill is essential to prevent destructive side effects, ensuring orthogonal code, comments, and structure remain completely untouched.
---

# Surgical Code Patching Protocol

When operating on an existing codebase, implicit dependencies, edge-case handling, and inline documentation are critical. Because your attention mechanism is highly focused on solving the user's immediate prompt, it is a known architectural pattern that you might accidentally drop orthogonal comments or simplify unrelated functions if you attempt to rewrite large sections of a file.

To protect the integrity of the codebase, you should shift your approach from "file generation" to "AST-aware patching" by following these steps:

## 1. Isolate the Blast Radius (AST Grounding)
Before making any modifications, read the file and build a mental model of its Abstract Syntax Tree. 
* Identify the exact boundaries (start and end lines) of the specific function, class, or logical block that requires the change.
* Understand the periphery. If you encounter code or comments that you do not fully understand, treat them as critical infrastructure. 
* **The Why:** Grounding your edits in the AST prevents the accidental deletion of logic simply because it wasn't relevant to the current task.

## 2. Execute Targeted Edits
Avoid whole-file overwrites (e.g., rewriting a 300-line file to change 5 lines). Large context window writes lead to token dropping and attention degradation.
* Use targeted modification techniques: emit unified diffs, use search-and-replace tools, or specifically rewrite only the isolated function.
* **The Preservation Invariant:** Orthogonal code, docstrings, and inline comments are immutable. If a line of text does not strictly require modification to fulfill the user's prompt, it should remain exactly as it was.

## 3. The Diff Validation Loop
Agentic autonomy requires self-correction. Before finalizing your edit and presenting the output to the user, you should run a validation step:
1. Generate a diff of your proposed changes against the original file state.
2. Review the diff line-by-line. 
3. Explicitly check for collateral damage: *Did I remove any comments? Did I alter any functions outside the scope of the user's request?*
4. If you detect that unrelated code or comments were stripped or altered, revert your change and apply a more constrained patch.