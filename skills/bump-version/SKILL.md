---
name: bump-version
description: Bump a userscript's `@version` (semver), update README, commit, tag, and push. Tampermonkey only auto-updates installed scripts when `@version` increases — forgetting this means users never receive fixes.
---

# Bump Version

Increment the `@version` field of a userscript and ship the bump.

## Inputs

- Path to the `.user.js` file (or repo root — auto-detect if only one)
- Bump type: `patch` (default) | `minor` | `major` | explicit `X.Y.Z`
- Optional: changelog line for the commit message

## Steps

1. Read the script's metadata block. Extract current `@version`. If missing or non-semver, ask the user for an explicit version and warn that Tampermonkey requires monotonic versions for update detection.
2. Compute the new version:
   - `patch`: `1.2.3` → `1.2.4`
   - `minor`: `1.2.3` → `1.3.0`
   - `major`: `1.2.3` → `2.0.0`
   - Or use the user's explicit value (must be greater than current).
3. Update the `@version` line in place. **Touch nothing else** in the metadata block.
4. If a `README.md` exists with the version embedded, update it too — or just suggest the user re-run `generate-readme`.
5. Commit, tag, push:
   ```bash
   git add -A
   git commit -m "Bump version to <new>: <changelog-line>"
   git tag "v<new>"
   git push && git push --tags
   ```
6. Report:
   - Old → new version
   - The install URL (unchanged) and a note that already-installed users will pick up the update on Tampermonkey's next check (default ~24h; user can force via "Check for userscript updates").

## Guards

- Never decrease the version. If the user asks, confirm explicitly — Tampermonkey will refuse to update.
- Don't bump if there are no committed changes since the last tag (warn and ask).
