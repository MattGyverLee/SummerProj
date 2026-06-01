# help.keyman.com

**Repo:** https://github.com/keymanapp/help.keyman.com
**Primary language:** PHP (with HTML, JS, CSS)
**Default branch:** `master`
**Size:** ~298 MB
**Stars / Open issues:** 9 / ~45
**License:** MIT (Copyright SIL Global)
**Activity:** Very active — docs PRs land daily

## What it is

The **source for https://help.keyman.com/**, the public documentation site for Keyman. The site is served by Apache inside a Docker container; this repo bundles the site code, templates, and the portion of the documentation that is authored *here*.

Importantly, **a lot of the published documentation does not live in this repo** — it's deployed in from other repositories. This repo is partly a documentation source and partly a documentation host.

## What's in the repo

* Apache/PHP site structure (the docs portal itself)
* Page templates, navigation, search, and styling
* Documentation that is *not* sourced from elsewhere (e.g. some product overviews, top-level help pages)
* `build.sh` — Docker-based build/run/test entry point
* Test suite — PHP unit tests, `.php` conformance checks, broken-link checks

### Docs deployed *into* this site from other repos

| Path on help.keyman.com | Source repo |
|---|---|
| `/keyboard/` | [keymanapp/keyboards](https://github.com/keymanapp/keyboards) — deployed on PR merge |
| `/products/android/` | [keymanapp/keyman](https://github.com/keymanapp/keyman) |
| `/products/iphone-and-ipad/` | keymanapp/keyman |
| `/products/linux/` | keymanapp/keyman |
| `/products/mac/` | keymanapp/keyman |
| `/products/windows/` | keymanapp/keyman |
| `/developer/<version>/` (versioned) | keymanapp/keyman |
| `/developer/engine/{android,iphone-and-ipad,web,windows}/` | keymanapp/keyman |
| `/developer/core/` | keymanapp/keyman |

The deploy from `keymanapp/keyman` is driven by `resources/build/help-keyman-com.sh` in that repo.

## Running it locally

* `./build.sh build` — build the Docker image (use `--debug` to include dev deps for tests)
* `./build.sh start --debug` — start the site at `http://localhost:8055` (or `http://help.keyman.com.localhost:8055`)
* `./build.sh stop` / `./build.sh clean`
* `./build.sh test` — PHP unit tests + conformance + broken-link checks

For running multiple keyman.com sites side-by-side, the recommendation is to use the [website-local-proxy](https://github.com/keymanapp/website-local-proxy) (one shared port) and [shared-sites](https://github.com/keymanapp/shared-sites) (coordinated startup/shutdown).

## General direction

* **Stable, conventional PHP/Apache stack.** No imminent rewrite — the focus is content quality and search/navigation polish, not platform change.
* **Single front door, multiple sources.** The architectural choice is to keep documentation alongside the thing it documents (keyboards, the main app, lexical models) and let the help site ingest, rather than to centralize all writing here. That shape is unlikely to change.
* **Versioned developer docs.** `/developer/<version>/` paths are maintained per Keyman Developer release so older versions remain referenced.
* **Active docs work.** Recent commits show steady content updates (release notes, knowledge-base entries, instructions for specific edge cases).

## Related repos

* [api.keyman.com](https://github.com/keymanapp/api.keyman.com) — DB backend for the keyman.com sites
* [keyman.com](https://github.com/keymanapp/keyman.com) — marketing/home site
* [keymanweb.com](https://github.com/keymanapp/keymanweb.com) — online keyboard site
* [s.keyman.com](https://github.com/keymanapp/s.keyman.com) — static JS, fonts, shared resources
