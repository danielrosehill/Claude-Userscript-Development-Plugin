---
name: onboard
description: First-run setup for userscript-development. Optionally records the user's userscript repos base directory so other skills can default new userscripts there. Skipping is fine — the plugin works without it.
---

# Onboard

One-time setup. Captures (optionally) where the user keeps their userscript repos so `new-userscript`, `publish-userscript`, etc. can default to that location.

## Data location

```
${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/userscript-development/config.json
```

## Steps

1. Compute the data root:
   ```bash
   DATA_ROOT="${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/userscript-development"
   mkdir -p "$DATA_ROOT"
   ```
2. Ask the user: "Where do you keep your userscript repos? (e.g. `~/repos/github/userscripts`). Press enter to skip — the plugin works without it." Accept absolute or `~`-prefixed paths; expand `~`.
3. If a path is provided, verify it exists (offer to create it if not). Then write `config.json`:
   ```json
   {
     "userscripts_base_path": "/abs/path/to/userscripts",
     "default_author": "<from git config user.name>",
     "default_namespace": "<from git config user.email or domain>"
   }
   ```
   If the user skipped, write `{}` so downstream skills can detect no preference.
4. Confirm what was saved and where.

## Environment variable alternative

The user can override per-shell by exporting `USERSCRIPT_REPOS_BASE`. Skills should prefer that env var over the saved config when both are present.
