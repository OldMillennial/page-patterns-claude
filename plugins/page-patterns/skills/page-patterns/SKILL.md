---
name: page-patterns
description: Use when the user asks to add, change or improve a background, texture or pattern on a website hero, banner or section ("make the hero more interesting", "add some texture behind the headline", "give this section a pattern"), or mentions Page Patterns.
---

# Page Patterns backgrounds

The tools are on the `page-patterns` MCP server: `guide`, `describe_options`, `explore`, `preview` and `export`. If they are not available, tell the user the server is not connected and stop.

Follow [references/playbook.md](references/playbook.md) step by step. It covers reading the site, exploring variants, presenting options and implementing the pick, with the design rules to apply.

In Claude Code:

- Use the browser tools for step 1 (measuring) and step 3 (showing the options in the live page). Prefer the built-in browser, which the user can watch.
- Do not edit site files until the user has picked an option. Then say which files you will change before changing them.
- Pass absolute paths to `insertInto` and `savePath`.
