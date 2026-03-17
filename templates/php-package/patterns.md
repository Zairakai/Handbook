# Patterns — php-package

> **[Handbook][handbook]** › **[Templates][templates]** › **[php-package][pkg]** › Patterns

---

## ServiceProvider

`src/{{PACKAGE_NAME}}ServiceProvider.php` — package entry point, auto-discovered via `extra.laravel.providers` in `composer.json`.

```php
class {{PACKAGE_NAME}}ServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->mergeConfigFrom(__DIR__ . '/../config/{{PACKAGE_SLUG}}.php', '{{PACKAGE_SLUG}}');
    }

    public function boot(): void
    {
        if ($this->app->runningInConsole()) {
            $this->publishes([
                __DIR__ . '/../config/{{PACKAGE_SLUG}}.php' => config_path('{{PACKAGE_SLUG}}.php'),
            ], 'zairakai-config');
        }
    }
}
```

### register()

`mergeConfigFrom()` makes the package config available under `config('{{PACKAGE_SLUG}}.*')` without requiring the consuming project to publish it. The project's published config takes precedence over the package default.

### boot()

`publishes()` makes the config available to `php artisan vendor:publish --tag=zairakai-config`. Only active when running in console — never during web requests.

### Extending the ServiceProvider

Common additions:

```php
public function boot(): void
{
    if ($this->app->runningInConsole()) {
        $this->publishes([...], 'zairakai-config');

        // Migrations
        $this->loadMigrationsFrom(__DIR__ . '/../database/migrations');

        // Routes
        $this->loadRoutesFrom(__DIR__ . '/../routes/api.php');
    }

    // Commands
    $this->commands([MyCommand::class]);

    // Translations
    $this->loadTranslationsFrom(__DIR__ . '/../lang', '{{PACKAGE_SLUG}}');
}
```

---

## TestCase — Orchestra Testbench

`tests/TestCase.php` bootstraps a minimal Laravel application for package tests without requiring a full app install.

```php
abstract class TestCase extends Orchestra
{
    /**
     * @return array<int, class-string>
     */
    protected function getPackageProviders($app): array
    {
        return [
            {{PACKAGE_NAME}}ServiceProvider::class,
        ];
    }
}
```

All test classes in `tests/Unit/` and `tests/Feature/` extend this `TestCase`.

### Customizing the test environment

Override these Testbench methods as needed:

```php
// Define environment variables for tests
protected function defineEnvironment($app): void
{
    $app['config']->set('{{PACKAGE_SLUG}}.some_key', 'test_value');
}

// Run migrations before tests
protected function defineDatabaseMigrations(): void
{
    $this->loadMigrationsFrom(__DIR__ . '/../database/migrations');
}

// Register additional service providers
protected function getPackageProviders($app): array
{
    return [
        {{PACKAGE_NAME}}ServiceProvider::class,
        SomeDependencyServiceProvider::class,
    ];
}
```

### Unit vs Feature tests

| Suite | Directory | Description |
| :--- | :--- | :--- |
| `Unit` | `tests/Unit/` | Pure class logic — no Laravel app bootstrap, fast |
| `Feature` | `tests/Feature/` | Full Testbench bootstrap — routes, DB, service providers |

For Unit tests that do not need Laravel at all:

```php
// tests/Unit/MyClassTest.php — no TestCase needed
use PHPUnit\Framework\TestCase as BaseTestCase;

class MyClassTest extends BaseTestCase { ... }
```

---

## Config file

The package config (`config/{{PACKAGE_SLUG}}.php`) must exist before `composer install`:

```bash
touch config/{{PACKAGE_SLUG}}.php
```

Minimal structure:

```php
<?php

declare(strict_types=1);

return [
    // package defaults
];
```

`mergeConfigFrom()` in `register()` merges this with any project-level override published via `vendor:publish`.

---

**[Back to php-package][pkg]**

[handbook]: ../../README.md
[templates]: ../README.md
[pkg]: ./README.md
