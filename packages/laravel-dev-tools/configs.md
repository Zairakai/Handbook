# Configs — laravel-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › **[laravel-dev-tools][pkg]** › Configs

---

## PHPStan

Three `.neon` configs, each with a distinct role:

### `base.neon` — shared rules

Applied by both `app.neon` and `library.neon`.

```neon
parameters:
  level: max
  parallel:
    maximumNumberOfProcesses: 4

  checkMissingCallableSignature: true
  checkTooWideReturnTypesInProtectedAndPublicMethods: true
  checkUninitializedProperties: true
  checkDynamicProperties: false

  reportUnmatchedIgnoredErrors: false
  treatPhpDocTypesAsCertain: false

  ignoreErrors:
    - identifier: missingType.iterableValue
    # Laravel magic patterns (undefined property/method access)
    - '#Access to an undefined property [a-zA-Z0-9_\\]+::\$[a-zA-Z0-9_]+\.#'
    - '#Call to an undefined method [a-zA-Z0-9_\\]+::[a-zA-Z0-9_]+\(\)\.#'
```

### `app.neon` — Laravel applications

```neon
includes:
    - vendor/larastan/larastan/extension.neon
    - base.neon
parameters:
    paths:
        - ../../../../app
        - ../../../../tests
    tmpDir: ../../../../build/phpstan
```

Use this for full Laravel apps. Activates Larastan for Eloquent type inference.

### `library.neon` — Composer packages

```neon
includes:
    - base.neon
parameters:
    paths:
        - ../../../../src
    tmpDir: ../../../../build/phpstan
```

Use this for packages (no Larastan, `src/` only).

### `package.neon` — laravel-dev-tools own analysis

Internal config for analysing this package itself. Not used by consumers.

### Config cascade

`setup-package.sh --publish=phpstan` deploys `app.neon` (or `library.neon`) to `config/dev-tools/phpstan.neon`. The `resolve_config()` function in `scripts/phpstan.sh` picks it up automatically.

### PHPStan baseline

`stubs/baseline.neon.stub` is published to `config/dev-tools/baseline.neon`. Auto-included when it exists. Generate with:

```bash
make phpstan-baseline
```

---

## Pint

**Preset:** `laravel` (PSR-12 base)

### Notable rules (custom additions)

