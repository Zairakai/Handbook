# Architecture

> **[Handbook][handbook]** › **[Templates][templates]** › **[Laravel 12][laravel-12]** › Architecture

---

## Overview

The template is structured as a **thin-controller JSON API + SPA frontend**:

- Laravel serves a single Blade view as the SPA shell — all rendering is client-side.
- All data flows through a versioned JSON API (`/api/v1/...`).
- Every PHP file in `app/`, `tests/`, `config/`, `database/`, `routes/`, `stubs/`, and `bootstrap/` carries `declare(strict_types=1)`.

---

## Directory structure

```text
app/
├── Http/
│   ├── Controllers/
│   │   ├── BaseController.php
│   │   └── API/V1/Cache/
│   │       └── TransController.php
│   └── Requests/
│       └── BaseRequest.php
├── Models/
│   ├── BaseModel.php
│   ├── BaseAuthenticatable.php
│   ├── BasePivot.php
│   ├── Auth/
│   │   ├── User.php
│   │   ├── Session.php
│   │   └── PasswordResetToken.php
│   └── Queue/
│       ├── Job.php
│       ├── JobBatch.php
│       └── FailedJob.php
├── Providers/
│   └── AppServiceProvider.php
└── Support/
    └── Helpers-dev.php         ← autoload-dev only
```

---

## Base classes

All domain models must extend one of the three base classes — never directly extend Eloquent or Laravel's `User`.

| Class | Extends | Traits |
| :--- | :--- | :--- |
| `BaseModel` | `Illuminate\Database\Eloquent\Model` | `BaseTable`, `TraceActivities` |
| `BaseAuthenticatable` | `Illuminate\Foundation\Auth\User` | `BaseTable`, `TraceActivities` |
| `BasePivot` | `Illuminate\Database\Eloquent\Relations\Pivot` | `BaseTable` |

- `BaseTable` — provides `COLUMNS` constant, `resolveColumn()`, `getTableName()`, and auto-derives table names from the namespace.
- `TraceActivities` — hooks into model events to log activity via `zairakai/laravel-activity`.

---

## Table naming — auto-derivation

Table names are **auto-derived** from the model namespace by the `BaseTable` trait. No `$table` property or `TABLE_NAME` constant on domain models.

Rule: prefix = last namespace segment before the class name, converted to `snake_case`.

```text
App\Models\Auth\User        → auth_users
App\Models\Auth\Session     → auth_sessions
App\Models\Queue\Job        → queue_jobs
App\Models\Billing\Invoice  → billing_invoices
```

Domain models do **not** declare `TABLE_NAME` — it is always auto-resolved.

---

## Column resolution

Always use `Model::resolveColumn('logicalKey')` when referencing foreign keys in relations and migrations. Never hardcode FK strings — they will break with prefixed tables.

```php
// ✅ Correct — resolves to the actual column name
$this->hasMany(Session::class, Session::resolveColumn('userId'));

// ❌ Wrong — hardcoded string, breaks with prefixed tables
$this->hasMany(Session::class, 'user_id');

// ❌ Wrong — foreignKey() generates the wrong name with prefixed tables
$this->hasMany(Session::class, foreignKey: 'user_id');
```

Use the same pattern in migrations:

```php
$table->foreignId(Session::resolveColumn('userId'))
      ->constrained(User::getTableName());
```

---

## Domain models

### `App\Models\Auth\`

| Model | Notes |
| :--- | :--- |
| `User` extends `BaseAuthenticatable` | Full `COLUMNS` constant, `getHidden()` override (not `$hidden`), `casts()`, relations `passwordResetToken()` + `sessions()` |
| `Session` extends `BaseModel` | `$timestamps = false`, relation `user()` |
| `PasswordResetToken` extends `BaseModel` | `PRIMARY_KEY = 'email'`, `$incrementing = false`, `$timestamps = false`, relation `user()` |

> `User` overrides `getHidden()` instead of using `$hidden` so that `static::resolveColumn()` can be called inside it. `$hidden` is evaluated before the model is fully booted.

### `App\Models\Queue\`

| Model | Notes |
| :--- | :--- |
| `Job` | `$timestamps = false` |
| `JobBatch` | `$timestamps = false` |
| `FailedJob` | `$timestamps = false` |

### `COLUMNS` constant

Every model carries a `public const array COLUMNS` listing all table columns as `'logicalKey' => 'column_name'`. Used by `resolveColumn()` and available for PHPStan type-checking.

```php
public const array COLUMNS = [
    'id'        => 'id',
    'userId'    => 'user_id',
    'payload'   => 'payload',
    'createdAt' => 'created_at',
];
```

> `COLUMNS` must be `public const array` — PHPInsights requires public visibility on constants.

---

**[Back to Laravel 12][laravel-12]**

[handbook]: ../../README.md
[templates]: ../README.md
[laravel-12]: ./README.md
