---
trigger: always_on
---

In-Place Mutation Only: Never create new versions of existing files (e.g., no main_v2.py or script_new.js). Modify existing files directly. If a complete rewrite is necessary, overwrite the existing file.

Zero-Tolerance for Dead Code: When refactoring or updating logic, immediately remove the old, unused code. Do not comment it out for "future reference." Keep the codebase lean.

Strict Real-Data Policy: DO NOT generate mock data, synthetic databases, or placeholder APIs unless explicitly instructed by the user or as a part of the testing suite. If a task requires data to proceed, halt execution and explicitly ask the user for the real data source, schema, or connection string.