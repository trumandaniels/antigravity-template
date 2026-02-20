---
name: context-analysis
description: Establishes a mental model of the target environment, identifying dependencies and module architecture.
tags: [architecture, planning, dependencies, agentic-workflow]
---

# Context Analysis

## 1. Meta-Information

* **Skill Name:** Context & Dependency Analysis
* **Phase:** 1 / 12 (Initialization & Discovery)
* **Objective:** Construct a highly accurate, deterministic mental model of the target codebase environment prior to any design or modification. Identify the structural blast radius of proposed changes by mapping upstream dependencies, downstream consumers, and systemic architectural patterns.
* **Prerequisites:** Read access to the repository, project root identification, and access to the target file(s) or issue ticket.

## 2. Core Philosophy

In agentic operations, hallucination and regressions stem from localized thinking. An agent must not evaluate a module in isolation. This skill enforces a "System-First" evaluation paradigm. By deterministically parsing the relationship between components—rather than relying solely on semantic search or isolated file retrieval—the agent guarantees that its subsequent design phase respects the established constraints, data contracts, and architectural norms of the broader system.

## 3. Execution Protocol

To successfully execute Context & Dependency Analysis, the agent must sequentially perform the following operations:

### Step 3.1: Scope Definition & Anchor Identification

* **Action:** Identify the "Anchor Nodes" (the specific files, classes, or functions targeted for modification based on the prompt or issue ticket).
* **Validation:** Verify that the Anchor Nodes exist in the current file system state. If missing, halt execution and request clarification.

### Step 3.2: Upstream Dependency Resolution (What does this code need?)

* **Action:** Parse the Anchor Nodes to identify all imported modules, injected services, inherited classes, and utilized interfaces.
* **Mechanism:** Traverse the Abstract Syntax Tree (AST) or utilize language-specific dependency mapping tools rather than relying on regex or basic text matching.
* **Output:** A structural list of constraints and data shapes that the target code relies upon to function correctly.

### Step 3.3: Downstream Impact Analysis (Who needs this code?)

* **Action:** Execute a reverse-dependency lookup. Identify which external modules, functions, or systems invoke the Anchor Nodes.
* **Focus:** Define the "Blast Radius." Determine if altering the return types, method signatures, or state side-effects of the Anchor Nodes will break downstream consumers.
* **Output:** A matrix of required backwards compatibility and expected output contracts.

### Step 3.4: Architectural Pattern Recognition

* **Action:** Scan the broader directory structure and neighboring files to deduce the prevailing architectural patterns (e.g., MVC, Hexagonal/Ports & Adapters, microservices, specific agentic state machines).
* **Focus:** Identify standard libraries, prevalent error-handling paradigms, logging conventions, and typing strictness (e.g., standard Python type hints vs. strict Pydantic models).
* **Output:** A set of stylistic and structural guardrails that the subsequent implementation must mimic.

### Step 3.5: Context Window Optimization & Pruning

* **Action:** Filter the gathered data to fit within the constraints of the agent's active reasoning context window.
* **Mechanism:** Discard implementation details of deep upstream/downstream nodes; retain only their interfaces, public methods, and type signatures.
* **Output:** A condensed, high-signal "Context Payload" free of noisy, irrelevant logic.

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Context_Summary` to be passed to Step 2 (Design & Planning). This summary MUST include:

1. **The Anchor Node(s):** The specific files/functions being modified.
2. **The Blast Radius:** A list of potentially affected downstream systems.
3. **Data Contracts:** The required input structures and expected output structures.
4. **Architectural Guardrails:** Identified conventions (e.g., "Uses async/await strictly," "Relies on LangChain BaseChatModel interfaces," etc.).

## 5. Failure Modes & Fallback Strategies

* **Circular Dependencies Detected:**
* *Trigger:* The dependency graph loops back on itself.
* *Fallback:* Flag the circular dependency as an architectural risk. Halt execution and request user intervention, or propose a refactor to break the cycle before proceeding.


* **Context Overflow (Token Limit Exceeded):**
* *Trigger:* The dependency tree is too massive to summarize within the context window limits.
* *Fallback:* Aggressively prune downstream analysis to only immediate neighbors (depth=1). Summarize distant dependencies as black-box interfaces.


* **Unresolvable Imports / Dynamic Routing:**
* *Trigger:* Dependencies are injected dynamically at runtime or use obfuscated pathing.
* *Fallback:* Log a warning about incomplete static analysis. Proceed with a strict "Defensive Design" posture in Step 2, assuming unknown downstream impacts.