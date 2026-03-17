# Tests

> **[Handbook][handbook]** › **[Templates][templates]** › **[Laravel 12][laravel-12]** › Tests

Three test layers: **PHPUnit** (PHP), **Vitest** (frontend TypeScript), **BATS** (shell scripts).

---

## PHPUnit

### Structure

```text
tests/
├── TestCase.php
├── Unit/
│   ├── ExampleTest.php
│   └── Models/
│       └── TableResolutionTest.php     ← 11 tests: table name, column resolution, PK
└── Feature/
    ├── ExampleTest.php
    ├── Api/
    │   ├── HealthTest.php
    │   └── ExceptionHandlerTest.php    ← JSON 404 on API, HTML 404 on web
```

### Custom assertions in `TestCase`

| Method | What it checks |
| :--- | :--- |
| `assertApiSuccess($response, $status)` | `status` field, HTTP code |
| `assertApiError($response, $status)` | `status` + `message` fields, HTTP code |
| `assertApiPaginated($response)` | `data`, `meta`, `links` structure |
| `assertApiValidationError($response, $fields)` | `errors` keys match expected fields |

### `withoutVite()`

All Feature tests that call `GET /` **must** use `withoutVite()`:

```php
// ✅ Correct
public function test_home_returns_200(): void
{
    $this->withoutVite()
         ->get('/')
         ->assertOk();
}

// ❌ Will fail — Vite manifest not found in test environment
public function test_home_returns_200(): void
{
    $this->get('/')->assertOk();
}
```

The SPA wrapper (`welcome.blade.php`) calls `@vite(...)`, which fails without a built manifest.

### PHPStan typing for tests

```php
// LengthAwarePaginator
/** @var LengthAwarePaginator<int, User> $paginator */

// TestResponse
/** @var TestResponse<Response> $response */
$response = $this->getJson('/api/v1/health');
```

### Running tests

```bash
make test           # all PHPUnit tests
make test-unit      # Unit/ only
make test-feature   # Feature/ only
make test-coverage  # with HTML + LCOV coverage report
```

Config: `config/dev-tools/phpunit.xml`

---

## Vitest

Frontend tests for TypeScript/Vue code.

### Structure

```text
tests/
└── js/
    └── stores/
        └── i18n.test.ts    ← 5 smoke tests for useI18nStore
```

### Test setup

```typescript
import { createPinia, setActivePinia } from 'pinia'
import { beforeEach, describe, expect, it, vi } from 'vitest'

describe('useI18nStore', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
    vi.restoreAllMocks()
  })
  // ...
})
```

Always call `setActivePinia(createPinia())` in `beforeEach` — Pinia stores are stateful and must be reset between tests.

### Covered cases — `i18n.test.ts`

| Test | Verifies |
| :--- | :--- |
| `has correct initial state` | `locale === defaultLocale`, `loaded === false` |
| `loads a locale and marks store as loaded` | fetch called once, `loaded === true`, `locale === 'fr'` |
| `falls back to fallbackLocale for unknown locale` | `locale === fallbackLocale` when unknown locale passed |
| `does not fetch again if locale already loaded` | cache hit — `fetch` called exactly once on second call |
| `does not mark as loaded if fetch fails` | `loaded` stays `false` on HTTP error |

### Configuration

- Config: `config/dev-tools/vitest.config.js` — extends `@zairakai/js-dev-tools` base + Vue plugin + jsdom environment.
- Invoked via `make js-test` → `scripts/test.sh` → `vitest run --config config/dev-tools/vitest.config.js`.
- Coverage (CI): `make js-test-ci` → adds `--coverage` → reports in `build/coverage/`.

### Running tests

```bash
make js-test          # Vitest run (no coverage)
make js-test-coverage # with coverage report
make js-test-ci       # CI mode: strict + coverage
```

---

## BATS

Shell script tests using Bash Automated Testing System.

### Structure

```text
tests/bats/
├── unit/
│   └── example.bats        ← artisan executable, health route registered, config valid
└── integration/
    └── example.bats        ← route list output, artisan about
```

### Route pattern in BATS

Routes are checked from `artisan route:list --json` output. Slashes in route URIs are escaped in the JSON output:

```bash
# ✅ Correct pattern
run bash -c "php artisan route:list --json | grep 'v1\/health'"
assert_success

# ❌ Wrong — unescaped slash will not match
run bash -c "php artisan route:list --json | grep 'v1/health'"
```

### Running tests

```bash
make bats               # all BATS tests (unit + integration)
make bats-unit          # unit only
make bats-integration   # integration only
```

---

## Running everything

```bash
make test-all   # PHPUnit + BATS
make ci         # quality + test-all + js-test-ci (full CI simulation)
```

---

**[Back to Laravel 12][laravel-12]**

[handbook]: ../../README.md
[templates]: ../README.md
[laravel-12]: ./README.md
