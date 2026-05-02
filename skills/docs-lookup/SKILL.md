---
name: docs-lookup
description: Look up Tampermonkey documentation — metadata keys (`@match`, `@grant`, `@run-at`, etc.), the GM_* / GM.* API, and configuration. Fetches the official docs page on demand to stay current.
---

# Docs Lookup

Authoritative reference: https://www.tampermonkey.net/documentation.php?locale=en

## When to use

- The user asks about a specific metadata directive (`@match` vs `@include`, `@require`, `@resource`, `@connect`, `@run-at`, `@inject-into`, etc.).
- Questions about a `GM_*` / `GM.*` function (`GM_xmlhttpRequest`, `GM_setValue`, `GM_addStyle`, `GM.cookie`, etc.).
- Differences between Tampermonkey, Greasemonkey, and Violentmonkey behavior.

## Method

1. Use `mcp__jungle-shared__fetch-and-convert__fetch_markdown` (or `WebFetch` as fallback) on:
   ```
   https://www.tampermonkey.net/documentation.php?locale=en
   ```
2. Extract the section matching the user's question.
3. Quote the relevant directive signature + 1–2 sentences of explanation. Keep it concise.
4. If the question is about a `GM.` (promise-based) vs `GM_` (sync) variant, mention both and which one Tampermonkey recommends.

## Common gotchas to flag proactively

- `@match` is stricter and faster than `@include`; prefer it.
- `@grant none` runs the script in the **page context** (can touch `window`, no `GM_*`). Any other grant runs in a **sandbox** with `unsafeWindow` for page access.
- `@run-at document-start` fires before the DOM is ready — use it for header injection, not DOM mutation.
- `@require` URLs are fetched once at install time and cached; updating the remote file does **not** re-fetch unless the user reinstalls.
- `@connect` is required for cross-origin `GM_xmlhttpRequest`.
