# Skills Architecture

This directory contains all agent skills. Skills are grouped by purpose:

## Orchestrators

| Skill | Role |
|-------|------|
| [code-mutation](./code-mutation/SKILL.md) | Master execution loop for all code modification tasks. Selects a tier (Micro / Standard / Full) and delegates each phase to the appropriate sub-skill below. |

## Code Mutation Sub-Skills

These skills are invoked by the `code-mutation` orchestrator in a defined sequence. They can also be triggered independently by the agent when the task matches their description.

| Phase | Skill | Purpose |
|-------|-------|---------|
| 1 | [context-analysis](./context-analysis/SKILL.md) | Map dependencies and blast radius before any change |
| 2 | [design-planning](./design-planning/SKILL.md) | Blueprint the implementation before writing code |
| 3 | [implementation](./implementation/SKILL.md) | Translate design into clean, typed, production code |
| 4 | [static-analysis](./static-analysis/SKILL.md) | Run linters and type checkers |
| 5 | [output-verification](./output-verification/SKILL.md) | Sanity-check that the code executes without crashing |
| 6 | [test-generation](./test-generation/SKILL.md) | Write unit and integration tests |
| 7 | [test-verification](./test-verification/SKILL.md) | Run tests and validate they can actually fail |
| 8 | [refactoring](./refactoring/SKILL.md) | Optimize for readability and DRY without changing behavior |
| 9 | [regression-check](./regression-check/SKILL.md) | Run the full test suite to catch systemic breakage |
| 10 | [diff-review](./diff-review/SKILL.md) | Holistic security and logic review of the final diff |
| 11 | [documentation](./documentation/SKILL.md) | Update docstrings, comments, and READMEs |
| 12 | [state-checkpointing](./state-checkpointing/SKILL.md) | Atomic commit with a descriptive message |

## Skill Management

| Skill | Purpose |
|-------|---------|
| [skill-creator](./skill-creator/SKILL.md) | Guide for creating and packaging new skills |

## Adding New Skills

1. Run `skill-creator/scripts/init_skill.py <name> --path .agent/skills/<name>`
2. Edit `SKILL.md` — keep body under 500 lines, move detail to `references/`
3. If the skill is a sub-step of an existing orchestrator, add it to that orchestrator's phase list
4. Update this README
