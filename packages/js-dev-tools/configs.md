# Configs — js-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › **[js-dev-tools][pkg]** › Configs

---

## ESLint

Flat config format (`eslint.config.js`). Exported as `@zairakai/js-dev-tools/eslint`.

### Rule layers

| Layer | Files | Description |
| :--- | :--- | :--- |
| Base JS | `*` | `@eslint/js` recommended |
| Vue | `*` | `eslint-plugin-vue` flat/strongly-recommended |
| Prettier compat | `*` | `eslint-config-prettier` (always last — disables conflicting rules) |
| Base rules | `*` | Custom rules (see below) |
| TypeScript | `**/*.ts`, `**/*.tsx`, `**/*.vue` | `@typescript-eslint` plugin + parser |
| Test files | `**/*.test.{js,ts}`, `**/*.spec.{js,ts}`, `**/tests/**/*` | Relaxed `any` + `no-console` |
| Config files | `**/*.config.{js,ts}`, `**/*rc.{js,ts}` | Relaxed `no-console` + `no-var-requires` |

### Key rules

| Rule | Value | Rationale |
| :--- | :--- | :--- |
| `yoda` | `always` | Enforces yoda comparisons — consistent with PHP side |
| `no-unused-vars` | `error` (ignore `^_` prefix) | Silence with `_varName` when intentional |
| `no-console` | `warn` — allow `warn`, `error`, `info`, `debug` | Prevent leftover `console.log` |
| `eqeqeq` | `always` (except `null`) | No loose equality |
| `prefer-const` | `error` | Immutable bindings by default |
| `no-var` | `error` | Always `let`/`const` |
| `prefer-template` | `error` | Template literals over concatenation |
| `sort-imports` | member sort only | Import members sorted, declaration sort off (handled by Prettier/organize-imports) |
| `vue/multi-word-component-names` | `off` | Single-word names are valid |
| `vue/component-name-in-template-casing` | `PascalCase` | Components in templates must be PascalCase |
| `@typescript-eslint/prefer-nullish-coalescing` | `error` | `??` over `\|\|` for null checks |
| `@typescript-eslint/prefer-optional-chain` | `error` | `?.` over manual null guards |

### Gitignore integration

The bundled config auto-includes `.gitignore` patterns via `@eslint/compat`'s `includeIgnoreFile`. If `.gitignore` does not exist, it is silently skipped.

### Extending in a project

```javascript
// config/dev-tools/eslint.config.js (published stub)
import baseConfig from '@zairakai/js-dev-tools/eslint'

export default [
  ...baseConfig,
  {
    rules: {
      // project-specific overrides
    },
  },
]
```

---

## Prettier

Exported as `@zairakai/js-dev-tools/prettier`. Plugins: `prettier-plugin-blade`, `@prettier/plugin-php`, `prettier-plugin-organize-imports`.

### Core options

| Option | Value |
| :--- | :--- |
| `printWidth` | 120 |
| `tabWidth` | 2 |
| `useTabs` | false |
| `semi` | false |
| `singleQuote` | true |
| `trailingComma` | `es5` |
| `bracketSpacing` | true |
| `bracketSameLine` | false |
| `arrowParens` | `always` |
| `endOfLine` | `lf` |
| `vueIndentScriptAndStyle` | true |

### File-specific overrides

| Files | Notable settings |
| :--- | :--- |
| `*.vue` | `singleAttributePerLine: true` |
| `*.scss`, `*.css` | `singleQuote: false`, `semi: false` |
| `*.blade.php` | `tabWidth: 4`, `phpVersion: '8.3'` |
| `*.json`, `*.jsonc` | `trailingComma: 'none'` |
| `*.yml`, `*.yaml` | `singleQuote: false`, `bracketSpacing: false` |

### Extending in a project

```javascript
// config/dev-tools/prettier.config.js (published stub)
import baseConfig from '@zairakai/js-dev-tools/prettier'

export default { ...baseConfig }
```

---

## Stylelint

Exported as `@zairakai/js-dev-tools/stylelint`. Extends `stylelint-config-standard-scss` + `stylelint-config-recommended-vue` + `stylelint-config-html`.

### Key rules

| Rule | Value |
| :--- | :--- |
| `declaration-block-no-redundant-longhand-properties` | true |
| `length-zero-no-unit` | true |
| `scss/at-rule-no-unknown` | true |
| `scss/at-mixin-argumentless-call-parentheses` | `never` |
| `scss/dollar-variable-pattern` | `^[_a-z0-9\\-]+$` |
| `scss/selector-no-redundant-nesting-selector` | true |

### Extending in a project

```javascript
// config/dev-tools/stylelint.config.js (published stub)
import baseConfig from '@zairakai/js-dev-tools/stylelint'

export default {
  ...baseConfig,
  rules: {
    ...baseConfig.rules,
    // project-specific overrides
  },
}
```

---

## Vitest

Exported as `@zairakai/js-dev-tools/vitest`. Base config — consumer projects extend it to add plugins (Vue, jsdom, etc.).

### Base options

```javascript
{
  test: {
    globals: true,
    environment: 'node',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'lcov', 'html', 'cobertura'],
      reportsDirectory: 'build/coverage',
      exclude: ['node_modules/**', 'dist/**', 'build/**', '**/*.config.*', '**/*.d.ts'],
    },
  },
}
```

### Published stub (Vue projects)

`stubs/testing/vitest.config.js.stub` extends the base and adds Vue plugin + jsdom:

```javascript
import baseConfig from '@zairakai/js-dev-tools/vitest'
import vue from '@vitejs/plugin-vue'
import { mergeConfig } from 'vitest/config'

export default mergeConfig(baseConfig, {
  plugins: [vue()],
  test: {
    environment: 'jsdom',
    include: ['tests/js/**/*.test.ts'],
  },
})
```

### Config resolution

`make test` → `scripts/test.sh` → `resolve_config "vitest.config.js"` → finds `config/dev-tools/vitest.config.js` (published) or falls back to bundled default.

`VITEST_CONFIG` env var overrides the path.

---

## Knip

Exported as `@zairakai/js-dev-tools/knip`. Detects unused exports, files, and dependencies.

### Extending in a project

```javascript
// config/dev-tools/knip.config.js (published stub)
import baseConfig from '@zairakai/js-dev-tools/knip'

export default {
  ...baseConfig,
  ignoreDependencies: [...(baseConfig.ignoreDependencies ?? [])],
  ignoreBinaries: [...(baseConfig.ignoreBinaries ?? [])],
}
```

> Always spread `ignoreDependencies` and `ignoreBinaries` — numeric-keyed arrays do not deep-merge.

---

## tsconfig.base.json

Base TypeScript config. Published to `tsconfig.json` via `--publish=typescript`.

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "lib": ["ES2022"],
    "strict": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "exactOptionalPropertyTypes": true,
    "ignoreDeprecations": "5.0"
  }
}
```

Projects extend it and add their `paths` + `include`:

```json
{
  "extends": "@zairakai/js-dev-tools/config/tsconfig.base.json",
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["resources/js/*"]
    }
  },
  "include": ["resources/js/**/*", "tests/js/**/*"]
}
```

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

Published to `config/dev-tools/.markdownlint.json` via `--publish=style`. A root `.markdownlint.json` that extends it is also created automatically (IDE support).

---

**[Back to js-dev-tools][pkg]**

[handbook]: ../../README.md
[packages]: ../README.md
[pkg]: ./README.md
