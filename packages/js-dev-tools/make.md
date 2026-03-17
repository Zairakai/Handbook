# Make Targets — js-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › **[js-dev-tools][pkg]** › Make Targets

All targets delegate to shell scripts in `scripts/`. Entry point: `core.mk`.

In full-stack PHP + JS projects, these targets are composed into `fullstack.mk` from `laravel-dev-tools` — see [laravel-dev-tools make targets][laravel-make].

---

## Quality Gates

| Target | Description |
| :--- | :--- |
| `make quality` | Full gate: `markdownlint shellcheck eslint prettier stylelint knip` |
| `make quality-fast` | Fast CI check: `eslint + prettier + markdownlint` (via `ci-quality.sh`) |
| `make quality-fix` | Auto-fix: `eslint-fix prettier-fix stylelint-fix markdownlint-fix` |
| `make ci` | Full CI: `quality + typecheck + test + bats` |

---

## Code Style

| Target | Description |
| :--- | :--- |
| `make eslint` | Run ESLint check |
| `make eslint-fix` | Fix ESLint issues automatically |
| `make prettier` | Check formatting with Prettier |
| `make prettier-fix` | Fix formatting automatically |

---

## CSS / SCSS

| Target | Description |
| :--- | :--- |
| `make stylelint` | Run Stylelint |
| `make stylelint-fix` | Fix Stylelint issues automatically |

---

## Dead Code Detection

| Target | Description |
| :--- | :--- |
| `make knip` | Detect unused exports, files, and dependencies |

---

## TypeScript

| Target | Description |
| :--- | :--- |
| `make typecheck` | Type-check without emitting (`tsc --noEmit`) |
| `make build` | Transpile TypeScript (tsup preferred, tsc fallback) |

---

## Documentation

| Target | Description |
| :--- | :--- |
| `make markdownlint` | Validate Markdown docs |
| `make markdownlint-fix` | Fix Markdown issues automatically |
| `make install-markdownlint` | Install `markdownlint-cli2` |

---

## Shell Scripts

| Target | Description |
| :--- | :--- |
| `make shellcheck` | ShellCheck — 100% compliance required |
| `make install-shellcheck` | Install ShellCheck |

---

## Testing

| Target | Description |
| :--- | :--- |
| `make test` | Vitest (no coverage) |
| `make test-coverage` | Vitest with coverage in `build/coverage/` |
| `make test-watch` | Vitest watch mode (interactive) |
| `make test-ci` | Vitest strict CI mode (strict + coverage) |

---

## Shell Testing (BATS)

| Target | Description |
| :--- | :--- |
| `make bats` | All BATS tests |
| `make bats-unit` | `tests/bats/unit/` only |
| `make bats-integration` | `tests/bats/integration/` only |
| `make test-all` | Vitest + BATS |
| `make install-bats` | Install BATS framework |

---

## Package Manager

| Target | Description |
| :--- | :--- |
| `make package-install` | `npm install` |
| `make package-update` | `npm update` |
| `make package-normalize` | Sort `package.json` fields (sort-package-json) |
| `make package-validate` | Validate `package.json` and lockfile sync |

---

## Utils

| Target | Description |
| :--- | :--- |
| `make doctor` | Environment diagnostics (Node, tools, paths) |
| `make outdated` | Check for outdated npm dependencies |
| `make install-hooks` | Install git hooks into `.githooks/` |
| `make git-update` | Fast-forward all local branches that track a remote |
| `make git-cleanup` | Remove local branches with no upstream |

---

**[Back to js-dev-tools][pkg]**

[handbook]: ../../README.md
[packages]: ../README.md
[pkg]: ./README.md
[laravel-make]: ../laravel-dev-tools/make.md
