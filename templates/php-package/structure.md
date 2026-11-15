# Structure — php-package

> **[Handbook][handbook]** › **[Templates][templates]** › **[php-package][pkg]** › Structure

---

## Directory layout

```text
php-package/
├── .gitattributes           ← LF line endings enforced on all text files
├── .gitlab-ci.yml           ← includes laravel-dev-tools shared pipeline
├── .gitignore
├── CODE_OF_CONDUCT.md       ← references global handbook
├── CONTRIBUTING.md          ← references global handbook, dev workflow table
├── LICENSE                  ← MIT
├── Makefile                 ← delegates to laravel-dev-tools/tools/make/core.mk
├── README.md                ← badges + requirements + install + usage
├── SECURITY.md              ← references global handbook
├── composer.json
├── .markdownlint.json       ← thin: extends config/dev-tools/.markdownlint.json
├── .markdownlintignore      ← copy of config/dev-tools/.markdownlintignore
├── phpstan.neon             ← thin: includes config/dev-tools/phpstan.neon
├── phpunit.xml              ← copy of config/dev-tools/phpunit.xml (paths of the root)
├── pint.json                ← copy of config/dev-tools/pint.json
├── build/
│   └── logs/
│       └── .gitkeep         ← clover.xml, cobertura.xml, junit.xml written here
├── config/
│   └── dev-tools/           ← the configuration of the project, one file per tool
│       ├── .markdownlint.json
│       ├── .markdownlintignore
│       ├── baseline.neon    ← empty PHPStan baseline (fill via make phpstan-baseline)
│       ├── phpinsights.php
│       ├── phpstan.neon     ← extends laravel-dev-tools/config/library.neon
│       ├── phpunit.xml      ← Unit + Feature suites, coverage reports
│       ├── pint.json        ← the complete file, edited by the project
│       └── rector.php
├── src/
│   └── ServiceProvider.php  ← package entry point, auto-discovered by Laravel
└── tests/
    ├── TestCase.php          ← extends Orchestra\Testbench, registers ServiceProvider
    ├── Feature/
    │   └── .gitkeep          ← integration tests go here
    └── Unit/
        └── .gitkeep          ← unit tests go here
```

---

## Makefile

```makefile
.DEFAULT_GOAL := help

include vendor/zairakai/laravel-dev-tools/tools/make/core.mk
```

One include. All targets come from `core.mk`. Optional overrides (commented out in template):

```makefile
# ZAIRAKAI_DOCKER_APP := my-app-container
# CMD_PINT    := docker exec my-app vendor/bin/pint
# CMD_PHPSTAN := docker exec my-app vendor/bin/phpstan
```

---

## Autoload

```json
"autoload": {
    "psr-4": { "Zairakai\\{{PACKAGE_NAME}}\\": "src/" }
},
"autoload-dev": {
    "psr-4": { "Zairakai\\{{PACKAGE_NAME}}\\Tests\\": "tests/" }
}
```

PSR-4 strict. `src/` for production code, `tests/` for test code only.

---

## .gitattributes

```text
* text=auto eol=lf

*.md   diff=markdown
*.php  diff=php
*.json text eol=lf
*.yml  text eol=lf
*.sh   text eol=lf

*.png binary
*.jpg binary
*.svg binary
*.woff binary
*.ttf binary
```

`eol=lf` is enforced on all text files — Windows contributors included. Binary files are excluded from diff/merge.

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
/vendor
/node_modules

# Build output
# ----------------
/build

# Laravel runtime
# ----------------
/storage

# Tests and coverage
# ----------------
.phpunit.cache
.phpunit.result.cache

# Logs
# ----------------
*.log
```

`config/dev-tools/` is **not** ignored - published configs are committed and versioned alongside the package. `.dev-tools-backup/`, where a forced publication keeps the files that it writes over, is ignored.

---

## build/ directory

PHPUnit writes reports here during `make test-coverage`:

| File | Format |
| :--- | :--- |
| `build/logs/clover.xml` | Clover (CI coverage gate) |
| `build/logs/cobertura.xml` | Cobertura (GitLab coverage visualization) |
| `build/logs/junit.xml` | JUnit (test results panel in GitLab) |
| `build/coverage/` | HTML interactive coverage report |

`build/` is gitignored. `build/logs/.gitkeep` keeps the directory in the repository.

---

**[Back to php-package][pkg]**

[handbook]: ../../README.md
[templates]: ../README.md
[pkg]: ./README.md
