# Configuration

> **[Handbook][handbook]** › **[Templates][templates]** › **[Laravel 11][laravel-11]** › Configuration

---

## Environment files

| File | Purpose |
| :--- | :--- |
| `.env.example` | Full template with safe defaults — copy to `.env` for local dev |
| `.env.testing` | SQLite `:memory:`, `array`/`sync` drivers, throttle 1000, Eloquent logging off |
| `.env.production` | Production template — all sensitive values empty, `SESSION_ENCRYPT=true`, `BCRYPT_ROUNDS=14` |

### Seeding variables (`.env.example` and `.env.testing`)

```env
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=
DEFAULT_PASSWORD=password
```

These are only meaningful when running `php artisan db:seed`. They are defined in `.env.testing` to support integration tests that seed a database.

### Docker service ports

Forwarded ports (`.env.example` defaults):

```env
FORWARD_DB_PORT=3306
FORWARD_REDIS_PORT=6379
FORWARD_MAILPIT_PORT=1025
FORWARD_MAILPIT_DASHBOARD_PORT=8025
FORWARD_MINIO_PORT=9000
FORWARD_MINIO_CONSOLE_PORT=9001
VITE_PORT=5173
```

---

## AppServiceProvider

```php
public function boot(): void
{
    Model::shouldBeStrict(! $this->app->environment('production'));
    DB::prohibitDestructiveCommands($this->app->environment('production'));
    $this->configureRateLimiting();
}
```

> Always use `$this->app->environment('production')` — not `->isProduction()`. The `isProduction()` method is not defined on the `Contracts\Foundation\Application` interface and will cause a PHPStan error at max level.

---

## Config files using model methods

Config files reference model methods instead of hardcoded strings to stay consistent with the `BaseTable` naming convention:

```php
// config/auth.php
'providers' => [
    'users' => [
        'model' => User::class,
    ],
],
'passwords' => [
    'users' => [
        'table' => PasswordResetToken::getTableName(),
    ],
],

// config/session.php
'table' => Session::getTableName(),

// config/queue.php
'database' => [
    'table'       => Job::getTableName(),
    'batches'     => JobBatch::getTableName(),
    'failed_jobs' => FailedJob::getTableName(),
],
```

---

## API throttle — `config/api.php`

```php
return [
    'throttle' => [
        'max_attempts'   => (int) env('API_THROTTLE_MAX_ATTEMPTS', 60),
        'decay_minutes'  => (int) env('API_THROTTLE_DECAY_MINUTES', 1),
    ],
];
```

---

## PHPInsights — `config/dev-tools/insights.php`

Rules excluded from the base config (conflicts with Pint or Eloquent):

| Rule | Reason |
| :--- | :--- |
| `ClassDefinitionFixer` | Conflicts with Pint `single_line_empty_body` |
| `ForbiddenPublicPropertySniff` | Eloquent requires `public $timestamps`, `public $incrementing`, etc. |
| `ReturnTypeHintSniff` | Cannot understand Eloquent generics (`HasMany<Model, $this>`) |
| `UseSpacingSniff` | Conflicts with Pint `blank_line_between_import_groups` — Pint wins |

> `config/insights.php` must **not** exist. PHPInsights is pointed to `config/dev-tools/insights.php` via the `INSIGHTS_CONFIG` variable in `scripts/insights.sh`. Having both will cause the wrong config to be used.

---

## Markdownlint

- `config/dev-tools/.markdownlint.json` — project config (committed, customizable).
- `.markdownlint.json` at root — extends `./config/dev-tools/.markdownlint.json` for IDE/extension support.
- `config/dev-tools/.markdownlintignore` — excludes `.cache/`, `.git/`, `node_modules/`, etc.

Both root files are required: the IDE extension looks for config at the root, while `make markdownlint` uses `resolve_config` which also finds the root file first.

---

## Makefile

```text
Makefile        ← includes vendor/.../fullstack.mk + .make/docker.mk
.make/docker.mk ← Docker targets
```

### PHP targets

```bash
make quality        # markdownlint + shellcheck + rector + pint + phpstan + insights
make quality-fast   # pint + phpstan + markdownlint (fast CI check)
make quality-fix    # pint-fix + rector-fix
make test           # PHPUnit (all suites)
make test-unit      # PHPUnit Unit/ only
make test-feature   # PHPUnit Feature/ only
make test-coverage  # PHPUnit with coverage report
make bats           # BATS shell tests
make test-all       # test + bats
make phpstan        # PHPStan analysis only
make pint           # Pint code style check
make pint-fix       # Pint auto-fix
make rector         # Rector dry-run
make rector-fix     # Rector apply
make insights       # PHPInsights analysis
make markdownlint   # Markdown lint
make shellcheck     # Shell scripts lint
```

### JS targets (requires `@zairakai/js-dev-tools`)

```bash
make eslint         # ESLint check
make eslint-fix     # ESLint auto-fix
make prettier       # Prettier format check
make prettier-fix   # Prettier auto-fix
make stylelint      # CSS/SCSS lint
make typecheck      # TypeScript tsc --noEmit
make knip           # dead code + unused deps
make js-test        # Vitest run
make js-test-ci     # Vitest CI mode (strict + coverage)
```

### Full CI simulation

```bash
make ci     # quality + test-all + js-test-ci
```

### Docker targets (`.make/docker.mk`)

```bash
make up             # start all Docker services
make down           # stop services
make shell          # PHP container shell
make shell-node     # Node container shell
make logs           # tail container logs
make docker-ci      # run CI inside Docker
```

`.make/` uses the hidden-directory convention (consistent with `.github/`, `.husky/`).

---

**[Back to Laravel 11][laravel-11]**

[handbook]: ../../README.md
[templates]: ../README.md
[laravel-11]: ./README.md
