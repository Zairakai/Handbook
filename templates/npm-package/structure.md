# Structure — npm-package

> **[Handbook][handbook]** › **[Templates][templates]** › **[npm-package][pkg]** › Structure

---

## Directory layout

```text
npm-package/
├── .gitlab-ci.yml           ← includes js-dev-tools shared pipeline
├── .gitignore               ← /node_modules /dist /docs /coverage
├── CODE_OF_CONDUCT.md       ← references global handbook
├── CONTRIBUTING.md          ← references global handbook, dev workflow table
├── LICENSE                  ← MIT
├── Makefile                 ← delegates to js-dev-tools/tools/make/core.mk
├── README.md                ← badges + requirements + install + usage
├── SECURITY.md              ← references global handbook
├── package.json             ← version always 0.0.0 (CI manages)
├── tsconfig.json            ← extends config/dev-tools/tsconfig.json
├── src/
│   └── index.ts             ← package entry point (export {})
└── tests/
    ├── bats/
    │   └── .gitkeep         ← BATS shell tests (add *.bats here)
    └── unit/
        └── .gitkeep         ← Vitest unit tests (add *.test.ts here)
```

After `npm install`, `postinstall` populates `config/dev-tools/`:

```text
config/
└── dev-tools/
    ├── eslint.config.js     ← extends @zairakai/js-dev-tools/eslint
    ├── prettier.config.js   ← extends @zairakai/js-dev-tools/prettier
    ├── knip.config.js       ← extends @zairakai/js-dev-tools/knip
    ├── vitest.config.js     ← extends @zairakai/js-dev-tools/vitest
    ├── tsconfig.json        ← extends @zairakai/js-dev-tools tsconfig.base
    ├── .markdownlint.json   ← markdownlint rules
    └── .markdownlintignore
.markdownlint.json           ← root file extending config/dev-tools/ (IDE support)
```

---

## Makefile

```makefile
NPM_DIRECTORY_TOOLS_PROJECT_NAME := "{{PACKAGE_NAME}}"
NPM_DIRECTORY_TOOLS_PROJECT_ROOT := $(shell pwd)

include node_modules/@zairakai/js-dev-tools/tools/make/core.mk
```

Two variables, one include. All targets come from `core.mk`. Nothing else.

---

## tsconfig.json

```json
{
  "extends": "./config/dev-tools/tsconfig.json"
}
```

The root `tsconfig.json` is a thin wrapper. The real config is in `config/dev-tools/tsconfig.json` (published by `js-dev-tools`), which itself extends `@zairakai/js-dev-tools/config/tsconfig.base.json`:

```json
{
  "extends": "@zairakai/js-dev-tools/config/tsconfig.base.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "build", "**/*.test.ts", "**/*.spec.ts"]
}
```

Key options inherited from `tsconfig.base.json`:

| Option | Value |
| :--- | :--- |
| `target` | `ES2022` |
| `module` | `ESNext` |
| `moduleResolution` | `bundler` |
| `strict` | `true` |
| `noUnusedLocals` | `true` |
| `noUnusedParameters` | `true` |
| `exactOptionalPropertyTypes` | `true` |
| `declaration` + `declarationMap` | `true` |
| `sourceMap` | `true` |

---

## src/index.ts

```typescript
/**
 * @zairakai/{{PACKAGE_SLUG}}
 * {{PACKAGE_DESCRIPTION}}
 */

export {}
```

Empty export — replace with actual exports as the package develops. The file header serves as the module-level JSDoc for TypeDoc.

---

## Tests structure

```text
tests/
├── bats/         ← BATS shell tests (*.bats files)
└── unit/         ← Vitest unit tests (*.test.ts files)
```

Vitest include pattern in `config/dev-tools/vitest.config.js`:

```javascript
test: {
  include: ['tests/unit/**/*.test.ts'],
}
```

BATS tests target shell scripts in `scripts/` if the package has any.

---

## .gitignore

```text
/node_modules
/dist
/docs          ← TypeDoc output
/coverage
npm-debug.log*
.idea/
.vscode/
```

`config/dev-tools/` is NOT ignored — published configs are committed and versioned.

---

**[Back to npm-package][pkg]**

[handbook]: ../../README.md
[templates]: ../README.md
[pkg]: ./README.md
