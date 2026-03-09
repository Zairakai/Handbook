# EditorConfig Standard

> **[Handbook][handbook]** › **[Coding Standards][standards]** › EditorConfig

---

## Overview

The `.editorconfig` file enforces consistent formatting across all editors and IDEs, regardless of developer environment. Every Zairakai project includes one — it is published automatically by the relevant `dev-tools` package on setup.

**Official reference**: [editorconfig.org][editorconfig]

---

## Standard Configuration

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

[*.php]
indent_size = 4

[*.md]
trim_trailing_whitespace = false
max_line_length = 120

[Makefile]
indent_style = tab
```

---

## Rules Explained

| Key | Value | Purpose |
| :--- | :--- | :--- |
| `indent_style` | `space` | Spaces everywhere except Makefile (requires tabs). |
| `indent_size` | `2` | Default — PHP uses 4. |
| `end_of_line` | `lf` | Unix line endings — mandatory for cross-platform consistency. |
| `charset` | `utf-8` | All files in UTF-8. |
| `trim_trailing_whitespace` | `true` | No invisible trailing spaces (except `.md` — two trailing spaces = line break). |
| `insert_final_newline` | `true` | All files end with a newline. |

---

## Notes

- **Markdown** (`*.md`): `trim_trailing_whitespace = false` because two trailing spaces are a valid Markdown line break (`  \n`).
- **Makefile**: must use **tabs** — Make will fail with spaces.
- **PHP**: 4-space indentation follows the PSR-12 standard enforced by Pint.

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[editorconfig]: https://editorconfig.org/
