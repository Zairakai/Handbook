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
