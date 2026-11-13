# EditorConfig Standard

> **[Handbook][handbook]** › **[Coding Standards][standards]** › EditorConfig

---

## Overview

The `.editorconfig` file enforces consistent formatting across all editors and IDEs, regardless of developer environment. Every Zairakai project includes one: it is published automatically by the relevant `dev-tools` package on setup.

**Official reference**: [editorconfig.org][editorconfig]

---

## Standard Configuration

The full reference file below lists every file type used across Zairakai projects. Each repository keeps **only the sections that match the file types it actually contains** (a Laravel application keeps `*.php`, a Node package does not).

```editorconfig
# EditorConfig: https://editorconfig.org
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

# PHP: PER Coding Style (4 spaces, soft limit of 120 columns)
[*.php]
indent_size = 4
max_line_length = 120

# Blade is HTML: 2 spaces and no column limit (Google HTML/CSS Style Guide).
# Written after [*.php] on purpose, because *.php also matches *.blade.php
[*.blade.php]
indent_size = 2
max_line_length = unset

# Python: PEP 8 (4 spaces, 79 columns)
[*.py]
indent_size = 4
max_line_length = 79

# JavaScript and TypeScript: Google JavaScript Style Guide (2 spaces, 80 columns)
[*.{cjs,cts,js,mjs,mts,ts}]
max_line_length = 80

# Shell and Bats: Google Shell Style Guide (2 spaces, 80 columns)
[*.{bash,bats,sh}]
indent_size = 2
max_line_length = 80

# Markdown: two trailing spaces are a line break
[*.md]
trim_trailing_whitespace = false
indent_size = unset

# Make requires tabs
[{Makefile,*.mk}]
indent_style = tab
```

---

## Sections per repository type

| Repository | Sections to keep |
| :--- | :--- |
| Laravel application or PHP package | `[*]`, `[*.php]`, `[*.blade.php]` (when the repository has Blade templates), `[*.md]`, `[{Makefile,*.mk}]` |
| Node / Vue package | `[*]`, `[*.{cjs,cts,js,mjs,mts,ts}]`, `[*.{bash,bats,sh}]` (when the repository has shell scripts or Bats tests), `[*.md]`, `[{Makefile,*.mk}]` |
| Python project | `[*]`, `[*.py]`, `[*.md]`, `[{Makefile,*.mk}]` |
| Docker / tooling repository | `[*]`, `[*.{bash,bats,sh}]`, `[*.md]`, `[{Makefile,*.mk}]` |
| Documentation only | `[*]`, `[*.md]` |

Add a section only when the repository has files of that type. JSON, YAML, Vue, CSS, SCSS and HTML use the `[*]` default (2 spaces) and have no column limit: no official rule or recommendation sets one.

---

## Rules Explained

| Key | Value | Purpose |
| :--- | :--- | :--- |
| `indent_style` | `space` | Spaces everywhere except Make files (require tabs). |
| `indent_size` | `2` | Default (Google style guides for JavaScript, HTML, CSS and shell). PHP and Python use 4. |
| `end_of_line` | `lf` | Unix line endings, mandatory for cross-platform consistency. |
| `charset` | `utf-8` | All files in UTF-8. |
| `trim_trailing_whitespace` | `true` | No invisible trailing spaces (except `.md`: two trailing spaces are a line break). |
| `insert_final_newline` | `true` | All files end with a newline. |
| `max_line_length` | per type | A guide, not a hard limit: only some editors interpret it. |

---

## Notes

- **A value is written only if a rule or a recommendation exists.** Official rules first (PER for PHP, PEP 8 for Python, the GNU make manual, CommonMark), then the most official recommendations (Google style guides for JavaScript, HTML/CSS and shell, Pro Git for commits). Where none exists (JSON, Vue, SCSS, Dockerfile, Compose, ini), nothing is set.
- **Line length is a guide.** PHP: PER, soft limit 120. Python: PEP 8, 79. JavaScript and TypeScript: Google, 80 (TypeScript by extension: the Google TypeScript guide states no column limit). Shell and Bats: Google, 80. HTML and CSS: Google recommends no limit. Prettier applies its default of 80 to every language it formats.
- **VS Code rulers** follow the same values (settings of the profiles, per language), plus 72 for Git commit messages (Pro Git: a body wrapped at about 72). The subject limit of about 50 characters counts the text after `type(scope): #TICKET `, which a ruler cannot do: only the commit hook checks it.
- **Markdown** (`*.md`): `trim_trailing_whitespace = false` because two trailing spaces are a valid Markdown line break (`  \n`). `indent_size = unset` because the indentation of a list item depends on its marker (a code block in `2. ` needs 3 spaces); markdownlint (`MD007`, `MD029`) checks lists.
- **Makefile**: must use **tabs**: Make will fail with spaces.
- **PHP**: 4-space indentation follows PER/PSR-12, enforced by Pint, and the Laravel `.editorconfig`.
- **Blade** (`*.blade.php`): a template of HTML, so 2 spaces and no column limit (see [SCSS / CSS / Blade][scss-css-blade]). The pattern `*.php` also matches `*.blade.php`, so the `[*.blade.php]` section must come after `[*.php]` to override it.
- **Shell and Bats**: 2 spaces and 80 columns, as in the Google Shell Style Guide.

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[scss-css-blade]: ./scss-css-blade.md
[editorconfig]: https://editorconfig.org/
