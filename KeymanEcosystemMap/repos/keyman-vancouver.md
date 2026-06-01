# keyman-vancouver

**Repo:** https://github.com/keymanapp/keyman-vancouver
**Primary language:** TypeScript
**Default branch:** `master`
**Size:** ~1.3 MB
**Stars / Open issues:** 0 / 2
**License:** MIT (Copyright SIL Global)
**Status:** **Archived** (as of early 2025) — no further work expected
**Activity:** Last substantive activity in late 2024 (Electron 30 → 33 bump, deps update); the repo was archived in 2025

## What it is

A **prototype** of a next-generation **Keyman Developer**, built on top of [Eclipse Theia](https://theia-ide.org/) and the VS Code extension model. The vision was to replace the current Windows-only, Pascal/Delphi Keyman Developer IDE with a modern, cross-platform editor that runs both in the browser and as an Electron desktop app, and that gets Keyman-specific features through plugins.

The "Vancouver" name is the codename for the prototype effort.

## What's in the repo

| Folder | Purpose |
|---|---|
| `extensions/keyman-kpj/` | VSCode plugin providing **Keyman Project** and **LDML keyboard** editing capabilities — the actual Keyman-specific intelligence |
| `keyman-vancouver/` (subdir) | Theia plugin providing the basic app personality, branding, command palette setup |
| `browser-app/` | Web/browser-hosted variant of the editor (largely scaffolded from the Theia template) |
| `electron-app/` | Electron desktop variant (also largely template-generated) |
| `plugins/` | Directory of symlinks resolved at startup; placeholder for any downloaded plugins beyond the in-repo `extensions/` |
| `package.json` + `lerna.json` | Monorepo wiring (the README notes that removing lerna/yarn was on the to-do list) |
| `.node-version` | Pins Node 20 |

## General direction

* **Archived prototype.** The repo is officially archived. It is a record of an exploratory effort, not an active product.
* **Validated the architectural shape.** What was learned — Theia + VSCode plugins as the editor host, with a dedicated `keyman-kpj` extension carrying the Keyman-specific knowledge — is the architectural pattern any future replacement is likely to take. The extension itself is the most reusable artifact.
* **Most relevant to LDML-era keyboard authoring.** The `keyman-kpj` extension was being built around the modern `.kpj` project format and LDML keyboards, aligning with the broader LDML push in the main [keyman](https://github.com/keymanapp/keyman) repo.
* **Replaces (in vision) the older online attempt.** Compared to [developer.keyman.com](https://github.com/keymanapp/developer.keyman.com), which tried to put Keyman Developer behind a SaaS facade, Vancouver took the opposite direction: a local-first, IDE-style editor running anywhere VS Code / Theia runs.

## Useful links

* Eclipse Theia: https://theia-ide.org/
* Main Keyman repo (where the current Developer still lives, under `developer/`): https://github.com/keymanapp/keyman
* Older online attempt at the same problem: https://github.com/keymanapp/developer.keyman.com
