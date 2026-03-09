# Testing Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › Testing

---

## Coverage Target

The target is **100% line and method coverage** — aiming for it is encouraged across all Zairakai packages.  
The hard rule is: **coverage must not decrease**. A contribution that drops coverage below the current baseline will not be merged.

> Coverage is a **floor**, not a ceiling. Tests must be meaningful, not just coverage-chasing.

---

## PHPUnit — PHP Projects

### Structure

```bash
tests/
├── Unit/           # Pure logic — no framework, no DB, no HTTP
│   ├── Services/
│   └── ValueObjects/
└── Feature/        # Laravel integration — HTTP, DB, jobs, events
    └── Api/
```

### Naming

- Test class: `{ClassUnderTest}Test` — `UserServiceTest`, `PivotChangeSetTest`
- Test method: `#[Test]` attribute + `it_` prefix — `it_{what}_{condition}`

```php
#[Test]
public function it_builds_all_query_as_union(): void

#[Test]
public function it_syncs_with_empty_array_and_clears_all_relations(): void
```

Do **not** use the `test_` prefix — use the `#[Test]` attribute instead (PHPUnit 10+).

### Anatomy of a Test

```php
final class UserServiceTest extends TestCase
{
    #[Test]
    public function it_creates_user_with_valid_data(): void
    {
        // Arrange
        $data = [
            'name'  => 'Alice',
            'email' => 'alice@example.com',
        ];

        // Act
        $user = $this->service->create($data);

        // Assert
        $this->assertInstanceOf(User::class, $user);
        $this->assertSame('Alice', $user->name);
    }
}
```

- **One assertion concept per test** — multiple `assert*` calls are fine if they validate the same concept
- Use `final class` for test classes
- Avoid `setUp()` for complex logic — prefer factory methods

### Coverage Annotations

For code that PCOV cannot reach (facades, abstract constructors, promoted properties):

```php
// @codeCoverageIgnore      — single line
// @codeCoverageIgnoreStart — block start
// @codeCoverageIgnoreEnd   — block end
```

Do **not** annotate real business logic — fix the test instead.

---

## Vitest — JavaScript / TypeScript Projects

### Structure

```bash
tests/
├── unit/           # Pure functions and composables
└── integration/    # Component + store interaction
```

### Naming

```typescript
describe('UserService', () => {
  it('returns user when id is valid', async () => { })
  it('throws NotFound when id is unknown', async () => { })
})
```

### Example

```typescript
import { describe, it, expect, vi } from 'vitest'
import { useUserStore } from '@/stores/User'
import { createPinia, setActivePinia } from 'pinia'

describe('useUserStore', () => {
  beforeEach(() => {
    setActivePinia(createPinia())
  })

  it('sets current user on login', () => {
    const store = useUserStore()
    store.setUser({ id: 1, name: 'Alice' })

    expect(store.isAuthenticated).toBe(true)
    expect(store.currentUser?.name).toBe('Alice')
  })
})
```

---

## BATS — Shell Scripts

BATS (Bash Automated Testing System) tests all shell scripts in `scripts/`.

### Structure

```bash
tests/bats/
├── helpers/
│   └── test_helper.bash
├── unit/
│   └── package-structure.bats   # File/dir existence checks
└── integration/
    └── composer-scripts.bats    # Full script execution
```

### Example

```bash
#!/usr/bin/env bats

setup() {
  load '../helpers/test_helper'
}

@test "setup-package.sh exists and is executable" {
  [ -x "${PROJECT_ROOT}/scripts/setup-package.sh" ]
}

@test "setup-package.sh --help exits 0" {
  run bash "${PROJECT_ROOT}/scripts/setup-package.sh" --help
  [ "$status" -eq 0 ]
}
```

### When to use BATS

- Any script that can be run in isolation with a predictable outcome
- File generation / publishing logic
- CI/CD script behavior

---

## Make Commands Reference

```bash
# PHP
make test           # PHPUnit — unit + feature tests
make test-all       # PHPUnit + BATS

# JS
make test           # Vitest — unit + integration
make bats           # BATS — shell script tests
make test-all       # Vitest + BATS

# Coverage (CI)
make test:coverage  # Generate coverage report (clover XML + HTML)
```

---

## What to Test

| Yes/No | Test |
| :---: | :--- |
| ✅ | Business logic |
| ✅ | Edge cases (null, empty, boundary values) |
| ✅ | All branches (`if`/`else`, `match` arms) |
| ✅ | Error and exception paths |
| ❌ | Framework boilerplate (getters/setters with no logic) |
| ❌ | Third-party library internals |
| ❌ | Configuration-only classes |

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
