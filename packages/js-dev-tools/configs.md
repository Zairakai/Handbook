# Configs — js-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › **[js-dev-tools][pkg]** › Configs

The configurations described here are defined in `@stanislas-poisson/js-dev-tools` and re-exported by this package under the same paths.

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
| Config files | `**/*.config.{js,ts}`, `**/*rc.{js,ts}` | Relaxed `no-console` + `no-require-imports` |

### Key rules

| Rule | Value | Rationale |
| :--- | :--- | :--- |
| `yoda` | `never` | No yoda comparisons (ESLint default) |
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
| `trailingComma` | `all` |
| `bracketSpacing` | true |
| `bracketSameLine` | false |
| `arrowParens` | `always` |
| `endOfLine` | `lf` |
| `vueIndentScriptAndStyle` | false |
| `jsxSingleQuote` | false |

### File-specific overrides

| Files | Notable settings |
| :--- | :--- |
| `*.blade.php` | `phpVersion: '8.3'`, `trailingCommaPHP: true` |
| `*.json`, `*.jsonc`, `package.json` | `trailingComma: 'none'` |

### Extending in a project

```javascript
// config/dev-tools/prettier.config.js (published stub)
import baseConfig from '@zairakai/js-dev-tools/prettier'

export default { ...baseConfig }
```

---

## Stylelint

Exported as `@zairakai/js-dev-tools/stylelint`. Extends `stylelint-config-standard-scss` + `stylelint-config-recommended-vue` + `stylelint-config-html`. Plugins: `stylelint-scss`, `stylelint-order`.

### Key rules

| Rule | Value |
| :--- | :--- |
| `declaration-block-no-redundant-longhand-properties` | true |
| `length-zero-no-unit` | true |
| `scss/at-rule-no-unknown` | true |
| `scss/at-mixin-argumentless-call-parentheses` | `never` |
| `scss/dollar-variable-pattern` | `^[_a-z0-9\\-]+$` |
| `scss/selector-no-redundant-nesting-selector` | true |
| `order/order` | custom properties, `$` variables, `@extend`, `@include` (no block), declarations, nested rules, other at-rules |
| `order/properties-order` | 7 groups: positioning, display and layout, box model, typography, visual, transforms and animations, miscellaneous (`content` first); unlisted properties last, alphabetical |

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
    environment: 'node',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'lcov', 'html', 'cobertura'],
      thresholds: { lines: 75, functions: 75, branches: 75, statements: 75 },
      watermarks: { lines: [75, 90], functions: [75, 90], branches: [75, 90], statements: [75, 90] },
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

> Always spread `ignoreDependencies` and `ignoreBinaries` - numeric-keyed arrays do not deep-merge.

### What the base ignores

`ignoreDependencies` lists `@vitest/coverage-v8` (the Vitest config names the provider as a text, `provider: 'v8'`, and Knip cannot link it to the package), `zod` and `sort-package-json`. `ignoreBinaries` lists `make`.

Knip may print the hint `Remove @vitest/coverage-v8 from ignoreDependencies` in the repository of the package itself. It is a hint, not an error: the entry stays, because the projects inherit the list.

The repository of the package has its own `knip.config.js` at its root, which is not published. It extends `config/knip.config.js` and adds the tools that the shell scripts of the package run (`eslint`, `prettier`, `stylelint`, `markdownlint-cli2`), which Knip does not read.

---

## tsconfig.base.json

Base TypeScript config. Published to `tsconfig.json` via `--publish=typescript`.

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
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

---

## Ignore lists

The generated and third-party folders are ignored by every tool: ESLint (`ignores` in `eslint.config.js`), Prettier (`.prettierignore`), Stylelint (`.stylelintignore`) and markdownlint (`.markdownlintignore`). The ESLint ignore list also reads `.gitignore`.

| Path | ESLint | Prettier | Stylelint | markdownlint |
| :--- | :---: | :---: | :---: | :---: |
| `node_modules/`, `vendor/` | yes | yes | yes | yes |
| `build/`, `coverage/`, `dist/` | yes | yes | yes | yes |
| `docs/` (VitePress site) | yes | yes | yes | yes |
| `public/` | yes | yes | yes | no |
| `.nuxt/`, `.output/`, `storybook-static/` | yes | yes | `.nuxt/`, `.output/` | no |
| `.cache/`, `.git/` | no | no | no | yes |
| `*.min.js` | yes | yes | no | no |
| `*.min.css` | no | yes | yes | no |
| `*.min.md` | no | no | no | yes |
| `*.lock`, `gl-*.json`, `*.md` | no | yes | no | no |
| `storage/`, `bootstrap/cache/` (Laravel, this package only) | yes | yes | yes | yes |
