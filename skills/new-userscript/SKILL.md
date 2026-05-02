---
name: new-userscript
description: Scaffold a new Tampermonkey userscript with a complete `==UserScript==` metadata block, sensible defaults, and a starter IIFE body. Drops it in the user's userscripts base directory if configured, otherwise asks where to place it.
---

# New Userscript

Create a new userscript file ready to load into Tampermonkey.

## Resolve the base path

Order of precedence:
1. `$USERSCRIPT_REPOS_BASE` env var
2. `userscripts_base_path` from `${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/userscript-development/config.json`
3. Ask the user

## Inputs

- **Script name** (kebab-case for filename, human-readable for `@name`)
- **Target site(s)** — for `@match` / `@include` (use `@match` by preference; it's stricter)
- **What the script does** — one sentence for `@description`
- **Run timing** — `document-start` | `document-end` | `document-idle` (default `document-idle`)
- **Needed APIs** — any `GM_*` / `GM.*` grants (default `none`)

## Metadata block reference

Use the official Tampermonkey metadata keys. Common ones:

```javascript
// ==UserScript==
// @name         <Human Name>
// @namespace    <stable URL or reverse-domain>
// @version      0.1.0
// @description  <one sentence>
// @author       <name>
// @match        https://example.com/*
// @icon         https://www.google.com/s2/favicons?sz=64&domain=example.com
// @grant        none
// @run-at       document-idle
// @license      MIT
// ==/UserScript==
```

Full key list: https://www.tampermonkey.net/documentation.php?locale=en#meta

## Body template

```javascript
(function () {
    'use strict';

    // TODO: implement
    console.log('[<script-name>] loaded on', location.href);
})();
```

## Layout

Create `<base>/<script-name>/`:
- `<script-name>.user.js` — the script (Tampermonkey requires the `.user.js` suffix for install URLs)
- `README.md` — placeholder; tell the user to run `generate-readme` later
- `.git` — `git init` it so it's ready to publish

If the user prefers single-file userscripts (no per-script directory), accept `<base>/<script-name>.user.js` instead. Ask once and remember the choice in the user's config if they want.
