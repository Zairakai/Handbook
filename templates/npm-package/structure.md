# Structure — npm-package

> **[Handbook][handbook]** › **[Templates][templates]** › **[npm-package][pkg]** › Structure

---

## Directory layout

```text
npm-package/
├── .editorconfig            ← the standard of the handbook (copied by the setup)
├── .gitattributes           ← LF, Markdown diffs, binary files
├── .gitignore               ← standard blocks, see the gitignore standard
├── .gitlab-ci.yml           ← includes js-dev-tools shared pipeline
├── .npmrc                   ← engine-strict=true, audit-level=high
├── assets/                  ← banner.svg/png (1280x640) and icon.svg/png (512x512, transparent)
├── CODE_OF_CONDUCT.md       ← references global handbook
├── CONTRIBUTING.md          ← references global handbook, dev workflow table
├── LICENSE                  ← MIT
├── Makefile                 ← delegates to js-dev-tools/tools/make/core.mk
├── README.md                ← banner + badges + install + usage
├── SECURITY.md              ← references global handbook
├── package.json             ← version always 0.0.0 (CI manages)
├── tsconfig.json            ← extends @zairakai/js-dev-tools tsconfig base (published by the setup)
├── src/
│   └── index.ts             ← package entry point (export {})
└── tests/
    ├── bats/
    │   └── .gitkeep         ← BATS shell tests (add *.bats here)
    └── unit/
        └── .gitkeep         ← Vitest unit tests (add *.test.ts here)
```

After `npm install`, `postinstall` populates `config/dev-tools/` and the root. The files of the root exist for the editors and their extensions, which look for the configuration of a tool at the root of the project and never in `config/dev-tools/` (see [Editor integration][editor-integration]). They are committed, like the rest:

```text
config/
└── dev-tools/
    ├── eslint.config.js     ← the project rules, extends @zairakai/js-dev-tools/eslint
    ├── prettier.config.js   ← extends @zairakai/js-dev-tools/prettier
    ├── stylelint.config.js  ← extends @zairakai/js-dev-tools/stylelint
    ├── knip.config.js       ← dead code and unused dependencies
    ├── vitest.config.js     ← extends @zairakai/js-dev-tools/vitest
    ├── .markdownlint.json   ← markdownlint rules
    ├── .markdownlintignore
    ├── .prettierignore
    └── .stylelintignore
eslint.config.mjs            ← thin file: re-exports config/dev-tools/eslint.config.js
prettier.config.mjs          ← thin file: re-exports config/dev-tools/prettier.config.js
stylelint.config.mjs         ← thin file: re-exports config/dev-tools/stylelint.config.js
vitest.config.mjs            ← thin file: re-exports config/dev-tools/vitest.config.js (none when there is a vite.config.*)
.markdownlint.json           ← extends config/dev-tools/.markdownlint.json
.markdownlintignore          ← copy of config/dev-tools/.markdownlintignore (an ignore file cannot extend another)
.prettierignore              ← copy, same reason
.stylelintignore             ← copy, same reason
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

The setup publishes `tsconfig.json` at the root of the project (a TypeScript server looks for it there): it extends `@zairakai/js-dev-tools/tsconfig`, the base that the package exports.

```json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": "@zairakai/js-dev-tools/tsconfig",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "build", "**/*.test.ts", "**/*.spec.ts"]
}
```

Key options inherited from the base:

| Option | Value |
| :--- | :--- |
| `target` | `ES2025` |
| `module`, `moduleResolution` | `ESNext`, `bundler` |
| `strict`, `exactOptionalPropertyTypes`, `noUncheckedIndexedAccess` | `true` |
| `noUnusedLocals`, `noUnusedParameters` | `true` |
| `isolatedModules`, `verbatimModuleSyntax` | `true` |
| `declaration`, `declarationMap`, `sourceMap` | `true` |

The reason for each option is in the [js-dev-tools configs][js-dev-tools-configs].

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
# JetBrains (PhpStorm, WebStorm)
# ----------------
.idea/
.idea_modules/
*.iml
*.ipr
*.iws

# Visual Studio Code and derivatives
# ----------------
.vscode/
.history/

# Other editors
# ----------------
.fleet/
.zed/
.vs/
*.sublime-project
*.sublime-workspace
.project
.classpath
.settings/
nbproject/
*.swp
*.swo
*~

# AI assistants
# ----------------
# Personal instruction files: not published (the KB repositories force them with exceptions)
AGENTS.md
CLAUDE.md
GEMINI.md
*.local.md
.claude/settings.local.json
.aider*
.cursor/
.windsurf/
.continue/
.codex/
.gemini/

# Dependencies
# ----------------
/node_modules

# Build output
# ----------------
/dist
# TypeDoc output
/docs

# Tests and coverage
# ----------------
/coverage

# Logs
# ----------------
npm-debug.log*
```

`config/dev-tools/` is NOT ignored — published configs are committed and versioned.

---

**[Back to npm-package][pkg]**

[handbook]: ../../README.md
[editor-integration]: ../../standards/editor-integration.md
[js-dev-tools-configs]: ../../packages/js-dev-tools/configs.md#tsconfigbasejson
[templates]: ../README.md
[pkg]: ./README.md
