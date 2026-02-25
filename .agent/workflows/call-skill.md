---
description: Ensure a specific available skill is called and executed guaranteed
---

To guarantee that any existing skill is called and executed properly, follow these steps exactly:

1. **Identify the Skill**: 
   Look at the available skills listed in your system prompt or use the `list_dir` tool on `.agent/skills/` to find the exact name of the skill the user wants to utilize.

2. **Read the Skill Instructions**:
   Use the `view_file` tool on the skill's `SKILL.md` file. For example, if the skill is `documentation`, you MUST run `view_file` on `c:\Users\truma\Documents\Programming\Antigravity\skillet\.agent\skills\documentation\SKILL.md`.
   *Do not skip this step under any circumstances.*

3. **Acknowledge and Prepare**:
   Briefly acknowledge to the user that you have read the skill's instructions and outline the immediate next steps or inputs you need from them, if any are missing.

4. **Execute the Skill**:
   Perform the tasks exactly as outlined in the `SKILL.md` file's instructions, abiding by all constraints, formatting rules, and required tools specified within the skill.

5. **Verify output**:
   Once you believe the skill execution is complete, quickly double check the output against the `SKILL.md` requirements to ensure a perfect delivery.
