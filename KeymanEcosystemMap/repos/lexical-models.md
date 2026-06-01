# lexical-models

**Repo:** https://github.com/keymanapp/lexical-models
**Primary language:** Shell (build scripts); models themselves are TypeScript/JSON wordlists
**Default branch:** `master`
**Size:** ~80 MB
**Stars / Open issues:** 19 / ~13
**License:** Each model carries its own; repo itself MIT
**Activity:** Moderate — dependabot bumps + occasional model updates (last substantive change Feb 2026)

## What it is

The **community repository of Keyman lexical models** — wordlists, dictionaries, and prediction models that power Keyman's predictive-text and autocorrect features on mobile and web. The companion to [keyboards](https://github.com/keymanapp/keyboards) on the "language data" side rather than the "keystrokes" side.

## What's in the repo

Models are organized in two tiers, similar to the keyboards repo:

| Folder | Purpose |
|---|---|
| `release/` | Vetted, distributed lexical models |
| `experimental/` | Models under development or not yet quality-checked |

Within each tier, models are addressed by the pattern `author/bcp47[.uniq]/`:

* **author** — short identifier (e.g. `nrc`, `sil`, `example`)
* **bcp47** — canonical BCP 47 language tag (e.g. `km`, `en-au`)
* **uniq** — optional disambiguator when one author ships multiple models for the same language (e.g. `en.custom` vs `en.wordlist`)

So a typical path looks like `release/example/en.custom/`.

A model project usually contains:

* `model.ts` — declarative TypeScript model definition consumed by `kmc`
* `wordlist.tsv` — frequency-tagged wordlist input
* `*.model.kps` — model package manifest
* `LICENSE.md`, `README.md`, `HISTORY.md`

`build.sh` at the repo root drives the `kmc` compiler to produce distributable `.model.kmp` packages.

## General direction

* **Wordlist-first models.** Most current models are wordlist-based with frequency weighting; richer statistical or ML models are an aspiration, not yet the norm.
* **Tighter integration with Keyman Web/Mobile prediction.** Improvements to the predictive engine (in the main `keyman` repo's `web/` and `core/`) drive what models are expected to provide.
* **Authors strongly encouraged to use a uniquifier.** Even for the only model in a language, naming it e.g. `en.wordlist` keeps the door open for additional models later without renaming.
* **Lower volume than keyboards.** This repo is less busy; substantive work tends to happen in bursts when a partner organization contributes a new language's wordlist.

## Useful links

* Build/contribution guide: see repo `README.md`
* Predictive text overview: https://help.keyman.com/developer/language-modeling/
* Sister repo: https://github.com/keymanapp/keyboards
