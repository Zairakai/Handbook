# PHP Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › PHP

Standards PHP purs applicables à **tout projet PHP** dans l'écosystème Zairakai — packages Composer, librairies, applications.

---

## Tools

| Tool | Purpose | Config file |
| :--- | :--- | :--- |
| **[Pint]** | Code formatter (PSR-12 + Laravel preset) | `config/dev-tools/pint.json`: the complete file, edited by the project. `pint.json` at the root is a copy. |
| **[PHPStan]** | Static analysis - level max | `config/dev-tools/phpstan.neon` + `config/dev-tools/baseline.neon`. `phpstan.neon` at the root is thin. |
| **[Rector]** | Automated code modernization | `config/dev-tools/rector.php` |
| **[PHPInsights]** | Architecture & quality metrics | `config/dev-tools/phpinsights.php` |
| **[PHPUnit]** | Unit and feature testing | `config/dev-tools/phpunit.xml`. `phpunit.xml` at the root is a copy. |

The configuration of each tool is in `config/dev-tools/`. The root holds only what an editor reads: thin files and copies (see [Editor integration](./editor-integration.md#the-rules-of-the-root-files)). `make config-check` verifies that they agree.

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

## Conditions

PHPInsights alerts on a long condition (the sniff counts characters, not parts: about 60 characters is where a third part usually is), and nothing rewrites it. Write it by hand:

- **Three parts or more**: one part per line.
- **The operator at the start of the line**, as in [PER-CS 3.0][per-cs]. Pint (`operator_linebreak`) keeps it there.
- **A group in parentheses** goes on its own lines: the opening parenthesis alone, the parts indented, the closing parenthesis alone.

```php
if (
    'gitlab-ci' === $target
    && (
        $fullstack
        || file_exists($basePath . '/package.json')
    )
) {
    // ...
}
```

Two short parts stay on one line: `if ($installed && $this->isReady()) {`.

### Ternaries

A ternary is always written on several lines, the operators at the start of the line. PHPInsights alerts on a single-line ternary, and `make spread-fix` writes it:

```php
$action = $dryRun
    ? 'Would fix'
    : 'Fixed';
```

A condition of three parts or more inside it follows the rules above.

### Arrays, calls and parameters

- **An array that is not empty** is always written on several lines: one element per line, a trailing comma, the `=>` aligned (Pint aligns them). Only `[]` stays on one line. An array that holds a spread array is spread too, never `['a' => [` on one line.
- **A call of more than two arguments**, and **a function, method or closure of more than two parameters**: one per line, a trailing comma.
- A destructuring (`[$a, $b] = $pair`) stays on one line.

```php
$context = stream_context_create([
    'http' => [
        'method'  => 'POST',
        'content' => $message,
        'timeout' => 15,
    ],
]);

public function publishGroup(
    string $group,
    string $vendorPath,
    string $basePath,
): bool {
```

Nothing alerts on these, because Pint and PHPInsights do not count elements. They are written by `make spread-fix` (part of `make quality-fix`, before Pint): the arrays, the calls and the parameters by the `CodeSpreader` of laravel-dev-tools, the ternaries by `phpcbf`.

### Strict constructs

PHPStan (`phpstan-strict-rules`) and PHPInsights both report these. Each one hides a loose comparison:

| Avoid | Why | Write instead |
| :--- | :--- | :--- |
| `empty($x)` | Checks existence and falsy value at once: `"0"`, `0`, `[]` are all "empty", and an undefined variable is silent. | `'' === $x`, `[] === $x`, `0 === count($x)` |
| `$a ?: $b` | Weak comparison: `"0"` and `0` fall to `$b`. | `$a ?? $b`, or a ternary with a strict condition: `'' === $a ? $b : $a` |
| `@function()` | Hides every error of the call, including the ones that matter. | Handle the failure: `set_error_handler()` around the call, restored in `finally`, and test the returned value |

---

## PHPStan — Static Analysis

PHPStan runs at **level max** across all projects. No exceptions.

```bash
make analyse
```

### Configuration

```neon
# config/dev-tools/phpstan.neon
includes:
  - ../../vendor/zairakai/laravel-dev-tools/config/library.neon
  - baseline.neon

parameters:
  paths:
    - ../../src
    - ../../tests
```

The `phpstan.neon` at the root is thin, for the editor: it includes `config/dev-tools/phpstan.neon`.

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

The goal is to **maintain scores, not let them degrade**. Scores must not drop below the thresholds defined in `config/dev-tools/phpinsights.php`. Aiming for 100% is encouraged, but some rules can legitimately be excluded when they conflict with project constraints — document exclusions in the config file.

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
[per-cs]: https://www.php-fig.org/per/coding-style/
