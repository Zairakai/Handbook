# zairakai/laravel-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › laravel-dev-tools

Unified PHP/Laravel development toolchain. Provides quality gates, static analysis, code style, testing, Makefiles, git hooks, and GitLab CI templates — all configured and ready to use.

**Repository:** `PHP-Packages/laravel-dev-tools/`
**Packagist:** `zairakai/laravel-dev-tools`
**Type:** `composer-plugin`

---

## What it provides

| Category | Tools |
| :--- | :--- |
| **Code style** | Laravel Pint (PSR-12 + custom rules) |
| **Static analysis** | PHPStan max level via Larastan |
| **Code modernization** | Rector (PHP 8.3 + Laravel sets) |
| **Quality analysis** | PHP Insights (min 80% quality) |
| **Security** | Enlightn (Laravel apps) |
| **Metrics** | PHPmetrics |
| **Documentation** | Markdownlint |
| **Shell scripts** | ShellCheck (100% compliance) |
| **Testing** | PHPUnit configurations |
| **Shell testing** | BATS (Bash Automated Testing System) |
| **Git hooks** | commit-msg, pre-commit, pre-push |
| **CI** | GitLab CI pipeline templates |
| **Make system** | 13+ `.mk` files, `fullstack.mk` for PHP + JS |
| **Artisan commands** | `dev-tools:publish`, `dev-tools:clean-after-ide-helper`, etc. |

---

## Documentation

| Page | Contents |
| :--- | :--- |
| **[Setup][setup]** | Installation, setup-package.sh, publish targets, config cascade |
| **[Configs][configs]** | PHPStan, Pint, Rector, PHPInsights, PHPUnit, Markdownlint, EditorConfig |
| **[Make targets][make]** | All available `make` targets |

---

## Install

```bash
composer require --dev zairakai/laravel-dev-tools
```

The Composer plugin (`DevToolsPlugin`) runs automatically on `post-install-cmd` and `post-update-cmd`:

- Ensures `@setup-dev-tools` is in `composer.json` scripts.
- Ensures the package is in `config.allow-plugins`.
- Synchronizes GitLab CI pipeline refs on `post-update-cmd`.

To trigger setup manually:

```bash
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh
```

---

## Quick reference

```bash
make quality        # Full quality gate (all checks)
make quality-fast   # Fast CI check (Pint + PHPStan + Markdownlint)
make quality-fix    # Auto-fix (Pint + Rector)
make test           # PHPUnit
make test-coverage  # PHPUnit with coverage
make bats           # BATS shell tests
make ci             # Full CI simulation
make phpstan        # PHPStan only
make pint           # Pint only
make rector         # Rector dry-run
make insights       # PHPInsights
make shellcheck     # ShellCheck
make doctor         # Environment diagnostics
```

---

**[Back to Packages][packages]**

[handbook]: ../../README.md
[packages]: ../README.md
[setup]: ./setup.md
[configs]: ./configs.md
[make]: ./make.md
