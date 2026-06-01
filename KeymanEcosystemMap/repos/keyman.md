# keyman

**Repo:** https://github.com/keymanapp/keyman
**Primary language:** Pascal (with C++, TypeScript, Swift, Kotlin, Objective-C, JavaScript, PHP, Shell)
**Default branch:** `master`
**Size:** ~508 MB checked out
**Stars / Open issues:** 514 / ~1,186
**License:** MIT (Copyright SIL Global)
**Activity:** Very active — daily commits, auto version bumps (e.g. `19.0.241` as of late May 2026)

## What it is

The **monorepo** for the whole Keyman cross-platform input-methods system. Keyman lets users type in any language on Windows, macOS, Linux, iOS, Android, and the web. This single repository holds the engine, the apps, the developer tools, and the build/release infrastructure for every supported platform.

## What's in the repo

Top-level layout (each is its own platform/component tree):

| Folder | Purpose |
|---|---|
| `android/` | Keyman for Android (Java/Kotlin app + JNI to core) |
| `ios/` | Keyman for iPhone/iPad (Swift/Obj-C) |
| `mac/` | Keyman for macOS |
| `windows/` | Keyman for Windows (Pascal/Delphi — the historical core platform) |
| `linux/` | Keyman for Linux (IBus integration) |
| `web/` | KeymanWeb — in-browser keyboard engine (TypeScript) |
| `core/` | Cross-platform C++ keyboard processor (`libkeyman_core`) shared by all platforms |
| `developer/` | Keyman Developer — the IDE/compiler (`kmc`) for authoring keyboards and lexical models |
| `common/` | Shared models, schemas, web/JS libraries used across platforms |
| `oem/` | OEM/embedded builds (e.g. FirstVoices) |
| `resources/` | Build scripts, CI helpers, version tooling, doc-deployment scripts |
| `docs/` | Repo-internal contributor docs (architecture, build, release process) |

`build.sh` at the repo root is the unified entry point for builds across all child components.

## General direction

* **Convergence on a shared C++ core.** Historically, each platform had its own implementation; the modern direction is everything (web, mobile, desktop) calling into `core/` so behavior matches across OSes. New keyboard features land in core first.
* **LDML keyboard format support.** Major ongoing investment in the Unicode LDML Keyboard 3 standard — the next-generation, declarative alternative to the legacy `.kmn` source format. Keyman is the reference implementation upstream of CLDR.
* **Modern web tooling.** The web engine is moving toward standard TypeScript packaging, ESM, and pnpm/Node 18+ workflows.
* **Continuous release cadence.** Auto-bumped patch versions on `master`; stable, beta, and alpha channels per platform.
* **Hacktoberfest / community-friendly.** Tagged for community contribution; many touch points (keyboards, lexical models, web demos) are intentionally easy entry-level work.

## Key relationships to other repos

* Keyboards authored here (in `developer/`) get published into [keyboards](https://github.com/keymanapp/keyboards).
* Lexical models compiled with `kmc` live in [lexical-models](https://github.com/keymanapp/lexical-models).
* Help content is **pushed out** to [help.keyman.com](https://github.com/keymanapp/help.keyman.com) by `resources/build/help-keyman-com.sh` whenever docs change here.
* This is the upstream for all the platform installers distributed via [downloads.keyman.com](https://github.com/keymanapp/downloads.keyman.com).

## Useful links

* Home: https://keyman.com
* Online: https://keymanweb.com
* Docs: https://help.keyman.com
* Status: https://status.keyman.com
