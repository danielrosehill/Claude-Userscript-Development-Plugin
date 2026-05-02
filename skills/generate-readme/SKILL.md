---
name: generate-readme
description: Generate or refresh a userscript repo's README.md from its `==UserScript==` metadata block — install button, what it does, supported sites, version, license. Idempotent; safe to re-run after metadata changes.
---

# Generate README

Build a clean README for a userscript repo by parsing its metadata block.

## Steps

1. Locate the `.user.js` file. If multiple exist, ask which one is canonical (or generate a multi-script README).
2. Parse the metadata block — extract: `@name`, `@description`, `@version`, `@author`, `@match` (all of them), `@grant`, `@license`, `@homepageURL`, `@supportURL`, `@downloadURL`.
3. Derive the install URL from `@downloadURL` if present, else compute it from the git remote.
4. Render this template (replace placeholders, drop sections with no data):

```markdown
# <@name>

<@description>

## Install

[**Click here to install in Tampermonkey**](<install-url>)

> Requires the [Tampermonkey](https://www.tampermonkey.net/) browser extension.

## What it does

<one or two paragraphs — ask the user to expand if @description is too terse>

## Supported sites

- `<@match #1>`
- `<@match #2>`

## Permissions

This script requests: `<@grant list, or "none">`.

## Version

Current version: **<@version>**. See [commit history](<repo-url>/commits/main) for changes.

## Author

<@author> — <@homepageURL or git remote owner URL>

## License

<@license>

## Issues / feedback

<@supportURL or repo Issues URL>
```

5. Write to `README.md` (overwrite). Show the user the diff vs. previous version.

## Notes

- Don't invent features that aren't in the script. Read the body for clues, but if behavior is non-obvious ask the user.
- Keep it short — a userscript README that's longer than the script itself is a smell.
