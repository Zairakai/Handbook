# Coding Standards

> **[Handbook][handbook]** › Coding Standards

Adhering to coding standards ensures a consistent and maintainable codebase across all Zairakai projects. These rules apply regardless of the project type (PHP, JS, Make, Bash…).

---

## 🌐 General Principles

- **Indentation**: spaces everywhere (2 or 4 depending on file type — see each standard).
- **Comments**: clear and concise, in English, explaining *why* not *what*.
- **Naming**: descriptive names in English for files, variables, and functions.
- **Tools**: always run linters and formatters before committing (`make quality`).
- **Trailing whitespace**: none — enforced by `.editorconfig`.
- **Final newline**: always — enforced by `.editorconfig`.

---

## 📂 Standards by Language / Tool

| Standard | Description |
| :--- | :--- |
| **[Bash][bash]** | Writing maintainable shell scripts: shebang, variables, control structures, testing. |
| **[EditorConfig][editorconfig]** | Cross-editor formatting rules for indentation, line endings, and charset. |
| **[JavaScript / Vue.js][javascript-vue]** | Component structure, Pinia stores, ESLint, Prettier, Vite best practices. |
| **[Makefile][makefile]** | Syntax, variables, phony targets, and best practices for the unified Make system. |
| **[PHP][php]** | PSR-12, strict types, naming, PHPStan, Rector, PHPInsights — tout projet PHP. |
| **[Laravel][laravel]** | Controllers thin, Form Requests, Services, Eloquent, routes — projets Laravel. |
| **[SCSS / CSS / Blade][scss-css-blade]** | Styling conventions enforced by Prettier and ESLint. |
| **[Testing][testing]** | PHPUnit, Vitest, BATS — structure, naming, coverage 100%, annotations. |

---

## 🔧 Enforcement

Every project uses the **Unified Make System** provided by the relevant `dev-tools` package:

| Ecosystem | Package | Quality Command |
| :--- | :--- | :--- |
| PHP / Laravel | `zairakai/laravel-dev-tools` | `make quality` |
| JavaScript / Vue | `@zairakai/js-dev-tools` | `make quality` |

The CI pipeline enforces all standards automatically — no merge without a green quality gate.

---

**[Back to Handbook][handbook]**

[handbook]: ../README.md
[bash]: ./bash.md
[editorconfig]: ./editorconfig.md
[javascript-vue]: ./javascript-vue.md
[makefile]: ./makefile.md
[php]: ./php.md
[laravel]: ./laravel.md
[scss-css-blade]: ./scss-css-blade.md
[testing]: ./testing.md
