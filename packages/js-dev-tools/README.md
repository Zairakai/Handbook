# @zairakai/js-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › js-dev-tools

Unified JS/TS development toolchain. Provides code style, static analysis, CSS linting, dead code detection, testing, Makefiles, git hooks, and GitLab CI templates — all configured and ready to use.

**Repository:** `NPM-Packages/js-dev-tools/`
**npm:** `@zairakai/js-dev-tools`
**Type:** ESM (`"type": "module"`)

---

## What it provides

| Category | Tools |
| :--- | :--- |
| **Code style** | ESLint (flat config) + TypeScript + Vue rules |
| **Formatting** | Prettier (JS, TS, Vue, SCSS, JSON, YAML, Blade, PHP) |
| **CSS/SCSS** | Stylelint (standard-scss + vue + html) |
| **Dead code** | Knip (unused exports, files, dependencies) |
| **TypeScript** | `tsconfig.base.json` (strict, ES2022, ESNext modules) |
| **Testing** | Vitest (v8 coverage, jsdom or node) |
| **Documentation** | Markdownlint |
| **Shell scripts** | ShellCheck (100% compliance) |
| **Git hooks** | commit-msg, prepare-commit-msg, pre-commit, pre-push |
| **CI** | GitLab CI pipeline templates (JS app + package) |
| **Make system** | `core.mk` for JS-only, extended by `fullstack.mk` from laravel-dev-tools |

---

## Documentation

| Page | Contents |
| :--- | :--- |
| **[Setup][setup]** | Installation, setup-project.sh, publish targets, config cascade, postinstall |
| **[Configs][configs]** | ESLint rules, Prettier options, Stylelint, Vitest, Knip, tsconfig |
| **[Make targets][make]** | All available `make` targets |

---

## Install

```bash
npm install --save-dev @zairakai/js-dev-tools
```

The `postinstall` hook runs `setup-project.sh --silent` automatically on every `npm install`.

To trigger setup manually:

```bash
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh
```

---

## Quick reference

```bash
make quality        # Full quality gate (all checks)
make quality-fast   # Fast CI check (ESLint + Prettier + Markdownlint)
make quality-fix    # Auto-fix (ESLint + Prettier + Stylelint + Markdownlint)
make test           # Vitest
make test-coverage  # Vitest with coverage
make test-ci        # Vitest strict CI mode
make typecheck      # tsc --noEmit
make eslint         # ESLint only
make prettier       # Prettier only
make stylelint      # Stylelint only
make knip           # Dead code detection
make markdownlint   # Markdown lint
make shellcheck     # ShellCheck
make doctor         # Environment diagnostics
```

---

## Package exports

All bundled configs are exported directly:

```javascript
// Extend in your project stubs
import baseConfig from '@zairakai/js-dev-tools/eslint'
import baseConfig from '@zairakai/js-dev-tools/prettier'
import baseConfig from '@zairakai/js-dev-tools/stylelint'
import baseConfig from '@zairakai/js-dev-tools/vitest'
import baseConfig from '@zairakai/js-dev-tools/knip'
// tsconfig.base.json
// "extends": "@zairakai/js-dev-tools/config/tsconfig.base.json"
```

---

**[Back to Packages][packages]**

[handbook]: ../../README.md
[packages]: ../README.md
[setup]: ./setup.md
[configs]: ./configs.md
[make]: ./make.md
