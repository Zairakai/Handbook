# Frontend

> **[Handbook][handbook]** › **[Templates][templates]** › **[Laravel 11][laravel-11]** › Frontend

Stack: **Vue 3 + TypeScript + Pinia + Vue Router + `@zairakai/vue-components`**

---

## File structure

```text
resources/js/
├── app.ts              ← entrypoint: createApp, Pinia, Router, VueComponents
├── App.vue             ← root component: RouterView + onMounted i18n load
├── bootstrap.ts        ← window.axios via createLaravelClient()
├── config/
│   └── i18n.ts         ← locale config (only place to declare available locales)
├── router/
│   └── index.ts        ← createRouter — routes array empty by default, fill per project
└── stores/
    └── i18n.ts         ← Pinia store: loadLocale(), applyLocale(), t()
```

---

## Entrypoint — `app.ts`

Bootstraps the Vue application:

1. Creates the Pinia instance.
2. Creates the Vue Router.
3. Installs `@zairakai/vue-components` plugin.
4. Mounts on `#app` (set in `welcome.blade.php`).

---

## Root component — `App.vue`

```text
<template>
  <RouterView />
</template>

<script setup lang="ts">
  onMounted(() => {
    const lang = document.documentElement.lang   // set by Laravel via HTML lang attribute
                 || navigator.language
                 || defaultLocale
    i18n.loadLocale(lang)
  })
</script>
```

The locale is auto-detected from the `lang` attribute on `<html>` — Laravel sets this from `app.locale`. Fallback: `navigator.language`, then `defaultLocale` from `config/i18n.ts`.

---

## Vite configuration

The config is split into two files to keep concerns separate:

### `vite.config.ts` — thin wrapper

```typescript
import { defineConfig } from 'vite'
import { createViteConfig } from './vite.modules'

export default defineConfig(({ command, mode }) => createViteConfig({ command, mode }))
```

### `vite.modules.ts` — full factory

Exports individual config factories and one top-level `createViteConfig()`:

| Export | Purpose |
| :--- | :--- |
| `paths` | `js`, `scss` resource paths |
| `entries` | `app.ts`, `app.scss` entrypoints |
| `aliases` | `@` → `resources/js/`, `@scss` → `resources/scss/` |
| `getEnvironmentVars()` | Loads `.env`, detects `APP_ENV=local`, reads `VITE_SOURCEMAP` |
| `createPlugins()` | `laravel-vite-plugin` + `vue()` |
| `createBuildConfig()` | Minify, sourcemaps, Rollup chunk splitting |
| `createServerConfig()` | HMR, host `0.0.0.0`, port from `VITE_PORT` |
| `createCssConfig()` | SCSS modern-compiler, quiet deps |
| `createViteConfig()` | Assembles all modules into a full `UserConfig` |

Customizing individual aspects (e.g. adding a chunk, changing a port) only requires modifying the relevant factory — not the entire config.

---

## TypeScript configuration

`tsconfig.json` extends the shared strict base from `@zairakai/js-dev-tools`:

```json
{
  "extends": "@zairakai/js-dev-tools/tsconfig",
  "compilerOptions": {
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "outDir": "dist",
    "rootDir": "resources/js"
  },
  "include": ["resources/js/**/*"],
  "exclude": ["node_modules", "dist", "**/*.test.ts", "**/*.spec.ts"]
}
```

The base config (`tsconfig.base.json`) enforces:

- `strict: true`
- `noUnusedLocals` + `noUnusedParameters`
- `exactOptionalPropertyTypes`
- `moduleResolution: bundler`
- Target: `ES2022`

---

## Aliases

| Alias | Resolves to |
| :--- | :--- |
| `@` | `resources/js/` |
| `@scss` | `resources/scss/` |

Defined in `vite.modules.ts` → `aliases`. Both are available in TypeScript (via `tsconfig.json` paths) and in Vite imports.

---

## Build output

Chunks are split via Rollup `manualChunks`:

```text
assets/js/app-{hash}.js          ← application code
assets/js/vue-vendor-{hash}.js   ← pinia + vue + vue-router
assets/css/app-{hash}.css
```

In `local` env, minification is disabled and sourcemaps are opt-in via `VITE_SOURCEMAP=true`.

---

**[Back to Laravel 11][laravel-11]**

[handbook]: ../../README.md
[templates]: ../README.md
[laravel-11]: ./README.md
