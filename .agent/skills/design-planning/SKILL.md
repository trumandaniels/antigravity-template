---
name: design-planning
description: Drafts architectural blueprints, data structures, and algorithms prior to implementation.
tags: [design, architecture, algorithms]
---

# Design Planning

## 1. Meta-Information

* **Skill Name:** Design & Planning
* **Phase:** 2 / 12 (Architecture & Blueprinting)
* **Objective:** Translate the raw constraints from the Context Analysis into a deterministic architectural blueprint. Define strict data interfaces, algorithmic pathways, and error-handling mechanisms before writing any executable logic.
* **Prerequisites:** Successful completion of `skill_context_analysis.md`, resulting in a valid `Context_Summary`.

## 2. Core Philosophy

The Design phase is the bridge between *understanding* the problem and *solving* the problem. In agentic frameworks, execution is cheap, but state management is expensive. Therefore, the agent must adopt a "Design Document" methodology. It must define the exact Pydantic models, state transitions, and API contracts it intends to use. If the agent cannot express the solution as a set of types and a control flow graph, it does not understand the solution well enough to write the code.

## 3. Execution Protocol

To construct a robust architectural blueprint, the agent must execute the following sequential operations:

### Step 3.1: Requirement & Constraint Mapping

* **Action:** Ingest the `Context_Summary`. Map the user's explicit request against the system's hard constraints (e.g., existing architectural patterns, token limits, library availability).
* **Validation:** Explicitly list what the new module *will* do and, just as importantly, what it *will not* do (scope containment).

### Step 3.2: Data Structure & Interface Definition

* **Action:** Draft the strict schemas for all inputs and outputs. For AI/ML and agentic pipelines, this means explicitly defining the shapes of the data moving between chains or nodes.
* **Mechanism:** Use strictly typed representations (e.g., Python `typing`, Pydantic `BaseModel` outlines, or dataclasses). Do not rely on loose dictionaries or untyped JSON objects.
* **Output:** A formal contract defining exactly how the new module will receive data (e.g., a raw code snippet string and a target language) and what it will return (e.g., a structured analysis object).

### Step 3.3: Algorithmic & Control Flow Design

* **Action:** Map the step-by-step logical progression of the implementation. If building an agentic workflow, define the graph nodes, state reducers, and conditional edges.
* **Focus:** Favor pure functions and deterministic state transitions. Avoid hidden side effects. If the process requires multiple steps (like parsing an AST, querying an LLM, and formatting the output), separate these into distinct logical blocks.
* **Output:** A step-by-step pseudocode or structural outline of the algorithm.

### Step 3.4: Failure Mode & Edge Case Anticipation

* **Action:** Conduct a preemptive "pre-mortem." Identify the top three ways this design could fail in a production environment.
* **Focus:** Consider unhandled null types, rate limits on external calls, oversized inputs (e.g., a user submitting a 10,000-line file for explanation), and LLM output parsing failures.
* **Output:** A mandated list of exceptions to catch, fallback strategies to implement, and validation checks required at the module's boundary.

### Step 3.5: PM/Product Alignment Check

* **Action:** Review the proposed design against the original intent of the task.
* **Validation:** Does this design actually solve the core problem elegantly, or does it over-engineer a simple fix? Simplicity is the ultimate sophistication. Discard unnecessary abstractions (YAGNI - You Aren't Gonna Need It).

## 4. Required Output Artifacts

Upon completing this skill, the agent must generate a `Design_Blueprint` to be passed to Step 3 (Implementation). This blueprint MUST include:

1. **The Interface Contract:** The exact type signatures and data models (e.g., Pydantic schemas) for the module.
2. **The Control Flow:** The sequential steps or state machine logic required to execute the task.
3. **The Edge Case Handlers:** Explicit instructions on how to handle anticipated failures or malformed inputs.
4. **Dependency Declarations:** Any new libraries or internal utilities that will need to be imported.

## 5. Failure Modes & Fallback Strategies

* **Over-Engineering (The "Astronaut Architecture" Problem):**
* *Trigger:* The agent designs a highly complex, multi-layered abstraction for a simple procedural task.
* *Fallback:* Introduce a strict complexity penalty constraint. Force the agent to output the absolute minimum viable architecture required to satisfy the `Context_Summary`.


* **Vague Data Contracts:**
* *Trigger:* The agent defines inputs/outputs using loose types like `Any` or `dict`.
* *Fallback:* Reject the design blueprint. Mandate strict, nested schemas before allowing the transition to the Implementation phase.


* **State Mutation Risks:**
* *Trigger:* The design proposes modifying global state or modifying input variables in place rather than returning new objects.
* *Fallback:* Enforce immutability protocols. Require the design to utilize pure functions that return new state copies.