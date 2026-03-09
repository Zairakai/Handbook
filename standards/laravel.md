# Laravel Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › Laravel

Conventions spécifiques à **Laravel** — applicables aux packages Composer utilisant Laravel et aux applications Laravel. À lire conjointement avec [PHP Standards][php].

---

## Architecture

### Controllers

Controllers stay **thin** — they receive a request, delegate to a service or action, and return a response.

```php
// ✅ Thin controller
class UserController extends Controller
{
    public function store(StoreUserRequest $request, CreateUser $action): JsonResponse
    {
        $user = $action->execute($request->validated());

        return response()->json($user, 201);
    }
}

// ❌ Fat controller — business logic does not belong here
class UserController extends Controller
{
    public function store(Request $request): JsonResponse
    {
        $validated = $request->validate([...]);
        $user      = User::create($validated);

        Mail::to($user)->send(new WelcomeMail($user));
        // ...
    }
}
```

### Form Requests

All validation lives in **Form Request** classes — never in controllers:

```php
class StoreUserRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'name'  => [
                'required',
                'string',
                'max:255',
            ],
            'email' => [
                'required',
                'email',
                'unique:users',
            ],
        ];
    }
}
```

### Services & Actions

- **Service**: stateful, injectable via constructor, handles a domain (e.g. `UserService`)
- **Action**: single-use, stateless, one method `execute()` (e.g. `CreateUser`, `SendWelcomeEmail`)

```php
// Action pattern
final class CreateUser
{
    public function execute(array $data): User
    {
        return User::create($data);
    }
}
```

---

## Eloquent Models

- One model per table, one file per model
- Use **`$fillable`** — never `$guarded = []`
- Casts for non-string attributes:

```php
protected $casts = [
    'email_verified_at' => 'datetime',
    'preferences'       => 'array',
    'is_active'         => 'boolean',
];
```

- Relationships are methods, not properties — always return the relation object:

```php
public function posts(): HasMany
{
    return $this->hasMany(Post::class);
}
```

---

## Migrations

Naming convention: `YYYY_MM_DD_HHMMSS_verb_description_table.php`

```text
2024_01_15_120000_create_users_table.php
2024_03_02_090000_add_avatar_to_users_table.php
2024_05_10_143000_drop_legacy_tokens_table.php
```

- Always define `up()` **and** `down()` — migrations must be reversible
- Never modify an existing migration in production — create a new one
- Use `after()` to control column order

---

## Naming Conventions (Laravel Layer)

| Element | Convention | Example |
| :--- | :--- | :--- |
| Models | singular `PascalCase` | `User`, `BlogPost` |
| Controllers | singular + `Controller` | `UserController` |
| Form Requests | verb + noun + `Request` | `StoreUserRequest`, `UpdatePostRequest` |
| Services | noun + `Service` | `UserService`, `PaymentService` |
| Actions | verb + noun | `CreateUser`, `SendWelcomeEmail` |
| Jobs | verb + noun | `ProcessPayment`, `SyncInventory` |
| Events | noun + past tense | `UserCreated`, `OrderShipped` |
| Listeners | verb + noun | `SendWelcomeEmail`, `NotifyAdmin` |
| Policies | noun + `Policy` | `UserPolicy`, `PostPolicy` |
| Observers | noun + `Observer` | `UserObserver` |
| Tables | plural `snake_case` | `users`, `blog_posts` |
| Columns | `snake_case` | `first_name`, `created_at` |
| Foreign keys | `singular_table_id` | `user_id`, `blog_post_id` |

---

## Route Organization

```php
// routes/api.php — resource routes
Route::middleware('auth:sanctum')
    ->group(function () {
        Route::apiResource('users', UserController::class);
        Route::apiResource('posts', PostController::class);
    });

// routes/web.php — named routes with dot notation
Route::get('/dashboard', DashboardController::class)->name('dashboard');
```

- Always name routes — never hardcode URLs in views or controllers
- Use `apiResource` for JSON APIs, `resource` for web routes
- Group related routes under a common middleware or prefix

---

## Service Provider Registration

For packages, register services in a dedicated `ServiceProvider`:

```php
public function register(): void
{
    $this->app->singleton(UserService::class);
}

public function boot(): void
{
    $this->loadMigrationsFrom(__DIR__.'/../database/migrations');
    $this->publishes(
        [
            __DIR__.'/../config/package.php' => config_path('package.php'),
        ],
        'package-config'
    );
}
```

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[php]: ./php.md
