---
name: python-repo-architect
description: Guide for initializing, structuring, and refactoring Python repositories using the `src` layout. Triggers automatically whenever requested to set up a new Python project, build code analysis tools, or architect agentic frameworks.
---

# SKILL: Professional Python Repository Architecture

## Overview
This skill provides the invariants and execution workflows for structuring robust, production-grade Python repositories. It enforces the `src` layout to prevent import pollution, ensure testing fidelity, and maintain strict separation of concerns.

## Resources
Before manually creating files, check the skill directory for bundled automation:
- `scripts/init_repo.py` - Executable script to scaffold the invariant directory tree.
- `assets/pyproject_template.toml` - Standardized build backend (e.g., Hatch/Poetry) and linting (Ruff/Mypy) configuration.
- `references/testing_patterns.md` - Guidelines for mirroring the `src/` directory in `tests/`.

## Workflow: Repository Initialization

When asked to initialize a new repository—whether a standard utility, a complex code explainer, or an agentic application—enforce this exact directory tree:

```text
<repository_root>/
├── .github/                # CI/CD pipelines
├── docs/                   # Documentation (Sphinx/MkDocs)
├── data/                   # [Optional] Local datasets (must be tightly .gitignore'd)
├── src/                    # [INVARIANT] The source code root
│   └── <package_name>/     # The primary Python package (snake_case)
│       ├── __init__.py     
│       ├── core/           # Core business logic (e.g., AST parsing, engine logic)
│       ├── agents/         # Agent workflows (e.g., LangChain/LangGraph configurations)
│       └── utils/          # Shared helper functions
├── tests/                  # [INVARIANT] Test suite isolated from application code
│   ├── conftest.py         # Pytest fixtures
│   ├── unit/               # Fast, isolated tests mirroring the src/ tree
│   └── integration/        # Slower component interaction tests
├── .env.example            # Environment templates (e.g., OPENAI_API_KEY, LANGSMITH_API_KEY)
├── .gitignore              # [INVARIANT] Ignored system files and environments
├── pyproject.toml          # [INVARIANT] Universal build system and tooling config
└── README.md               # [INVARIANT] Project overview and setup instructions