| Rule | Value | Rationale |
| :--- | :--- | :--- |
| `new_with_parentheses` | `false` | No `()` after `new Class` |
| `single_line_empty_body` | `true` | `public function boot(): void {}` |
| `strict_comparison` | `true` | No loose `==` |
| `yoda_style` | `always_move_variable: true` | Enforces yoda comparisons |
| `binary_operator_spaces` | `align_single_space_minimal` / `=>` + `=` → `align_single_space` | Aligned assignments |
| `ordered_class_elements` | traits → constants → properties → construct → methods | Strict member ordering |
| `ordered_imports` | alpha, `const` → `class` → `function` | Consistent imports |
| `global_namespace_import` | classes + constants + functions | Full import, no `\` prefix |
| `blank_line_before_statement` | all control flow statements | Breathing room |
| `control_structure_continuation_position` | `next_line` | `}\nelse {` |
| `phpdoc_align` | `vertical` | Aligned `@param`, `@return`, `@throws` |

### Ignored paths

`.docker`, `.github`, `bootstrap/cache`, `node_modules`, `public/vendor`, `storage`, `vendor`.

---

## Rector

### Entry point — `config/rector.php`

Auto-detects project root (vendor dep vs. own development), locates `rector.base.php`, and calls `RectorBaseConfig::configure()`.

Supports extension:

```php
return RectorBaseConfig::configure(
    projectRoot: $projectRoot,
    extraPaths: ['extra/path'],
    extraSkips: [SomeRector::class],
);
```

### Base config — `RectorBaseConfig`

#### Sets applied

| Set | Description |
| :--- | :--- |
| `UP_TO_PHP_83` | Modernize PHP syntax to 8.3 |
| `CODE_QUALITY` | Remove dead code, simplify expressions |
| `DEAD_CODE` | Remove unreachable code |
| `EARLY_RETURN` | Flatten nested conditions |
| `TYPE_DECLARATION` | Add missing type hints |
| `LARAVEL_110` | Laravel 11 migration patterns |
| `LARAVEL_CODE_QUALITY` | Laravel-specific improvements |

Also applies prepared sets: `deadCode`, `codeQuality`, `codingStyle`, `typeDeclarations`, `privatization`, `naming`, `instanceOf`, `earlyReturn`.

#### Auto-discovered paths

`app/`, `src/`, `tests/` — whichever exist in project root.

#### Skipped by default

- `tests/Fixtures` — fixture files should not be modernized
- `AddOverrideAttributeToOverriddenMethodsRector` — too noisy
- `EncapsedStringsToSprintfRector` — breaks readable string interpolation

---

## PHPInsights

**Preset:** `laravel` — minimum requirements: quality 80%, complexity 40%, architecture 75%, style 0% (Pint handles style).

### Removed rules (conflicts with Pint or Laravel conventions)

| Category | Removed | Reason |
| :--- | :--- | :--- |
| Naming | `SuperfluousAbstractClassNamingSniff`, `SuperfluousExceptionNamingSniff`, `SuperfluousInterfaceNamingSniff` | PSR-12 does not mandate these suffixes |
| Namespaces | `UseSpacingSniff` | Conflicts with Pint `blank_line_between_import_groups` |
| Type hints | `DisallowMixedTypeHintSniff`, `PropertyTypeHintSniff` | Too strict for Eloquent |
| Code analysis | `EmptyStatementSniff` | Sometimes intentional |
| Yoda | `DisallowYodaComparisonSniff` | Pint enforces yoda — they conflict |
| Operators | `BinaryOperatorSpacesFixer` | Pint aligns operators — Insights must not interfere |
| Instantiation | `NewWithParenthesesFixer`, `NewWithBracesFixer`, `ClassInstantiationSniff` | Pint enforces `new Class` (no `()`) |
| Imports | `OrderedImportsFixer` | Pint manages import order |
| Quotes | `SingleQuoteFixer` | Pint manages quote style |
| Chaining | `MethodChainingIndentationFixer` | Pint manages indentation |
| Constructs | `ForbiddenNormalClasses`, `ForbiddenTraits` | Valid in Laravel |
| Braces | `ScopeClosingBraceSniff`, `BracesFixer` | Pint controls brace style |
| Strings | `UnnecessaryStringConcatSniff` | Conflicts with Pint long-line splits |
| Parameters | `UnusedParameterSniff` | False positives with variadic forwarding |
| Constants | `UselessConstantTypeHintSniff` | `@var` on typed constants is informational |

### Configured thresholds

| Setting | Value |
| :--- | :--- |
| `CyclomaticComplexityIsHigh.maxComplexity` | 15 |
| `FunctionLengthSniff.maxLinesLength` | 50 |
| `LineLengthSniff.lineLimit` | 120 |
| `LineLengthSniff.absoluteLineLimit` | 160 |

### Important: config file location

`config/insights.php` must **not** exist. PHPInsights is pointed to `config/dev-tools/insights.php` via `INSIGHTS_CONFIG` in `scripts/insights.sh`. Having both files causes the wrong config to be loaded.

---

## PHPUnit

Two PHPUnit configs:

### `config/phpunit-app.xml` — published to projects

```xml
<testsuites>
  <testsuite name="Unit"><directory>tests/Unit</directory></testsuite>
  <testsuite name="Feature"><directory>tests/Feature</directory></testsuite>
</testsuites>
<source><include><directory>src</directory></include></source>
<php>
  <env name="APP_ENV" value="testing"/>
  <env name="BCRYPT_ROUNDS" value="4"/>
  <env name="CACHE_STORE" value="array"/>
  <env name="QUEUE_CONNECTION" value="sync"/>
  <env name="SESSION_DRIVER" value="array"/>
  <env name="MAIL_MAILER" value="array"/>
</php>
```

### `config/phpunit.xml` — laravel-dev-tools own tests

Internal config. Uses `tests/Unit` + `tests/Integration` with `src/` as source.

---

## Markdownlint

`config/.markdownlint.json` (bundled default):

```json
{
  "default": true,
  "MD013": false,
  "MD024": { "siblings_only": true },
  "MD033": { "allowed_elements": ["details", "summary", "kbd", "br"] },
  "MD040": false,
  "MD041": false
}
```

Published to `config/dev-tools/.markdownlint.json` via `--publish=markdownlint`. A root `.markdownlint.json` that extends it is also created automatically (IDE support).

---

## EditorConfig

Bundled `.editorconfig` sets the project baseline:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
indent_style = space
indent_size = 4
insert_final_newline = true
trim_trailing_whitespace = true

[*.{js,ts,vue,json,yml,yaml,css,scss}]
indent_size = 2

[Makefile]
indent_style = tab
```

---

**[Back to laravel-dev-tools][pkg]**

[handbook]: ../../README.md
[packages]: ../README.md
[pkg]: ./README.md
