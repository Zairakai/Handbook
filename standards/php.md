# PHP Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › PHP

Standards PHP purs applicables à **tout projet PHP** dans l'écosystème Zairakai — packages Composer, librairies, applications.

---

## Tools

| Tool | Purpose | Config file |
| :--- | :--- | :--- |
| **[Pint]** | Code formatter (PSR-12 + Laravel preset) | `config/dev-tools/pint.json` |
| **[PHPStan]** | Static analysis — level max | `phpstan.neon` + `config/dev-tools/baseline.neon` |
| **[Rector]** | Automated code modernization | `config/dev-tools/rector.php` |
| **[PHPInsights]** | Architecture & quality metrics | `config/dev-tools/insights.php` |
| **[PHPUnit]** | Unit and feature testing | `config/dev-tools/phpunit.xml` |

---

## Pint — Code Style

Pint enforces the **Laravel coding standard** (PSR-12 extended) on all PHP files.

```bash
make cs        # Check — lists violations without fixing
make cs:fix    # Fix — applies all corrections automatically
```

### Key Rules

- **4 spaces** for indentation (PSR-12)
- **Single quotes** for strings where possible
- **Trailing commas** in multi-line arrays and function arguments
- `declare(strict_types=1)` at the top of every file
- **Return type declarations**: always explicit
- **No unused imports**: enforced by Pint

---

## PHPStan — Static Analysis

PHPStan runs at **level max** across all projects. No exceptions.

```bash
make analyse
```

### Configuration

```neon
# phpstan.neon
includes:
    - vendor/zairakai/laravel-dev-tools/config/library.neon
    - config/dev-tools/baseline.neon
```

### Baseline Policy

`config/dev-tools/baseline.neon` acknowledges pre-existing errors during migrations only. **New errors are never added to the baseline** — they must be fixed.

---

## Rector — Modernization

Rector automates refactoring to the target PHP version.

```bash
make rector        # Dry-run — shows changes without applying
make rector:fix    # Apply all modernizations
```

Run Rector before major PHP version upgrades and after adding new rule sets.

---

## PHPInsights — Architecture Metrics

PHPInsights measures four dimensions: **Code**, **Complexity**, **Architecture**, **Style**.

The goal is to **maintain scores, not let them degrade**. Scores must not drop below the thresholds defined in `config/dev-tools/insights.php`. Aiming for 100% is encouraged, but some rules can legitimately be excluded when they conflict with project constraints — document exclusions in the config file.

```bash
make insights
```

---

## Naming Conventions

| Element | Convention | Example |
| :--- | :--- | :--- |
| Classes / Interfaces / Traits | `PascalCase` | `UserService`, `HasActivities` |
| Methods | `camelCase` | `getUserById()` |
| Properties | `camelCase` | `$firstName` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Files | Match class name | `UserService.php` |
| Namespaces | `PascalCase` segments | `Zairakai\LaravelActivity` |

---

## Strict Typing

Every file starts with:

```php
<?php

declare(strict_types=1);

namespace Zairakai\YourPackage;
```

---

## Type Declarations

- **Always** declare parameter and return types
- Use **union types** (`int|string`) over docblocks when possible
- Use **`never`** for methods that always throw or exit

```php
public function find(int $id): User
{
    // ...
}

public function findMany(array $ids): Collection
{
    // ...
}
```

---

## Docblocks

Only add docblocks when PHPStan or IDE cannot infer types from native declarations:

```php
/**
 * @param array<int, string> $items
 *
 * @return Collection<int, User>
 */
public function process(array $items): Collection
{
    // ...
}
```

Do **not** duplicate information already expressed by type hints:

```php
// ❌ Useless — PHPStan already knows this
/** @param int $id */
public function find(int $id): User { }

// ✅ Useful — generic type not expressible natively
/** @return array<string, mixed> */
public function toArray(): array { }
```

---

## Single Responsibility

- One class per file
- Classes have one reason to change
- Avoid static methods except for factory patterns and pure functions

---

## Make Commands Reference

```bash
make quality      # Full gate: cs + analyse + rector + insights + markdownlint
make cs           # Check code style (Pint)
make cs:fix       # Fix code style (Pint)
make analyse      # Static analysis (PHPStan level max)
make rector       # Check modernizations (Rector)
make rector:fix   # Apply modernizations (Rector)
make insights     # Architecture metrics (PHPInsights)
```

---
**[Back to Coding Standards][standards]**

[Pint]: https://github.com/laravel/pint
[PHPStan]: https://phpstan.org/
[Rector]: https://getrector.com/
[handbook]: ../README.md
[standards]: ./README.md
[PHPInsights]: https://phpinsights.com/
[PHPUnit]: https://phpunit.de/
