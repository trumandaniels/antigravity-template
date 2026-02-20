Building a universal Minimum Description Length (MDL) compressor for agentic knowledge is a Ph.D.-level orchestration problem. You are essentially building a meta-skill: an agent whose sole job is to ingest high-entropy human pedagogy and output low-entropy, machine-actionable state spaces.

To achieve this in Antigravity, we must rely on a rigid, immutable workflow. The agent cannot simply "read and summarize." It must systematically parse the text, design the data structure, implement the extraction, and rigorously test the resulting logic against the source material.

Here is how to structure the `.agent` directory to accomplish this, utilizing a phased execution loop.

### 1. The `.agent` Directory Architecture

To build a world-class MDL compressor, the skill folder must isolate the orchestrator prompt from the discrete cognitive steps required to process the text.

```text
/.agent/skills/mdl-compressor/
│
├── SKILL.md                 # The Meta-Prompt (The Universal Compressor)
│
├── /execution-loop/         # The immutable, step-by-step cognitive engine
│   ├── 01-context-analysis/SKILL.md
│   ├── 02-design-planning/SKILL.md
│   ├── 03-implementation/SKILL.md
│   ├── 04-static-analysis/SKILL.md
│   ├── 05-output-verification/SKILL.md
│   ├── 06-test-generation/SKILL.md
│   ├── 07-test-verification/SKILL.md
│   ├── 08-refactoring/SKILL.md
│   ├── 09-regression-check/SKILL.md
│   ├── 10-diff-review/SKILL.md
│   ├── 11-documentation/SKILL.md
│   └── 12-state-checkpoint/SKILL.md
│
└── /templates/
    └── mdl_schema.yaml      # The strict output structure for the compressed knowledge

```

**A crucial note on routing architecture:** Referencing the explicit `SKILL.md` for each phase of your execution loop is absolutely the correct way to route the agent's cognitive steps. It forces the LLM to context-switch and adopt the specific constraints of that phase. However, when writing your YAML configurations or system prompts, it is highly recommended to use forward slashes (e.g., `execution-loop/01-context-analysis/SKILL.md`) rather than backslashes (`\`). Antigravity's underlying environment is Unix-based, and using standard POSIX paths prevents silent path resolution errors during cross-platform execution.

---

### 2. The Universal MDL Compressor (`SKILL.md`)

This is the root prompt that sits at `/.agent/skills/mdl-compressor/SKILL.md`. Notice how it avoids asking the agent to "summarize" and instead forces it through the rigorous, 12-step execution loop to programmatically extract constraints.

```markdown
# MDL Compressor: Universal Knowledge Extraction

**Objective:** You are an information-theoretic parser. Your task is to ingest unstructured narrative text (textbooks, articles, research papers) and output its Minimum Description Length (MDL) representation. You must strip all pedagogical fluff, historical context, and redundant prose, reducing the text strictly to computable axioms, heuristics, and execution constraints.

## The Execution Engine
You must process the target text by strictly following the sequential execution loop defined below. Do not skip steps. Before acting, load the specific constraints for your current phase:

```yaml
standard_execution_loop:
  - phase: "1. Context & Dependency Analysis"
    reference: "execution-loop/01-context-analysis/SKILL.md"
    action: "Establish the upstream dependencies of the text. What prior knowledge is assumed?"
  - phase: "2. Design & Planning"
    reference: "execution-loop/02-design-planning/SKILL.md"
    action: "Draft the architectural blueprint of the text's core thesis. Identify the state transitions."
  - phase: "3. Implementation"
    reference: "execution-loop/03-implementation/SKILL.md"
    action: "Translate the concepts into discrete, executable rules."
  # ... (The agent will systematically iterate through to step 12) ...

```

## Extraction Rules

When executing **Phase 3: Implementation**, apply the following information-theoretic rules:

1. **Lossy Compression of Narrative:** Discard all anecdotes, metaphors, and historical background. If a sentence does not define a variable, a constraint, or a causal link, delete it.
2. **Lossless Compression of Logic:** Extract formulas, diagnostic tests, and decision boundaries with 100% fidelity.
3. **If/Then Formalization:** Convert abstract advice into strict `IF [Condition] THEN [Action] ELSE [Fallback]` operational logic.

## Output Formatting

The final artifact generated during **Phase 12: State Checkpoint** MUST be a structured YAML document representing the computable state machine of the book.
<template_reference>
Strictly adhere to the schema defined in `./templates/mdl_schema.yaml`
</template_reference>

```

### Why this approach works
By forcing the agent to treat reading a book exactly like refactoring a legacy codebase, you eliminate the LLM's natural tendency to act like a helpful tutor. The 12-step loop forces it to statically analyze the author's logic, test its own understanding of the concepts, and document the final extracted algorithms cleanly.

Would you like me to draft what the `./templates/mdl_schema.yaml` should look like so the extracted knowledge is perfectly formatted for another agent to ingest and use?

```