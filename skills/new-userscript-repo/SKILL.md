---
name: new-userscript-repo
description: Set up a brand-new development workspace for a single userscript. Asks public or private, creates the GitHub repo with the chosen visibility, installs this plugin at project scope inside it, and ends. The user opens that repo in a fresh Claude Code session and starts authoring the script with `new-userscript`.
---

# New Userscript Repo

Spin up a clean per-script workspace. This is the **bootstrap** step — it does not write the userscript itself. Once the repo exists, the user runs `new-userscript` inside it.

## Inputs

1. **Repo name** — Train-Case (e.g. `Github-Stars-Cleaner`). Will be used verbatim as the GitHub repo name and the local directory name.
2. **Visibility** — ask the user: **public** or **private**?
   - Public is the norm for shareable userscripts (Tampermonkey can install from the raw URL out of the box).
   - Private is fine for personal scripts, but warn: Tampermonkey users will need to pre-authenticate the raw URL or download manually.
3. **Base directory** — resolve in this order:
   1. `$USERSCRIPT_REPOS_BASE` env var
   2. `userscripts_base_path` in `${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/userscript-development/config.json`
   3. Ask the user (and offer to save it for next time)

## Steps

1. Confirm the name doesn't collide:
   ```bash
   test ! -d "<base>/<repo-name>" && ! gh repo view danielrosehill/<repo-name> >/dev/null 2>&1
   ```
   If either hits, ask the user for a different name. Do not overwrite.
2. Scaffold the directory:
   ```bash
   mkdir -p "<base>/<repo-name>"
   cd "<base>/<repo-name>"
   ```
3. Write a minimal `README.md`:
   ```markdown
   # <repo-name>

   Tampermonkey userscript. Authored with the [`userscript-development`](https://github.com/danielrosehill/Claude-Userscript-Development-Plugin) Claude Code plugin.

   Run `/new-userscript` inside this repo to scaffold the `.user.js`.
   ```
4. Write a standard MIT `LICENSE` (or skip if private and the user prefers no license — ask).
5. Write `.gitignore` with `.DS_Store`, `node_modules/`, `.envrc`.
6. Init git, commit, create remote with the chosen visibility:
   ```bash
   git init -q
   git add -A
   git commit -q -m "Initial commit: <repo-name> userscript workspace"
   gh repo create danielrosehill/<repo-name> --<public|private> --source=. --push
   ```
7. Install this plugin at **project scope** so it's available in that repo's Claude Code sessions but doesn't pollute the global skill list:
   ```bash
   claude plugins install userscript-development@danielrosehill --scope project
   ```
8. **End task.** Tell the user:
   - The repo URL.
   - The local path.
   - "Open a new Claude Code session in `<path>` and run `/new-userscript` to scaffold the script body."

## Guards

- Never create a public repo without an explicit "public" answer — when in doubt, default to private and let the user flip it later via `gh repo edit --visibility public`.
- Don't run `new-userscript` automatically — keep this skill's scope to workspace bootstrap only. Authoring is a separate step in a fresh session so the user's intent for the script can drive the metadata block.
