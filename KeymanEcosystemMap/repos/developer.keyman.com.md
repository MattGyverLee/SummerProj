# developer.keyman.com

**Repo:** https://github.com/keymanapp/developer.keyman.com
**Primary language:** TypeScript (NestJS backend + Angular frontend)
**Default branch:** `master`
**Size:** ~2.3 MB
**Stars / Open issues:** 4 / ~10
**License:** MIT
**Activity:** Stalled — most recent commits are dependabot bumps from mid-2022; no substantive feature work since then

## What it is

**Keyman Developer Online (KDO).** A web app — NestJS backend + Angular frontend — intended to let people contribute *new keyboards* to keyman.com without having to download the ~1 GB Keyman Developer install. It's essentially a browser-resident, lightweight onboarding path into the keyboards repository.

## What's in the repo

* `backend/` (NestJS) — REST API for the app, GitHub OAuth integration, server-side keyboard build orchestration
* `frontend/` (Angular) — the user-facing single-page app
* `test_kdo_khmer_angkor`-style end-to-end test fixtures using a forked test GitHub user
* `docker-compose.yml` — used to package the app for deployment
* `gulp` task pipeline tying install/build/test/e2e together across both halves

Required setup includes creating a GitHub OAuth app (the user signs in with GitHub so KDO can push keyboard PRs on their behalf) and providing `CLIENT_ID` / `CLIENT_SECRET` / `SESSION_SECRET` via `development.env` or `production.env`.

## General direction

* **Effectively dormant.** Pinned to Node 12.x / npm 6.13 and last touched (substantively) in 2022. The recent commit log is purely automated dependency bumps. There is no public deprecation notice, but the project is clearly not the active path for contributing keyboards today.
* **Superseded in practice by simpler GitHub flows.** The current documented contribution route for keyboards (see [keyboards](https://github.com/keymanapp/keyboards)) is a GitHub-native fork + ZIP-upload workflow, which removes much of KDO's original raison d'être.
* **Potentially replaced by [keyman-vancouver](https://github.com/keymanapp/keyman-vancouver).** That prototype takes the inverse approach — a Theia/VSCode-style editor rather than a hosted SaaS — and is the stated direction for a "next-generation Keyman Developer."
* **Useful as reference.** The architecture (Angular SPA + NestJS API + GitHub OAuth + e2e test user) remains a reasonable template if a future hosted variant is revived.

## Useful links

* Project README: https://github.com/keymanapp/developer.keyman.com#readme
* NestJS: https://nestjs.com/
* Test fixture repo: https://github.com/keymanapp/test_kdo_khmer_angkor
