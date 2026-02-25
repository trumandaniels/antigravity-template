---
description: Start web server and preview it in the built-in browser
---

This workflow spins up the local development server and then opens the antigravity browser to view it.

1. Determine the appropriate command to start the web server in the current project context (e.g., `npm run dev`, `npm start`, `python -m http.server`, etc.).
2. Use the `run_command` tool to execute the server start command. Make sure to set `WaitMsBeforeAsync` appropriately (e.g., 2000) so the long-running server command goes to the background and you can retrieve its command ID.
3. Check the command output to identify the exact `localhost` URL and port it is running on (e.g., `http://localhost:3000`, `http://127.0.0.1:5173`). Use the `command_status` tool to read the latest output if the URL isn't immediately visible.
4. Once the server is active, use the `browser_subagent` tool. Set the `Task` argument to navigate to the identified localhost address and verify that the application loads correctly.
