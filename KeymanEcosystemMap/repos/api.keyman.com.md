# api.keyman.com

**Repo:** https://github.com/keymanapp/api.keyman.com
**Primary language:** PHP
**Default branch:** `master`
**Size:** ~13 MB
**Stars / Open issues:** 3 / ~15
**License:** MIT (Copyright SIL Global)
**Activity:** Moderate — recent dependency / packaging maintenance (May 2026); bug fixes a few times a year

## What it is

The **source for https://api.keyman.com/** — the **database backend that powers the keyman.com family of websites**. It exposes API endpoints used by `keyman.com`, `keymanweb.com`, `help.keyman.com`, and `s.keyman.com` for things like keyboard catalog lookups, language metadata, downloads metadata, etc.

It is a relatively small, focused service: PHP on Apache, backed by **SQL Server for Linux**, both running as Docker containers.

## What's in the repo

* PHP source for the API endpoints
* SQL schema and seed data
* Docker configuration for the Apache + SQL Server two-container setup
* `build.sh` — Docker-based build/run/test entry point
* `tests/data/` — mock data for the test suite

## Running it locally

* `./build.sh build` — build the Docker images
* `./build.sh start --debug` — start the containers; site at `http://localhost:8058` or `http://api.keyman.com.localhost:8058`
* `./build.sh stop` / `./build.sh clean`
* `./build.sh test` — API/conformance/link tests against mock data
* `./build.sh test --rebuild-test-fixtures` — force-rebuild the test DB from `tests/data/` (use when the schema changes)

As with the rest of the site family, [website-local-proxy](https://github.com/keymanapp/website-local-proxy) and [shared-sites](https://github.com/keymanapp/shared-sites) are the recommended way to bring up several keyman.com sites together.

## General direction

* **Quiet, stable infrastructure.** The site shape (PHP + SQL Server for Linux in Docker) is established and the trajectory is "keep it running and current," not "modernize the stack." Recent work has been about dependency hygiene, `composer.json` plugin allowlists, and the BOOTSTRAP package version.
* **Backend-for-frontend role.** Treat this less as a public REST product and more as the data layer the other keyman.com sites depend on. Most consumers are the other repos in this org, not external developers.
* **SQL Server choice is load-bearing.** Schema and queries are written for SQL Server (specifically the Linux container build), which colors what's easy or hard to change.

## Related repos

* [help.keyman.com](https://github.com/keymanapp/help.keyman.com)
* [keyman.com](https://github.com/keymanapp/keyman.com)
* [keymanweb.com](https://github.com/keymanapp/keymanweb.com)
* [s.keyman.com](https://github.com/keymanapp/s.keyman.com)
* [website-local-proxy](https://github.com/keymanapp/website-local-proxy)
* [shared-sites](https://github.com/keymanapp/shared-sites)
