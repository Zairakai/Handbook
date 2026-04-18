# php-package template

> **[Handbook][handbook]** › **[Templates][templates]** › php-package

Boilerplate for new `zairakai/*` Composer packages. PHP 8.4, Laravel 12|13, Orchestra Testbench, full `zairakai/laravel-dev-tools` integration, GitLab CI ready.

**Repository:** `Templates/php-package/`
**Type:** Composer library

---

## Documentation

| Page | Contents |
| :--- | :--- |
| **[Structure][structure]** | Directory layout, key files, gitattributes |
| **[Patterns][patterns]** | ServiceProvider, TestCase, Orchestra Testbench, config publishing |
| **[Quality & CI][quality]** | laravel-dev-tools integration, PHPStan, phpunit.xml, make targets, CI pipeline |

---

## Placeholders

| Placeholder | Description | Example |
| :--- | :--- | :--- |
| `{{PACKAGE_SLUG}}` | Identifier — lowercase, kebab-case | `my-feature` |
| `{{PACKAGE_NAME}}` | PHP namespace segment — CamelCase | `MyFeature` |
| `{{PACKAGE_DESCRIPTION}}` | One-line description | `Provides X for Laravel apps` |
| `{{SERVICE_DESK_TOKEN}}` | GitLab Service Desk email token | `contact-project+...@incoming.gitlab.com` |

`{{PACKAGE_SLUG}}` and `{{PACKAGE_NAME}}` appear in multiple files — replace with a global search across the whole directory.

---

## Quick start

```bash
# 1. Copy template
cp -r Templates/php-package/ path/to/new-package/
cd path/to/new-package/

# 2. Replace all placeholders (slug, name, description, service-desk token)

# 3. Create the package config file referenced by ServiceProvider
touch config/{{PACKAGE_SLUG}}.php

# 4. Install — post-install-cmd triggers setup-package.sh automatically
composer install

# 5. Validate
make quality
```

---

**[Back to Templates][templates]**

[handbook]: ../../README.md
[templates]: ../README.md
[structure]: ./structure.md
[patterns]: ./patterns.md
[quality]: ./quality.md
