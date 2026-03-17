# Dev Tools Packages

> **[Handbook][handbook]** › Packages

Centralized development toolchain packages used across all Zairakai projects.

---

## Available packages

| Package | Type | Description |
| :--- | :--- | :--- |
| **[zairakai/laravel-dev-tools][laravel-dev-tools]** | Composer | PHP/Laravel toolchain: PHPStan, Pint, Rector, PHPInsights, BATS, Makefile, git hooks, GitLab CI |
| **[@zairakai/js-dev-tools][js-dev-tools]** | NPM | JS/TS toolchain: ESLint, Prettier, Stylelint, Vitest, Knip, TypeScript, Makefile, git hooks |

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
[laravel-dev-tools]: ./laravel-dev-tools/README.md
[js-dev-tools]: ./js-dev-tools/README.md
