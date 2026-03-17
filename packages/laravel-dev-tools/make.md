# Make Targets — laravel-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › **[laravel-dev-tools][pkg]** › Make Targets

All targets delegate to shell scripts in `scripts/`. Two entry points exist:

- **`core.mk`** — PHP-only projects
- **`fullstack.mk`** — PHP + JS projects (auto-detects `@zairakai/js-dev-tools`)

---

## Quality Gates

| Target | Description |
| :--- | :--- |
| `make quality` | Full gate: `markdownlint shellcheck rector pint phpstan insights` |
| `make quality-fast` | Fast CI check: `pint + phpstan + markdownlint` (via `ci-quality.sh`) |
| `make quality-fix` | Auto-fix: `markdownlint-fix rector-fix pint-fix` |
| `make ci` | Full CI simulation: `quality + test + bats` |

---

## Code Style

| Target | Description |
| :--- | :--- |
| `make pint` | Check code style (Pint) |
| `make pint-fix` | Fix code style automatically |

---

## Static Analysis

| Target | Description |
| :--- | :--- |
| `make phpstan` | Run PHPStan at max level |
| `make phpstan-baseline` | Generate / update `config/dev-tools/baseline.neon` |

---

## Code Modernization

| Target | Description |
| :--- | :--- |
| `make rector` | Rector dry-run — show what would change |
| `make rector-fix` | Apply Rector transformations |

---

## Code Quality

| Target | Description |
| :--- | :--- |
| `make insights` | PHPInsights analysis (min 80% quality) |
| `make insights-fix` | PHPInsights with auto-fix |

---

## Security

| Target | Description |
| :--- | :--- |
| `make enlightn` | Run Enlightn security + performance analysis |
| `make enlightn-ci` | Enlightn in strict CI mode |

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
| `make test` | All PHPUnit tests |
| `make test-unit` | `tests/Unit/` only |
| `make test-feature` | `tests/Feature/` only |
| `make test-coverage` | PHPUnit with HTML + LCOV coverage in `build/coverage/` |
| `make test-ci` | PHPUnit in strict CI mode |

---

## Shell Testing (BATS)

| Target | Description |
| :--- | :--- |
| `make bats` | All BATS tests (`unit/` + `integration/`) |
| `make bats-unit` | `tests/bats/unit/` only |
| `make bats-integration` | `tests/bats/integration/` only |
| `make test-all` | PHPUnit + BATS |
| `make install-bats` | Install BATS framework |

---

## Metrics

| Target | Description |
| :--- | :--- |
| `make phpmetrics` | Generate PHPMetrics HTML report |

---

## Composer

| Target | Description |
| :--- | :--- |
| `make composer-install` | `composer install --prefer-dist --no-interaction --optimize-autoloader` |
| `make composer-update` | `composer update --prefer-dist --no-interaction --optimize-autoloader` |
| `make composer-normalize` | Check `composer.json` normalization |
| `make composer-validate` | `composer validate --strict` |

---

## Utils

| Target | Description |
| :--- | :--- |
| `make doctor` | Environment diagnostics (PHP, tools, paths) |
| `make security-audit` | Composer security audit |
| `make install-hooks` | Install git hooks into `.githooks/` |
| `make install-packages` | Interactive optional PHP tools installer |
| `make setup` | Re-run `setup-package.sh` |
| `make uninstall` | Remove dev-tools configuration files |
| `make git-update` | Fast-forward all local branches that track a remote |
| `make git-cleanup` | Remove local branches with no upstream |

---

## Full-stack mode (PHP + JS)

When `@zairakai/js-dev-tools` is in `node_modules`, `fullstack.mk` extends the quality and CI gates with JS targets:

```text
make quality   → + eslint prettier stylelint knip
make ci        → + typecheck js-test-ci
```

JS targets (see `@zairakai/js-dev-tools` make targets):

```bash
make eslint         # ESLint check
make eslint-fix     # ESLint auto-fix
make prettier       # Prettier format check
make prettier-fix   # Prettier auto-fix
make stylelint      # CSS/SCSS lint
make typecheck      # tsc --noEmit
make knip           # Dead code + unused deps
make js-test        # Vitest (JS tests only, not extended to PHPUnit)
make js-test-coverage  # Vitest with coverage
make js-test-ci     # Vitest strict CI mode
make test           # PHPUnit + js-test (double-colon append)
make test-coverage  # PHPUnit coverage + js-test-coverage
```

---

**[Back to laravel-dev-tools][pkg]**

[handbook]: ../../README.md
[packages]: ../README.md
[pkg]: ./README.md
