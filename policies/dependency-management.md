# Dependency Management

> **[Handbook][handbook]** › **[Policies][policies]** › Dependency Management

Keeping dependencies up-to-date reduces security vulnerabilities, ensures compatibility, and benefits from upstream bug fixes. This document covers the standard process for all Zairakai projects.

---

## PHP Dependencies (Composer)

### Check for outdated packages

```bash
make outdated
# or directly:
composer outdated --direct
```

### Update all dependencies

```bash
composer update
```

### Update a specific package

```bash
composer update zairakai/laravel-dev-tools
```

### Security audit

```bash
composer audit
# or via Make:
make security-audit
```

> The CI pipeline runs `composer audit` automatically — any known vulnerability blocks the merge.

---

## JavaScript Dependencies (NPM / Yarn)

### Check for outdated packages

```bash
npm outdated
# or:
yarn outdated
```

### Interactive upgrade (recommended)

```bash
npx npm-check-updates -i
# or with yarn:
yarn upgrade-interactive --latest
```

The interactive mode lets you select which packages to upgrade, preventing accidental major version jumps.

### Update all dependencies

```bash
npm update
# or:
yarn upgrade
```

### Security audit

```bash
npm audit
# or:
yarn audit
```

---

## Strategy

### Patch and minor updates

Apply regularly — at least once per sprint, and right away for a security advisory. These are low-risk and keep the project current.

### Major updates

- Always read the **CHANGELOG** / migration guide before upgrading
- Create a dedicated branch: `chore/#TICKET-upgrade-package-name`
- Run the full quality gate after upgrading: `make quality && make test-all`
- Update the baseline if PHPStan errors appear (only pre-existing ones)

### `dev-tools` packages

`zairakai/laravel-dev-tools` and `@zairakai/js-dev-tools` are updated like any other dependency (`composer update` / `npm update`), but two things are **not** automatic:

- The `ref:` of the `include:` in `.gitlab-ci.yml` is pinned to a tag (for example `ref: 3.0.1`). Bump it by hand to the version of the installed package, in the same merge request as the dependency.
- The files published in `config/dev-tools/` (and the like) are copies owned by the project: they hold project specific settings. Do **not** run `setup-project.sh --publish --force` / `setup-package.sh --publish --force` to refresh them, it overwrites those settings. Compare with the stub and merge the differences by hand.

---

## Runtimes

### Node.js

Use the current **LTS** line (24 at the time of writing), never an odd-numbered release. Set it in:

- `engines.node` of the `package.json` (`>=24.0.0`),
- the Docker image (`zairakai/node`) and the `NODE_VERSION` / `NODE_IMAGE` variables of the CI.

A new LTS line is adopted once the Docker images are published on it. The previous line is dropped when it reaches end of life. Moving the minimum Node version of a published package is a compatibility change: release it on purpose (see [Versioning][versioning]).

### PHP

PHP has no LTS. Use the oldest version still receiving **active support** that all our packages accept (8.4 at the time of writing), and move all projects together when we change.

### Laravel

Move an application to a new Laravel major **only when every package it uses supports it**. Check it before starting:

```bash
# in a copy of the project: allow the new major and let Composer tell what blocks
composer require "laravel/framework:^13.0" -W --dry-run
```

Packages we own declare `^12.0 || ^13.0` as soon as their tests pass on both versions. If a third party package blocks, stay on the current major and update only within it (patch and minor). Then, for the move itself: one branch, `composer update -W`, the full test suite, the quality gate and `composer audit`. Do not merge on red.

---

## Docker images and services

- **Our images** (`zairakai/php`, `zairakai/node`) are rebuilt and tagged when their base image, their pinned tools or their Dockerfile change. Tags are `X.Y.Z` (see [Versioning][versioning]).
- **Pin what we depend on**: tools of the CI (`hadolint`, `shellcheck`, `kaniko`, `release-cli`, `crane`, `alpine`, `node`) are pinned to an exact version in the CI variables. Review them at each release of the images.
- **Services of the development stack** (`docker-compose.yml` and `.env.example` of the applications and the templates):
  - Prefer the current stable or **LTS** line (MySQL 8.4, nginx stable branch, Redis current major, PostgreSQL current major).
  - `latest` is accepted only for tools with no data and no compatibility risk (Mailpit, Adminer, RedisInsight).
  - Check that the image is still published and maintained. **MinIO** is no longer published on Docker Hub and not released since October 2025: the stack uses **RustFS** (S3 compatible).
  - The Playwright image must match the version of `@playwright/test` locked in `package-lock.json`.
  - A major PostgreSQL upgrade needs a new volume (the image changes its data directory): document it in the merge request.
- Test the change for real: `docker compose config`, start the services, `nginx -t` for the web server, a round trip for the object storage.

---

## Pitfalls we met

- **`npm audit fix` / `npm update` crash** (`Cannot read properties of null (reading 'edgesOut')`): a peer dependency conflict with vitest 4 and the old tooling. Moving to vitest 5 fixes it. Meanwhile, force a patched transitive version with `overrides` in the `package.json` of the project (it only applies to that project).
- **`composer cs-fix` only fixes the files changed in the working tree**: run it on all files (`composer cs` to check) before pushing.
- **PHP Insights fails on advisories**: its Security check reports any known advisory of the locked dependencies, so a new advisory can turn a green pipeline red without any code change. Update the lock.
- **Do not update only part of the PHP tooling**: with `laravel-dev-tools` 2, updating `phpstan` alone breaks `rector`. Move `laravel-dev-tools` first.
- **Vue projects**: a full `npm update` can make `prettier-plugin-organize-imports` add unused imports. Update the vulnerable packages only and investigate separately.
- **Vitest config**: run the unit tests with the project config (`--config config/dev-tools/vitest.config.js`), without it the jsdom environment is missing.
- **Runner cache**: the shared pipeline keeps `vendor/` on the runner between jobs. If a job fails with a class that does not exist after a major update, set `GIT_CLEAN_FLAGS: "-ffdx -e .composer-cache/ -e node_modules/ -e .npm/"` in the project variables.

---

## Make Commands Reference

```bash
# PHP
make outdated        # List outdated Composer dependencies
make security-audit  # Run composer audit

# JS (from @zairakai/js-dev-tools)
make outdated        # List outdated npm/yarn dependencies
```

---

**[Back to Policies][policies]**

[handbook]: ../README.md
[policies]: ./README.md
[versioning]: ./versioning.md
