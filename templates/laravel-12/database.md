# Database

> **[Handbook][handbook]** › **[Templates][templates]** › **[Laravel 12][laravel-12]** › Database

---

## Migrations

### Bundled migrations

```text
0001_01_01_000000_create_users_table.php    ← User, Session, PasswordResetToken
0001_01_01_000001_create_cache_table.php    ← cache, cache_locks (Laravel default)
0001_01_01_000002_create_jobs_table.php     ← Job, JobBatch, FailedJob
```

### Conventions

- `down()` is defined **before** `up()` in every migration file — makes rollbacks easier to spot.
- Use `Model::getTableName()` and `Model::resolveColumn()` everywhere — no hardcoded strings.
- Never modify an existing migration in production — always create a new one.

```php
// ✅ Correct
Schema::create(User::getTableName(), function (Blueprint $table): void {
    $table->id(User::resolveColumn('id'));
    $table->string(User::resolveColumn('email'))->unique();
    $table->foreignId(Session::resolveColumn('userId'))
          ->constrained(User::getTableName())
          ->cascadeOnDelete();
});

// ❌ Wrong — hardcoded strings break with prefixed tables
Schema::create('users', function (Blueprint $table): void {
    $table->foreignId('user_id')->constrained('users');
});
```

---

## Seeders

### Auto-discovery architecture

`DatabaseSeeder` discovers and runs seeders by environment directory. No manual `$this->call()` list to maintain.

```text
database/seeders/
├── DatabaseSeeder.php      ← entry point (never moved or renamed)
├── data/
│   ├── Common/
│   │   └── users.csv       ← admin account — runs in all envs except production
│   ├── Local/
│   │   └── users.csv       ← alice, bob, claire
│   ├── Staging/
│   │   └── .gitkeep
│   └── Testing/
│       └── .gitkeep
└── Calls/
    ├── Common/
    │   └── UserSeeder.php  ← admin via CSV + ADMIN_EMAIL override
    └── Local/
        └── UserSeeder.php  ← CSV Local + factory(10)
```

**Execution order:**

1. All seeders in `Calls/Common/` — always, in every environment.
2. All seeders in `Calls/{APP_ENV}/` — only for the matching environment.

> Never put environment-specific data in `Common/`. It is for shared mandatory records only (e.g. the admin account that must exist in every environment except production).

### Environment variables

| Variable | Default | Description |
| :--- | :--- | :--- |
| `ADMIN_EMAIL` | `admin@example.com` | Email for the admin account (`Common/UserSeeder`) |
| `ADMIN_PASSWORD` | value of `DEFAULT_PASSWORD` | Password override for admin |
| `DEFAULT_PASSWORD` | `password` | Applied to all seeded accounts without an explicit password |

### Adding a new environment seeder

1. Create `database/seeders/Calls/Staging/ProductSeeder.php` (or any env).
2. Drop a CSV in `database/seeders/data/Staging/products.csv` if needed.
3. `DatabaseSeeder` auto-discovers and runs it — no registration required.

---

## csvToSql()

Helper in `app/Support/Helpers-dev.php` (loaded via `autoload-dev` — **not available in production**).

```php
csvToSql(string $tableOrModel, string $csvPath, array $columnMap = []): string
```

| Behaviour | Detail |
| :--- | :--- |
| Accepts model class or table name | `User::class` or `'auth_users'` |
| Backtick-escapes all identifiers | Table name and every column name |
| `NULL` string in CSV | → SQL `NULL` |
| JSON values (`{` or `[`) | → single-quoted with `addslashes` |
| Unreadable or empty file | → throws `RuntimeException` |

### Pattern in a seeder

```php
use function database_path;

// 1. Insert all rows from CSV
DB::statement(csvToSql(User::class, database_path('seeders/data/Local/users.csv')));

// 2. Hash the default password for rows that have none
$defaultPassword = Hash::make(env('DEFAULT_PASSWORD', 'password'));

DB::table(User::make()->getTable())
    ->whereNull(User::resolveColumn('password'))
    ->update([User::resolveColumn('password') => $defaultPassword]);

// 3. Supplement with factory-generated records
User::factory(10)->create([User::resolveColumn('password') => $defaultPassword]);
```

---

## Factories

Factories extend Laravel's default `HasFactory` via `BaseModel`. No specific constraints beyond the standard:

- Use `fake()` for all generated data.
- Reference columns via `static::resolveColumn()` to stay consistent with the rest of the codebase.

```php
public function definition(): array
{
    return [
        User::resolveColumn('name')  => fake()->name(),
        User::resolveColumn('email') => fake()->unique()->safeEmail(),
    ];
}
```

---

## Stubs

Artisan `make:*` commands use customized stubs that enforce the project conventions:

| Stub | Command | Pre-configured |
| :--- | :--- | :--- |
| `model.stub` | `make:model` | Extends `BaseModel`, `COLUMNS` constant pre-filled |
| `model.pivot.stub` | `make:model --pivot` | Extends `BasePivot`, `COLUMNS` |
| `migration.create.stub` | `make:migration --create` | `declare(strict_types=1)`, `down()` before `up()` |
| `migration.stub` | `make:migration` | Generic with `strict_types` |
| `request.stub` | `make:request` | Extends `BaseRequest` |

---

**[Back to Laravel 12][laravel-12]**

[handbook]: ../../README.md
[templates]: ../README.md
[laravel-12]: ./README.md
