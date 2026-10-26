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

- The `ref:` of the `include:` in `.gitlab-ci.yml` is pinned to a tag (for example `ref: 3.0.1`). Bump it by hand to the version of the installed package, in the same merge request as the dependency. For the NPM packages, the `postinstall` script of `@zairakai/js-dev-tools` rewrites this `ref:` when the package is installed: read the diff of `.gitlab-ci.yml` to be sure.
- The files published in `config/dev-tools/` (and the like) are copies owned by the project: they hold project specific settings. Do **not** run `setup-project.sh --publish --force` / `setup-package.sh --publish --force` to refresh them, it overwrites those settings. Compare with the stub and merge the differences by hand.

---

## Automated updates

Working on the two ecosystems since 2026-10-11 (NPM and Composer), validated end to end: a new version of a dev-tools package updated, tested, merged, signed and, when needed, tagged all its consumers without any manual step.

- **Where the tools live**: the update tools are in the zairakai packages, because they need GitLab and the Docker images of the group. `scripts/update-dependencies.mjs` is in `@zairakai/js-dev-tools` (Node.js) and `bin/update-dependencies` in `zairakai/laravel-dev-tools` (PHP, there is no JavaScript in the PHP image). The base packages of `Stanislas-Poisson` (`php-dev-tools`, `@stanislas-poisson/js-dev-tools`) have no update script: they only tell GitLab, after a release, to start the update of the zairakai package that requires them.
- **Two triggers, so one can fail**: the cascade starts the update as soon as a new version is published, and a weekly schedule of each project catches up if a trigger was missed (the two dev-tools packages on Monday 02:00, because they start a cascade, and the other packages on Thursday 04:00, Europe/Paris). The update does nothing when it is already done.
- **Prepare, test, publish**: the update is applied on a branch, then the quality gate and the tests run. If everything is green, the merge requests are merged and the tag is made. If anything fails, or if a major version is needed, a merge request stays open and nothing is published.
- **A cooling period of 3 days** (`cooldown`) is applied to the new versions of the dependencies, so that a compromised release is not published automatically.
- **Cascade**: once the new version is available on Packagist or npm (the job waits for it, there is no fixed hour), the projects that use it are updated. They are found through the GitLab API in the groups `npm-packages` and `php-packages`, so there is no list to maintain. The chains are `php-dev-tools` then `laravel-dev-tools` then the Laravel packages, and `@stanislas-poisson/js-dev-tools` then `@zairakai/js-dev-tools` then the JS packages.
- **Tag only when the published ranges change** (see [Versioning][versioning]): the `dev-tools` packages get a MINOR, the other packages a PATCH. A consumer that only has the dev-tools in `devDependencies` is updated and merged but not tagged.
- **Signed**: the commits and the tags of the automation are signed with a dedicated key (`Stanislas Poisson (autoupdate)`), from the group variables `GPG_PRIVATE_KEY_B64`, `RELEASE_SIGNER_NAME` and `RELEASE_SIGNER_EMAIL`. Without them the tool stops: there is no unsigned tag.
- **The exceptions live in the project**: the file `.dependency-update.json` lists what must not be updated (`reject`, with the reason), the `cooldown`, the tag policy (`tag.when`, `tag.level`) and the groups of the `cascade`.
- **The dependency policy**: the repository [zairakai/dependency-policy][dependency-policy] lists, in `node.json` and `php.json`, the packages that no project updates yet (with the reason, the date and, when it can be checked, the condition that lifts the block: the ranges of the packages that hold it back must accept the version). The update tools read it at each run, so a rule changes without a release of the tools and without a cascade. If the file cannot be read, the update stops with an alert instead of going blind. A project can still list a package of its own in `reject`.
- **Each tool handles its ecosystem**: a daily job of that repository lifts an entry by a merge request when its condition is met, and the tools take the package at their next run. `node.json` is lifted by `@zairakai/js-dev-tools` (the `peerDependencies` of the latest npm release of each peer), `php.json` by `laravel-dev-tools` (the `require` of the latest stable release of each peer on Packagist, read with the constraint parser of Composer). The job of `php.json` runs after the one of `node.json`, so the two never open a merge request at the same time.
- **A change cannot update a held back package**: `update-dependencies policy-check <ref>` (in both tools) compares the manifest and the lock file with a ref and fails when a package of the policy moves. It only reads, so it also runs on GitHub.
- **The base tools on GitHub follow the same policy**: `@stanislas-poisson/js-dev-tools` and `stanislas-poisson/php-dev-tools` keep Dependabot, which proposes the updates and raises the alerts. What it must skip is not written by hand: `update-dependencies dependabot-sync` rewrites the block between `# BEGIN dependency-policy (npm|composer)` and `# END ...` of `.github/dependabot.yml`, and the workflow `Policy sync` (every day) opens a signed pull request, set to merge when the CI passes, when it is out of date. The workflow `Policy` fails a pull request that updates a package of the policy. Both need the secrets `POLICY_SYNC_TOKEN`, `GPG_PRIVATE_KEY_B64`, `RELEASE_SIGNER_NAME` and `RELEASE_SIGNER_EMAIL` in the repository, and the workflows are read from `main`, so they must be merged there. No table or test lists the held back packages in these repositories: a test that reads such a table fails the day the block is lifted.
- **Lock files are refreshed**: the weekly update also refreshes `package-lock.json` inside the ranges (after the cooling period, the packages of the group are not made to wait), because the `overrides` of a package do not apply to the packages that use it. When npm cannot respect the cooling period, the refresh waits for the next run and the update goes on.
- **Overrides are raised too**: `npm audit fix` never changes an `overrides` entry, so an advisory on a package pinned that way would stay open. In the urgent update started by the audit, `@zairakai/js-dev-tools` raises every override whose pinned version is in the vulnerable range to the newest stable version of the same major outside of it.
- **Security advisories**: a daily job looks for them in every project. One issue per advisory (priority from the severity, status Ready) and one notification per audit, for the advisories of a moderate severity or above. An advisory that only comes through a package of the group is followed in that package and does not open a duplicate issue in the packages that use it. When an update is merged into `develop`, the issues whose advisory is gone from the tree are closed at once: a fix in a base package closes the issues of the others with the cascade.
- A major update is never merged automatically.

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

