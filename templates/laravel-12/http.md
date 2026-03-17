# HTTP Layer

> **[Handbook][handbook]** › **[Templates][templates]** › **[Laravel 12][laravel-12]** › HTTP Layer

---

## BaseController

All API controllers extend `BaseController`. It provides standardized JSON response helpers:

```php
// Single resource or empty response
successResponse(JsonResource|array|null $data, string $message, int $status = 200): JsonResponse

// Error response
errorResponse(string $message, int $status): JsonResponse

// Paginated list
paginatedResponse(ResourceCollection|LengthAwarePaginator<int,Model> $data, string $message): JsonResponse

// Catch and format any exception
handleException(Exception $e, string $message): JsonResponse
```

> Passing a raw `array` to `successResponse()` triggers a log warning at line 42:  
> `"Prefer a JsonResource over a raw array in ClassName::method() (L42)"`.  
> Always wrap array data in a `JsonResource`.

---

## BaseRequest

All Form Request classes extend `BaseRequest`. It overrides two Laravel methods with dual behavior — JSON for AJAX, native Laravel response for Blade:

| Request type | Detection | `failedValidation()` | `failedAuthorization()` |
| :--- | :--- | :--- | :--- |
| AJAX / API | `Accept: application/json` → `expectsJson()` | `422` JSON | `403` JSON |
| Blade web form | everything else | redirect back + `$errors` in session | `403` HTTP exception (redirect) |

**AJAX — `422` validation response:**

```json
{
  "status": "error",
  "message": "Validation failed.",
  "errors": { "field": ["The field is required."] }
}
```

**AJAX — `403` authorization response:**

```json
{
  "status": "error",
  "message": "This action is unauthorized."
}
```

**Blade — validation failure:** Laravel's native `redirect()->back()->withErrors()->withInput()`. Errors are available in templates via `@error('field')`.

**Blade — authorization failure:** Laravel's native `AuthorizationException` (caught by the exception handler → `403` HTML page).

> Do not mix the two paths. A request either sends `Accept: application/json` (AJAX) or it does not (web form). Never try to return JSON alongside a redirect.

---

## Routes

```text
routes/
├── api.php         → prefix /api → delegates to routes/api/v1.php
├── api/
│   └── v1.php      → GET /api/v1/health
│                   → GET /api/v1/cache/trans/{lang}  (public)
└── web.php         → GET / → welcome.blade.php (SPA shell)
```

### Versioning

`routes/api.php` delegates to versioned sub-files. To add v2:

```php
// routes/api.php
Route::prefix('v1')->group(base_path('routes/api/v1.php'));
Route::prefix('v2')->group(base_path('routes/api/v2.php'));
```

---

## TransController

`GET /api/v1/cache/trans/{lang}` — **public by default in the template**.

**What it does:**

1. Globs all `lang/{locale}/*.php` files.
2. Replaces `:app_name` placeholder with `config('app.name')`.
3. Caches the result for 300 s in `production` and `staging`, 0 in `local`.
4. Returns a flat JSON object keyed by namespace:

```json
{
  "common": { "hello": "Bonjour", "save": "Enregistrer" },
  "auth":   { "login": "Se connecter" }
}
```

**Why public in the template?**

`lang/` contains UI strings only — labels, button text, error messages. No sensitive data. A public route allows the frontend to load translations before any authentication, which is required for public-facing pages.

**It is up to the project to decide the auth strategy.**

The template does not enforce anything beyond the default. Depending on the project's needs, options include:

- **Keep public** — `lang/` stays UI-only, no sensitive strings. Nothing to do.
- **Add Sanctum** — wrap the route in `auth:sanctum` if all pages are behind login.
- **Split by subdirectory** — keep `GET /cache/trans/{lang}` public for shared strings, add a separate authenticated route (e.g. `GET /user/trans/{lang}`) for role-specific strings loaded from a different `lang/` path.
- **Any other organization** — subdomain, versioned namespaces, custom middleware — the controller is a starting point, not a constraint.

> The only firm rule: `lang/` files must never contain sensitive or role-specific data as long as the route is public. If they do, protect the route.

---

## Exception Handler

Defined in `bootstrap/app.php`. API detection: `$request->expectsJson() || $request->is('api/*')`.

| Exception | HTTP Code |
| :--- | :--- |
| `AuthenticationException` | 401 |
| `AuthorizationException` | 403 |
| `ModelNotFoundException` | 404 |
| `NotFoundHttpException` | 404 |
| `ValidationException` | 422 |
| `HttpException` | exception's own code |

Web requests fall back to Laravel's default HTML error pages (`null` handler).

---

## Throttle

Configured via `config/api.php`, sourced from `.env`:

```env
API_THROTTLE_MAX_ATTEMPTS=60
API_THROTTLE_DECAY_MINUTES=1
```

Rate limiter named `api`, configured in `AppServiceProvider::configureRateLimiting()`:

```php
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinutes(
        config('api.throttle.decay_minutes'),
        config('api.throttle.max_attempts'),
    )->by($request->user()?->getKey() ?? $request->ip());
});
```

Applied automatically to the `api` middleware group in Laravel 12.

> In `.env.testing`, throttle is set to `API_THROTTLE_MAX_ATTEMPTS=1000` to avoid test failures.

---

**[Back to Laravel 12][laravel-12]**

[handbook]: ../../README.md
[templates]: ../README.md
[laravel-12]: ./README.md
