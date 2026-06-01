# keyboards

**Repo:** https://github.com/keymanapp/keyboards
**Primary language:** HTML (lots of keyboard source + docs) — keyboard source itself is `.kmn`, `.xml` (LDML), `.kps`
**Default branch:** `master`
**Size:** ~1.24 GB checked out
**Stars / Open issues:** 197 / ~37
**License:** Each keyboard carries its own license (commonly MIT or community-set)
**Activity:** Very active — daily community PRs adding or updating keyboards

## What it is

The **community repository of Keyman keyboard layouts**. This is the source of every keyboard that ends up published at `https://keyman.com/keyboards` and served via the Keyman Cloud API. Over 1,000 layouts covering ~2,500 languages live here.

## What's in the repo

Keyboards are organized by tier and then alphabetically by first letter / prefix:

| Folder | Purpose |
|---|---|
| `release/` | Stringently-checked, Unicode-only keyboards. These are what end users see by default in Keyman apps. |
| `legacy/` | Binary distributions migrated from old Tavultesoft servers. Unicode + non-Unicode. Frozen — not the place for new work. |
| `experimental/` | Keyboards that haven't yet passed quality checks; visible under the "experimental" category in Keyman 10+. PUA is allowed here. |

Within each tier, keyboards group by first letter (`a/`, `b/`, …) or by a shared author prefix (e.g. `sil/`, `gff/`) so layouts from the same maintainer cluster together.

Each keyboard folder is a self-contained Keyman Developer project:

* `<keyboard>.kmn` or `<keyboard>.xml` — source (legacy `.kmn` rules or modern LDML)
* `<keyboard>.kps` — packaging manifest
* `<keyboard>.kpj` — Keyman Developer project file
* `source/welcome/` — per-keyboard HTML help and welcome screens
* `HISTORY.md`, `LICENSE.md`, `README.md`
* Optional `DEPRECATED.md` referring users to a replacement keyboard

The root `build.sh` builds all keyboards (or a filtered subset) via the `kmc` compiler — no Keyman Developer GUI required for CI.

## General direction

* **Continuous community ingest.** PRs land almost daily; the contribution path is documented end-to-end for non-developers (zip-and-drop GitHub uploads work).
* **D.I.S.C.U.S. quality standard.** New keyboards aimed at `release/` must follow a documented design method covering layout, font, target users, etc.
* **Migration toward LDML.** The same direction as the main [keyman](https://github.com/keymanapp/keyman) repo — new keyboards increasingly authored as LDML XML rather than `.kmn`.
* **Shared fonts.** Fonts used by multiple keyboards belong in a shared folder, not duplicated per keyboard package.
* **Automated publish.** When PRs merge, the relevant pages on `help.keyman.com/keyboard/` and the Keyman Cloud catalog (`r.keymanweb.com/api/4.0/keyboards/`) update automatically.

## Useful links

* Contribution guide: https://help.keyman.com/developer/keyboards/
* Published keyboard docs: https://help.keyman.com/keyboards/
* JSON catalog API: https://r.keymanweb.com/api/4.0/keyboards/
* Community: https://community.software.sil.org/c/keyman
