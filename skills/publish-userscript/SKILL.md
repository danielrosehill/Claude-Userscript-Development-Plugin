---
name: publish-userscript
description: Publish a userscript to GitHub so it can be installed via Tampermonkey's "Install from URL" using the raw GitHub URL. Wires up `@updateURL` and `@downloadURL` in the metadata block, commits, and pushes.
---

# Publish Userscript

Make a userscript installable from a raw GitHub URL.

## How Tampermonkey installs from URL

Tampermonkey installs any URL ending in `.user.js`. The raw GitHub URL works directly:

```
https://raw.githubusercontent.com/<owner>/<repo>/main/<script-name>.user.js
```

For auto-updates Tampermonkey checks `@updateURL` (or falls back to `@downloadURL`, then to the original install URL) on its update interval and compares `@version`.

## Steps

1. Confirm the target script file path. Read its metadata block.
2. If the script lives in its own directory under the userscripts base, treat that directory as the repo. Otherwise ask the user whether to (a) create a one-script-per-repo layout or (b) push to a shared "userscripts" mono-repo.
3. If no remote exists yet:
   ```bash
   gh repo create <owner>/<script-name> --public --source=. --push
   ```
4. Compute the raw URLs (use the default branch — `main` unless the user says otherwise):
   ```
   downloadURL = https://raw.githubusercontent.com/<owner>/<repo>/<branch>/<file>.user.js
   updateURL   = same
   ```
5. Inject `@downloadURL` and `@updateURL` into the metadata block if missing. Also set `@supportURL` to the GitHub Issues URL.
6. Commit + push:
   ```bash
   git add -A
   git commit -m "Publish <script-name> v<version>"
   git push
   ```
7. Report back:
   - The install URL (user clicks it; Tampermonkey intercepts).
   - The repo URL.
   - A reminder that future updates must bump `@version` (use the `bump-version` skill).

## Notes

- Private GitHub repos won't work for Tampermonkey installs unless the user pre-authenticates the raw URL. Default to public; warn before pushing private.
- Don't gzip or minify — Tampermonkey wants the source as-is.
- Keep the file extension exactly `.user.js`.
