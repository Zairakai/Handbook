# Zairakai Handbook

Welcome to the central source of truth for **Zairakai Engineering Standards**.  
This repository defines the unified rules, workflows, and policies applied across all projects in the ecosystem _— PHP packages, NPM packages, Laravel applications, Vue.js frontends, Bash scripts, and more._

---

## 📋 Core Documents

| Document | Purpose |
| :--- | :--- |
| **[Contributing][contributing]** | Standard workflow for contributing to any Zairakai project. |
| **[Security][security]** | Vulnerability reporting channels and response timelines. |
| **[Code of Conduct][code-of-conduct]** | Expected behavior and community standards. |

---

## 📐 Policies

Rules and processes — see **[Policies][policies]** for the overview.

| Policy | Description |
| :--- | :--- |
| **[Git Rules][git-rules]** | Commit format, branching strategy, breaking changes, and ticket requirements. |
| **[Versioning][versioning]** | SemVer, release process, git tags, Packagist/NPM publishing. |
| **[Dependency Management][dependency-management]** | How to update PHP and JS dependencies safely. |

---

## 📦 Packages

Centralized dev toolchain packages — see **[Packages][packages-overview]** for the overview.

| Package | Type | Description |
| :--- | :--- | :--- |
| **[zairakai/laravel-dev-tools][laravel-dev-tools]** | Composer | PHP/Laravel: PHPStan, Pint, Rector, PHPInsights, BATS, Makefile, git hooks, GitLab CI |
| **[@zairakai/js-dev-tools][js-dev-tools]** | npm | JS/TS: ESLint, Prettier, Stylelint, Vitest, Knip, TypeScript, Makefile, git hooks |

---

## 🗂️ Templates

Production-ready boilerplates — see **[Templates][templates]** for the overview.

| Template | Stack | Description |
| :--- | :--- | :--- |
| **[Laravel 11][laravel-11-template]** | Laravel 11 + Vue 3 + TypeScript | Full-stack boilerplate — API, Blade, frontend, auth, i18n, seeders, Vitest, BATS. |
| **[Laravel 12][laravel-12-template]** | Laravel 12 + Vue 3 + TypeScript | Same as Laravel 11 boilerplate, targeting `laravel/framework: ^12.0`. |
| **[npm-package][npm-package-template]** | TypeScript — ESM + CJS | Boilerplate for `@zairakai/*` NPM packages. |
| **[php-package][php-package-template]** | PHP 8.3 — Laravel 11\|12 | Boilerplate for `zairakai/*` Composer packages. |

---

## 🧑‍💻 Coding Standards

Full standards by language and tool — see **[Coding Standards][standards]** for the overview.

| Standard | Description |
| :--- | :--- |
| **[Bash][bash]** | Shebang, variables, control structures, ShellCheck. |
| **[EditorConfig][editorconfig]** | Indentation, line endings, charset — cross-editor consistency. |
| **[JavaScript / Vue.js][javascript-vue]** | Components, Pinia stores, ESLint, Prettier, Vite. |
| **[Makefile][makefile]** | Syntax, phony targets, unified Make system. |
| **[PHP][php]** | PSR-12, strict types, naming, PHPStan, Rector, PHPInsights. |
| **[Laravel][laravel]** | Controllers, Form Requests, Eloquent, routes, Service Providers. |
| **[SCSS / CSS / Blade][scss-css-blade]** | BEM, Prettier, Stylelint, Blade conventions. |
| **[Testing][testing]** | PHPUnit, Vitest, BATS — coverage 100%, structure, naming. |

---

## 🛠️ How to Use This Handbook

### For Project Maintainers

Reference this handbook from your project's local Markdown files instead of duplicating content:

```markdown
> This project follows the [Zairakai Global Security Policy][handbook-security].

[handbook-security]: https://gitlab.com/zairakai/handbook/-/blob/main/SECURITY.md
```

Use the `dev-tools` packages to publish pre-configured stub files to your project:

```bash
# PHP project
bash vendor/zairakai/laravel-dev-tools/scripts/setup-package.sh --publish=governance

# NPM project
bash node_modules/@zairakai/dev-tools/scripts/setup-project.sh --publish=governance
```

### For Contributors

Follow the rules defined here for every Merge Request. Any deviation is flagged by the automated quality gates (`make quality` / CI pipeline).

---

**Unified and Centralized by [Zairakai][zairakai]**

[packages-overview]: ./packages/README.md
[laravel-dev-tools]: ./packages/laravel-dev-tools/README.md
[js-dev-tools]: ./packages/js-dev-tools/README.md
[templates]: ./templates/README.md
[laravel-11-template]: ./templates/laravel-11/README.md
[laravel-12-template]: ./templates/laravel-12/README.md
[npm-package-template]: ./templates/npm-package/README.md
[php-package-template]: ./templates/php-package/README.md
[contributing]: ./CONTRIBUTING.md
[security]: ./SECURITY.md
[code-of-conduct]: ./CODE_OF_CONDUCT.md
[policies]: ./policies/README.md
[git-rules]: ./policies/git-rules.md
[versioning]: ./policies/versioning.md
[dependency-management]: ./policies/dependency-management.md
[standards]: ./standards/README.md
[bash]: ./standards/bash.md
[editorconfig]: ./standards/editorconfig.md
[javascript-vue]: ./standards/javascript-vue.md
[makefile]: ./standards/makefile.md
[php]: ./standards/php.md
[laravel]: ./standards/laravel.md
[scss-css-blade]: ./standards/scss-css-blade.md
[testing]: ./standards/testing.md
[zairakai]: https://gitlab.com/zairakai
