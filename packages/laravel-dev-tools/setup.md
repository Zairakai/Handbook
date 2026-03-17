# Setup — laravel-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › **[laravel-dev-tools][pkg]** › Setup

---

## setup-package.sh

Central setup script. Run automatically by Composer on every `install` / `update` via the `@setup-dev-tools` hook.

```bash
# Normal setup (run automatically by composer install)
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh

# Publish specific configs to config/dev-tools/
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --publish
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --publish=quality
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --publish=markdownlint

# Include git hooks
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --with-hooks

# Force full-stack Makefile (PHP + JS)
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --fullstack

# Force overwrite (backs up existing files first)
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --force

# Silent mode (used by composer post-install — errors only)
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --silent
```

---

## Publish targets

`--publish` deploys configs to `config/dev-tools/` so they can be customized per project. Published files are hash-protected — they will not be overwritten on subsequent runs unless you use `--force`.

| Key | Source | Destination |
| :--- | :--- | :--- |
| `phpstan` | `config/library.neon` | `config/dev-tools/phpstan.neon` |
| `phpstan-baseline` | `stubs/baseline.neon.stub` | `config/dev-tools/baseline.neon` |
| `rector` | `stubs/rector.php.stub` | `config/dev-tools/rector.php` |
| `insights` | `stubs/insights.php.stub` | `config/dev-tools/insights.php` |
| `pint` | `config/pint.json` | `config/dev-tools/pint.json` |
| `markdownlint` | `.markdownlint.json` | `config/dev-tools/.markdownlint.json` + `.markdownlint.json` (root) |
| `phpunit` | `config/phpunit-app.xml` (or `phpunit.xml`) | `config/dev-tools/phpunit.xml` |
| `hooks` | `stubs/githooks/` | `.githooks/` |
| `gitlab-ci` | `stubs/gitlab-ci.*.stub` | `.gitlab-ci.yml` |
| `governance` | `stubs/governance/*.stub` | `SECURITY.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md` |

### Root phpstan.neon

Setup also generates a root `phpstan.neon` (distinct from the published `config/dev-tools/phpstan.neon`). It auto-detects project type:

- **Laravel app** (has `artisan`) → includes `app.neon` (Larastan + `app/` + `tests/`)
- **Library package** → includes `library.neon` (`src/` only)

This file is created once and never overwritten.

### Publish groups

| Group | Includes |
| :--- | :--- |
| `quality` | phpstan, phpstan-baseline, rector, insights |
| `style` | pint, markdownlint |
| `testing` | phpunit |
| `hooks` | hooks only |
| `gitlab-ci` | gitlab-ci only |
| `governance` | governance only |
| `all` | quality + style + testing + hooks (gitlab-ci and governance require explicit opt-in) |

---

## Config cascade

All scripts resolve config files using `resolve_config()` from `scripts/config.sh`:

```text
Priority 1  →  {project_root}/{filename}              (user root override)
Priority 2  →  {project_root}/config/dev-tools/{filename}   (published, customizable)
Priority 3  →  {project_root}/config/{filename}        (legacy)
Priority 4  →  vendor/zairakai/laravel-dev-tools/...   (bundled default)
```

This means you can override any tool's config at the root without modifying the published file.

---

## Makefile generation

The setup creates or updates the project `Makefile`. Two modes are detected automatically:

- **PHP only** → includes `tools/make/core.mk`
- **PHP + JS** (when `@zairakai/js-dev-tools` is in `node_modules`) → includes `tools/make/fullstack.mk`

Switching from PHP-only to full-stack:

```bash
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --fullstack
```

---

## Git hooks

Installed to `.githooks/` (versioned) and activated via `git config core.hooksPath .githooks`.

| Hook | Trigger | Action |
| :--- | :--- | :--- |
| `commit-msg` | Every commit | Validates Conventional Commits format + ticket ID |
| `prepare-commit-msg` | Before editor opens | Prepends ticket ID from branch name (if detectable) |
| `pre-commit` | Before commit | Runs `make quality-fast` (Pint + PHPStan + Markdownlint) |
| `pre-push` | Before push | Runs `make quality` (full gate) |

To skip a hook when needed:

```bash
git commit --no-verify    # skip pre-commit
git push --no-verify      # skip pre-push
```

> Bypass only when justified. Never skip in CI.

### Conventional Commits validation

The `commit-msg` hook enforces:

```text
<type>(<scope>): <description>

[optional body]

<TICKET-ID>
```

| Allowed types | `feat` `fix` `refactor` `test` `docs` `chore` `ci` `perf` `build` `style` |
| :--- | :--- |
| First line max | 72 characters |
| Description min | 10 characters |
| WIP commits | bypass validation automatically |

---

## Composer plugin — DevToolsPlugin

Runs on every `composer install` / `composer update`:

1. Ensures `zairakai/laravel-dev-tools` is in `config.allow-plugins`.
2. Ensures `@setup-dev-tools` is in `scripts.post-update-cmd`.
3. On `post-update-cmd` only: auto-synchronizes GitLab CI pipeline ref versions.

---

## Artisan commands (Laravel apps only)

| Command | Description |
| :--- | :--- |
| `php artisan dev-tools:publish` | Publishes configs to `config/dev-tools/` (same as `--publish`) |
| `php artisan dev-tools:clean-after-ide-helper` | Removes IDE helper artifacts that pollute PHPStan analysis |
| `php artisan dev-tools:remove-trailing-slashes` | Removes trailing slashes from routes |
| `php artisan dev-tools:clear-activation` | Clears activation cache |

---

**[Back to laravel-dev-tools][pkg]**

[handbook]: ../../README.md
[packages]: ../README.md
[pkg]: ./README.md
