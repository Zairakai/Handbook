# Dev Tools Packages

> **[Handbook][handbook]** › Packages

Centralized development toolchain packages used across all Zairakai projects.

---

## Available packages

| Package | Type | Description |
| :--- | :--- | :--- |
| **[zairakai/laravel-dev-tools][laravel-dev-tools]** | Composer | PHP/Laravel toolchain: PHPStan, Pint, Rector, PHPInsights, BATS, Makefile, git hooks, GitLab CI |
| **[@zairakai/js-dev-tools][js-dev-tools]** | NPM | JS/TS toolchain: ESLint, Prettier, Stylelint, Vitest, Knip, TypeScript, Makefile, git hooks |

### Base packages

The generic part of each toolchain lives in a package of the personal account, which the zairakai package requires and extends. The paths of the zairakai package do not change.

| Package | Type | Used by | Description |
| :--- | :--- | :--- | :--- |
| **stanislas-poisson/php-dev-tools** | Composer | zairakai/laravel-dev-tools | Pint, PHPStan, Rector, PHP Insights, markdownlint and Git hooks, without Laravel |
| **@stanislas-poisson/js-dev-tools** | NPM | @zairakai/js-dev-tools | ESLint, Prettier, Stylelint, Vitest, TypeScript and markdownlint configurations |

The zairakai packages add the Makefiles, the GitLab CI templates, the Git hooks that call `make` and the [automatic update of the dependencies][dependency-management]. The base packages are hosted on GitHub; a release there starts the update of the zairakai package on GitLab.

---

## Design principles

Both packages follow the same architecture:

- **Zero configuration out of the box** — sane defaults bundled, override only what you need.
- **Config cascade** — project root → `config/dev-tools/` → `config/` → bundled vendor default.
- **Hash-protected updates** — setup scripts detect user-modified files and never overwrite them.
- **Modular Makefiles** — each tool has its own `.mk` file, composed at the project level.
- **100% ShellCheck compliance** — all shell scripts must pass ShellCheck without warnings.

---

**[Back to Handbook][handbook]**

[handbook]: ../README.md
[dependency-management]: ../policies/dependency-management.md
[laravel-dev-tools]: ./laravel-dev-tools/README.md
[js-dev-tools]: ./js-dev-tools/README.md
