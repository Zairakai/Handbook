# Laravel 12 Template

> **[Handbook][handbook]** › **[Templates][templates]** › Laravel 12

Production-grade Laravel 12 boilerplate — full-stack SPA with Vue 3 + TypeScript, pre-configured with the full `zairakai/*` package chain, Docker stack, CI pipeline, and quality gates.

**Location in monorepo:** `Templates/laravel-12/`

---

## What's included

| Layer | Stack |
| :--- | :--- |
| **Backend** | Laravel 12, Sanctum, Pint, PHPStan max, Rector, PHPInsights, BATS |
| **Frontend** | Vue 3, TypeScript, Pinia, Vue Router, `@zairakai/vue-components`, Vitest |
| **Tooling** | Docker (PHP + Node + MySQL + Redis + MinIO + Mailpit), Makefile, GitLab CI |
| **Dev tools** | `zairakai/laravel-dev-tools` + `@zairakai/js-dev-tools` |

---

## Documentation

| Page | Contents |
| :--- | :--- |
| **[Architecture][architecture]** | App structure, base classes, table/column naming, domain models |
| **[HTTP Layer][http]** | BaseController, BaseRequest, routes, exception handler, throttle |
| **[Frontend][frontend]** | Vue 3 setup, Vite, TypeScript, aliases |
| **[i18n][i18n]** | Translation pattern, TransController, Pinia store, locale detection |
| **[Database][database]** | Migrations, seeders, auto-discovery, `csvToSql`, factories |
| **[Tests][tests]** | PHPUnit custom assertions, Vitest, BATS |
| **[Configuration][configuration]** | Environment files, AppServiceProvider, configs, Makefile, PHPInsights |

---

## Quickstart

### 1. Replace placeholders

Search and replace across the entire project before anything else:

| Placeholder | Example | Locations |
| :--- | :--- | :--- |
| `{{VENDOR}}` | `zairakai` | `composer.json` |
| `{{APP_SLUG}}` | `my-app` | `composer.json`, `package.json`, `.env.example`, `.env.production` |
| `{{APP_NAME}}` | `My App` | `README.md`, `composer.json`, `.env.example`, `.env.production` |
| `{{APP_DESCRIPTION}}` | `Short description` | `README.md`, `package.json` |
| `{{GITLAB_PATH}}` | `zairakai/apps/my-app` | `composer.json`, `package.json` |

> Also update manually: `composer.json` → `keywords[]`, `authors[]`.

### 2. Configure environment

```bash
cp .env.example .env
# Fill in DB_HOST, DB_DATABASE, APP_KEY, and any required values
```

Seeding variables (used by `php artisan db:seed`):

| Variable | Default | Description |
| :--- | :--- | :--- |
| `ADMIN_EMAIL` | `admin@example.com` | Admin account email (`Common/UserSeeder`) |
| `ADMIN_PASSWORD` | value of `DEFAULT_PASSWORD` | Admin password override |
| `DEFAULT_PASSWORD` | `password` | Default password for all seeded accounts |

### 3. Install

```bash
make up

make shell
composer install    # also runs dev-tools setup: Makefile, git hooks, quality configs
exit

make shell-node
npm install         # also runs js-dev-tools setup: ESLint, Prettier, Vitest config
exit
```

### 4. Initialize

```bash
make shell
php artisan key:generate
php artisan migrate
php artisan db:seed   # optional — seeds admin + local users
exit
```

Run `make help` to list all available targets.

---

**[Back to Templates][templates]**

[handbook]: ../../README.md
[templates]: ../README.md
[architecture]: ./architecture.md
[http]: ./http.md
[frontend]: ./frontend.md
[i18n]: ./i18n.md
[database]: ./database.md
[tests]: ./tests.md
[configuration]: ./configuration.md
