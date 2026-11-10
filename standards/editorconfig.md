# EditorConfig Standard

> **[Handbook][handbook]** › **[Coding Standards][standards]** › EditorConfig

---

## Overview

The `.editorconfig` file enforces consistent formatting across all editors and IDEs, regardless of developer environment. Every Zairakai project includes one — it is published automatically by the relevant `dev-tools` package on setup.

**Official reference**: [editorconfig.org][editorconfig]

---

## Standard Configuration

The full reference file below lists every file type used across Zairakai projects. Each repository keeps **only the sections that match the file types it actually contains** (a Laravel application keeps `*.php`, a Node package does not).

```editorconfig
# EditorConfig — https://editorconfig.org
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

# PHP
[*.php]
indent_size = 4
max_line_length = 120

# Blade: written after [*.php] on purpose, because *.php also matches *.blade.php
[*.blade.php]
indent_size = 2

# Python
[*.py]
indent_size = 4
max_line_length = 88

# Shell
[*.{sh,bash}]
indent_size = 2
max_line_length = 80

# Markdown: two trailing spaces are a line break
[*.md]
trim_trailing_whitespace = false
max_line_length = 120

# Make requires tabs
[{Makefile,*.mk}]
indent_style = tab
```

---

## Sections per repository type

| Repository | Sections to keep |
| :--- | :--- |
| Laravel application or PHP package | `[*]`, `[*.php]`, `[*.blade.php]` (when the repository has Blade templates), `[*.md]`, `[{Makefile,*.mk}]` |
| Node / Vue package | `[*]`, `[*.md]`, `[{Makefile,*.mk}]` |
| Python project | `[*]`, `[*.py]`, `[*.md]`, `[{Makefile,*.mk}]` |
| Docker / tooling repository | `[*]`, `[*.{sh,bash}]`, `[*.md]`, `[{Makefile,*.mk}]` |
| Documentation only | `[*]`, `[*.md]` |

Add a section only when the repository has files of that type. JSON, YAML, JavaScript, TypeScript, Vue, CSS and SCSS use the `[*]` default (2 spaces).

---

## Rules Explained

| Key | Value | Purpose |
| :--- | :--- | :--- |
| `indent_style` | `space` | Spaces everywhere except Make files (require tabs). |
| `indent_size` | `2` | Default. PHP and Python use 4. |
| `end_of_line` | `lf` | Unix line endings — mandatory for cross-platform consistency. |
| `charset` | `utf-8` | All files in UTF-8. |
| `trim_trailing_whitespace` | `true` | No invisible trailing spaces (except `.md` — two trailing spaces = line break). |
| `insert_final_newline` | `true` | All files end with a newline. |
| `max_line_length` | per type | A guide, not a hard limit: only some editors interpret it. |

---

## Notes

- **Line length is a guide.** The values follow each language's style guide (PHP: PER, soft limit 120; Python: ruff default 88; Shell: Google Shell Style Guide 80). Only the commit hook blocks on length.
- **Markdown** (`*.md`): `trim_trailing_whitespace = false` because two trailing spaces are a valid Markdown line break (`  \n`).
- **Makefile**: must use **tabs** — Make will fail with spaces.
- **PHP**: 4-space indentation follows PER/PSR-12, enforced by Pint, and the Laravel `.editorconfig`.
- **Blade** (`*.blade.php`): 2 spaces, like HTML (see [SCSS / CSS / Blade][scss-css-blade]). The pattern `*.php` also matches `*.blade.php`, so the `[*.blade.php]` section must come after `[*.php]` to override it.
- **Shell**: 2 spaces, as in the Google Shell Style Guide.

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[scss-css-blade]: ./scss-css-blade.md
[editorconfig]: https://editorconfig.org/
