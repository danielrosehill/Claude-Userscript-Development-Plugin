---
name: test-userscript
description: Validate a userscript end-to-end in a real Chrome tab via the Claude in Chrome MCP. Loads the target page, simulates the script's effect, reads console logs and DOM state, and reports whether the script's behavior matches its `@description`.
---

# Test Userscript

Validate userscript behavior live in Chrome. Tampermonkey itself can't be installed/configured by Claude in Chrome (it's a browser extension installed by the user), so this skill does **simulated injection**: it navigates to the target site and evaluates the userscript body via `playwright_evaluate` / `javascript_tool` to confirm logic works against real DOM.

## When to use

- After writing or modifying a userscript
- Before bumping its version with `bump-version`
- To debug why a userscript isn't behaving on a live page

## Prerequisites

The Claude in Chrome MCP must be available. Tools used:
- `mcp__claude-in-chrome__tabs_context_mcp` — pick a tab
- `mcp__claude-in-chrome__tabs_create_mcp` — open the target URL
- `mcp__claude-in-chrome__navigate` — go to a `@match` URL
- `mcp__claude-in-chrome__javascript_tool` — inject and run the script body
- `mcp__claude-in-chrome__read_console_messages` — collect `console.log` output
- `mcp__claude-in-chrome__read_page` / `get_page_text` — inspect the resulting DOM

Load each tool via `ToolSearch` first.

## Steps

1. Read the target `.user.js` file. Parse the metadata block — extract `@match` / `@include` URLs and `@grant` list.
2. **Bail early** if the script needs `GM_*` APIs the simulator can't provide (`GM_xmlhttpRequest`, `GM_setValue`, `GM_getValue`, etc.). Tell the user this script can only be validated by installing it in Tampermonkey and exercising it manually. Stop here.
3. If `@grant none`, proceed: open one of the matched URLs in a new tab, wait for `document-idle`, then `playwright_evaluate` the IIFE body.
4. Read console messages with a pattern matching the script's name prefix to filter noise.
5. Read the page text or query specific selectors to confirm the expected DOM mutation occurred.
6. Report:
   - PASS / FAIL with a one-line verdict
   - What was injected, what changed, what the console said
   - Any errors or warnings surfaced
7. If the user wants visual proof, record a short GIF of the before/after with `gif_creator`.

## Output

A short report (under 30 lines) the user can paste into a commit message or PR description.