Packages we own declare `^12.0 || ^13.0` as soon as their tests pass on both versions. We support only the Laravel majors that still receive security fixes: Laravel 11 was dropped when its security support ended (March 2026), which is a MAJOR release of the packages (see [Versioning][versioning]). If a third party package blocks, stay on the current major and update only within it (patch and minor). Then, for the move itself: one branch, `composer update -W`, the full test suite, the quality gate and `composer audit`. Do not merge on red.

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
- **Vue projects**: `@vue/language-core` 3.3.12 makes `prettier-plugin-organize-imports` add `defineOptions` and `defineProps` to the imports of the `.vue` files (they are compiler macros, never imported). Keep `vue-tsc` and `@vue/language-core` on 3.3.10 (exact versions) and update `vue` itself. Remove the pin when the plugin is fixed.
- **`braces` and its chain** (`micromatch`, `fast-glob`, `globby`, then `stylelint` and `markdownlint-cli2`): the advisory GHSA-vfj7-8cjw-p6xm covered every published version until it was withdrawn on 2026-10-23. Nothing is held back for it any more and the audits fail from the `high` level. Do not soften the audit of a project to live with an advisory that has no fixed version: it hides the next ones.
- **VitePress and `vite`**: VitePress 1.6.4, the latest stable version, depends on `vite ^5.4`, a line with no fix. Force `vite ^6.4.3` with an `overrides` entry in `docs/package.json`, and check that `npm run build` still builds the site. Remove the override when VitePress moves to a fixed Vite.
- **Generated documentation files break the linters**: the build of the documentation writes `docs/guide/`, `docs/.vitepress/dist` and `docs/node_modules`, ignored by git but scanned by markdownlint. Run `git clean -fdX docs` before `make quality`.
- **`git tag` without a message fails** when `tag.gpgSign` is set: use `git tag -a X.Y.Z -m X.Y.Z`.
- **`Closes #N` is only read on the default branch**: a merge request into `develop` does not close the issue, so the one into `main` must carry it.
- **Vitest config**: run the unit tests with the project config (`--config config/dev-tools/vitest.config.js`), without it the jsdom environment is missing.
- **Do not raise the minimum of a tool that imposes framework components**: `phpinsights` 2.14.2 needs `symfony/console` 7.4 or 8, so raising its minimum in `php-dev-tools` closed the door to Symfony 6.4, which the package supports. The package adapts, not the tool: the jobs that test a framework do not install `php-dev-tools` and keep their own PHPStan rules (`phpstan-frameworks.neon`).
- **Scripts run by `postinstall` in CI mode**: when `CI` is set, `setup-project.sh` removes the target before copying the source. In `js-dev-tools` itself both are the same file, so `.editorconfig` was deleted and the automatic update committed it. A file is never copied onto itself. Read the diff of the first merge request of a new automation before arming its auto-merge.
- **The runner keeps its working directory**: the local branch of a previous run still exists, so create the update branch with `git switch -C`.
- **A token is limited to its scope**: the group `NPM_TOKEN` is refused (404 on the publication) on the scope `@stanislas-poisson`. Check what a token can write before the first release of a new package, and do not push the tag before it is ready: a tag is not moved, it is deleted with its release and made again.
- **A release workflow can run again**: if the publication fails after the GitHub release is created, the second run must not fail on the release or on the version that exist.
- **The tokens are to be reworked**: regenerate them with an expiry and a narrow scope, and add the trusted publishing on top (npm, GitHub Actions) so that the long-lived secrets can be removed. Planned with the maintainer.
- **Docker Hub tokens**: Docker deactivated a personal access token after a vulnerability notice (the e-mail names the tokens), and the `promote` jobs failed with 401. Give tokens an expiry, and read the notices of Docker before looking elsewhere.
- **`git log --grep` reads a basic regular expression**: an escaped parenthesis is a group there, so `chore\(deps\)` never matched and the tag job found no update. Write the parentheses as they are.
- **A project runs the tool of its own lock file**: a fix of `laravel-dev-tools` does not reach a package until the package is updated, so the update jobs take the newest `laravel-dev-tools` first. The same goes for the JavaScript tool, which is installed with `@latest`.
- **Add only the manifest and the lock file to an update**: `composer update` runs the plugin of `laravel-dev-tools` and `npm install` runs the `postinstall` of `js-dev-tools`, which rewrite `.editorconfig`, the `Makefile` and other files of the project in CI mode. `git add -A` committed them, and the cache of Composer of the runner too. Read the diff of the first merge request of a new automation before its auto-merge can run.
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
[dependency-policy]: https://gitlab.com/zairakai/dependency-policy
