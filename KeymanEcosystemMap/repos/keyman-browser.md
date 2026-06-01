# keyman-browser

**Repo:** https://github.com/keymanapp/keyman-browser
**Primary language:** Dart (Flutter)
**Default branch:** `main`
**Size:** ~1.9 MB
**Stars / Open issues:** 2 / 3
**License:** MIT (Copyright SIL Global)
**Activity:** Low — last meaningful changes Nov 2025 (bookmark feature, permissions tweak); the project is newer and not yet under heavy development

## What it is

A **Flutter-based mobile web browser** (Android-first, iOS planned) that ships with built-in **font replacement**. The point is to solve the "tofu" / square-box problem: even when a website doesn't embed a font for a script, Keyman Browser injects an appropriate font (drawn from Keyman keyboard packages) so the page renders correctly in that language.

It is a satellite app in the Keyman ecosystem — not part of the main `keyman` monorepo because it's its own deployable application with its own Flutter toolchain.

## What's in the repo

| Folder / file | Purpose |
|---|---|
| `lib/` | Flutter app source (Dart) |
| `android/` | Android-specific configuration and platform code |
| `ios/` | iOS scaffolding (the iOS port is still TODO — see below) |
| `assets/` | Bundled resources |
| `test/` | Widget / unit tests |
| `pubspec.yaml` | Flutter dependency manifest |
| `build.sh` | Convenience build script |
| `VERSION.md` | Version pin |

Minimum Android API level: 21.

## General direction

* **Android first, iOS to follow.** The README explicitly enumerates the remaining iOS work: create an app group shared between Keyman and Keyman Browser, surface the current Keyman-for-iOS font name into a shared container, and wire a Flutter `MethodChannel` on iOS to read it.
* **Tight coupling to the user's installed Keyman keyboards.** The fonts injected into the browser come from the keyboard packages the user already has installed — Keyman Browser is a presentation layer on top of that font catalog rather than carrying its own.
* **Aimed at minority-language users in the field.** The product motivation is reading-side parity for the same languages Keyman keyboards enable on the writing side: people who can type their language should also be able to read it on the open web.
* **Small surface area.** This is a focused single-purpose app, not a general-purpose browser; expect feature growth to stay narrow (bookmarks, font injection, basic navigation).

## Useful links

* Keyman ecosystem home: https://keyman.com
* Documentation: https://help.keyman.com
* Main Keyman repo: https://github.com/keymanapp/keyman
