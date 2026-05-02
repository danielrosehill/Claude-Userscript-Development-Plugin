# Userscript Development

Develop, test, and publish Tampermonkey (and Greasemonkey-compatible) userscripts from inside Claude Code — scaffolding, in-browser validation via Claude in Chrome, README generation, semver-aware version bumping.

## Skills

- `onboard` — optional one-time setup. Records the user's userscript repos base directory so other skills can default new scripts there.
- `new-userscript` — scaffold a new `.user.js` with a complete `==UserScript==` metadata block.
- `test-userscript` — validate behavior live in a real Chrome tab via the Claude in Chrome MCP.
- `publish-userscript` — push to GitHub and wire `@updateURL` / `@downloadURL` so Tampermonkey can install from the raw URL.
- `generate-readme` — build a clean repo README from the metadata block.
- `bump-version` — increment `@version` (Tampermonkey only auto-updates on monotonic version increase), commit, tag, push.
- `docs-lookup` — fetch the official Tampermonkey docs on demand.

## Optional configuration

The user can declare where they keep their userscript repos:

- **Env var:** `USERSCRIPT_REPOS_BASE=/path/to/userscripts` (takes precedence)
- **Config:** `userscripts_base_path` written by `onboard` to
  `${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/userscript-development/config.json`

Skipping is fine — skills will ask for a path each time if neither is set.

## Installation

```bash
claude plugins install userscript-development@danielrosehill
```

## Reference

Tampermonkey documentation: <https://www.tampermonkey.net/documentation.php?locale=en>

## License

MIT
