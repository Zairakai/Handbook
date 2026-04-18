# Templates

> **[Handbook][handbook]** › Templates

Production-ready boilerplates for new Zairakai projects. Each template is pre-configured with the full dev-tools chain, CI pipeline, Docker stack, and coding standards enforced by quality gates.

---

## Available Templates

| Template | Stack | Description |
| :--- | :--- | :--- |
| ~~[Laravel 11][laravel-11]~~ | Laravel 11 + Vue 3 + TypeScript | **ARCHIVED** — L11 EOL. |
| **[Laravel 12][laravel-12]** | Laravel 12 + Vue 3 + TypeScript | Full-stack boilerplate — API, Blade, frontend, auth, i18n, seeders, Vitest, BATS. |
| **Laravel 13** | Laravel 13 + Vue 3 + TypeScript | Same as Laravel 12 boilerplate, targeting `laravel/framework: ^13.0`. |
| **[npm-package][npm-package]** | TypeScript — ESM + CJS | Boilerplate for `@zairakai/*` NPM packages. |
| **[php-package][php-package]** | PHP 8.4 — Laravel 12\|13 | Boilerplate for `zairakai/*` Composer packages. |

---

## How to Use a Template

Templates live in `Templates/` in the monorepo. To start a new project from a template:

1. Copy the template directory to your project location.
2. Follow the **Setup** section in the template's handbook page.
3. Search and replace all placeholders (listed per template).
4. Run `composer install && npm install` — dev-tools configure themselves automatically via `postinstall` hooks.

---

**[Back to Handbook][handbook]**

[handbook]: ../README.md
[laravel-11]: ./laravel-11/README.md
[laravel-12]: ./laravel-12/README.md
[npm-package]: ./npm-package/README.md
[php-package]: ./php-package/README.md
