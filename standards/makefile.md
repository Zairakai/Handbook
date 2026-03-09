# Makefile Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › Makefile

---

## Overview

A `Makefile` is the **single entry point** for all development tasks in Zairakai projects — formatting, testing, quality checks, CI, and more. Every project exposes a unified set of targets via the `dev-tools` package.

**Official reference**: [GNU Make Manual][gnu-make-manual]

---

## Basic Syntax

```makefile
target: dependencies
	command
```

- **target**: the name of the rule (`build`, `test`, `quality`…)
- **dependencies**: targets or files that must be up-to-date first
- **command**: shell command — **must be indented with a tab, not spaces**

---

## Variables

```makefile
# Define
DEV_TOOLS := node_modules/@zairakai/dev-tools

# Use
include $(DEV_TOOLS)/tools/make/core.mk
```

- Use `:=` (immediate assignment) over `=` (deferred) for most variables
- UPPERCASE for all Make variables
- Store paths and flags in variables — never hardcode

---

## Phony Targets

Always declare targets that are not files as `.PHONY` to avoid conflicts with same-named files:

```makefile
.PHONY: quality
quality:
	@make eslint prettier typecheck markdownlint

.PHONY: clean
clean:
	rm -rf dist/ node_modules/
```

---

## Help System

The `help` target is **provided by `dev-tools`** — do not rewrite it. Your responsibility is to annotate your own targets correctly so they appear in its output.

### Annotating targets

Add a `## description` comment after the target name:

```makefile
quality: ## Run the full quality gate
	@bash scripts/quality.sh

test: ## Run unit tests
	@bash scripts/test.sh
```

### Section headers

Use the `## —— Title ——` pattern (double `##`, em dashes, optional emoji) to create a named section in the help output. This acts as a visual separator — `make help` will display it as a group heading:

```makefile
## —— 🧪 Testing ——

test: ## Run unit tests
	@bash scripts/test.sh

test-all: ## Run unit + integration tests (BATS)
	@bash scripts/test-all.sh

## —— ✅ Quality ——

quality: ## Run the full quality gate
	@bash scripts/quality.sh
```

Targets without a `## comment` are **not shown** in `make help` — use this intentionally for internal targets that should not be exposed to contributors.

---

## File Dependencies

```makefile
# myapp depends on compiled objects
myapp: main.o utils.o
	gcc -o myapp main.o utils.o

main.o: main.c
	gcc -c main.c
```

---

## Unified Make System — Zairakai Targets

All projects expose these standard targets via `dev-tools`:

| Target | Description |
| :--- | :--- |
| `make quality` | Full quality gate (all checks). |
| `make test` | Run unit tests. |
| `make test-all` | Unit tests + integration tests (BATS). |
| `make markdownlint` | Lint all Markdown files. |
| `make shellcheck` | Validate all shell scripts. |
| `make help` | List all available targets. |

PHP-specific:

| Target | Description |
| :--- | :--- |
| `make cs` | Check PHP code style (Pint). |
| `make cs:fix` | Fix PHP code style (Pint). |
| `make analyse` | Static analysis (PHPStan). |
| `make rector` | Apply modernizations (Rector). |
| `make insights` | Architecture analysis (PHPInsights). |

JS-specific:

| Target | Description |
| :--- | :--- |
| `make eslint` | Run ESLint. |
| `make prettier` | Check formatting (Prettier). |
| `make typecheck` | Run TypeScript type checking. |
| `make stylelint` | Run Stylelint. |

---

## Best Practices

- **Use `.PHONY`** for all non-file targets.
- **No hardcoded paths** — use variables.
- **Annotate every public target** with `## description` — unnanoted targets are hidden from `make help`.
- **Group related targets** with a `## —— Title ——` section header.
- **Never use spaces for indentation** — Make requires tabs.
- **Delegate to scripts** for complex logic — keep Make targets thin.

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[gnu-make-manual]: https://www.gnu.org/software/make/manual/make.html
