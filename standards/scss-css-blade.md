# SCSS / CSS / Blade Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › SCSS / CSS / Blade

---

## Overview

Stylesheet quality is enforced by **Prettier** (formatting) and **Stylelint** (linting).  
Blade templates follow the same formatting rules as HTML via Prettier.

---

## Tools

| Tool | Purpose | Config file |
| :--- | :--- | :--- |
| **[Prettier]** | Formatter for SCSS, CSS, HTML/Blade | `config/dev-tools/prettier.config.js` |
| **[Stylelint]** | SCSS/CSS linter | `config/dev-tools/stylelint.config.js` |

---

## SCSS / CSS Conventions

### Formatting Rules (Prettier)

- **2 spaces** for indentation
- **Single quotes** for strings
- **Trailing semicolons** always
- **Max line length**: none (Google HTML/CSS Style Guide: no column limit); Prettier wraps at its default of 80

### BEM Naming

All CSS classes follow the **BEM** (Block, Element, Modifier) methodology:

```scss
// Block
.card { }

// Element
.card__title { }
.card__body  { }
.card__action { }

// Modifier
.card--featured { }
.card__action--disabled { }
```

### SCSS Nesting

```scss
.card {
  padding: 1rem;
  border-radius: 0.5rem;

  &__title {
    font-size: 1.25rem;
    color: #333;
  }

  &__body {
    font-size: 1rem;
    color: #666;
  }

  &--featured {
    border: 2px solid var(--color-primary);
  }
}
```

- **Maximum nesting depth**: 3 levels
- **Use `&` for BEM elements and modifiers** — never write full class names inside a rule
- **Variables**: use CSS custom properties (`--color-primary`) over SCSS variables for runtime theming

### Properties Order

The order is enforced by Stylelint (`stylelint-order`), and `--fix` applies it.

- **Inside a block**: custom properties, `$` variables, `@extend`, `@include` without a block, declarations, nested rules, then the other at-rules (`@media`).
- **Properties**: `content`, positioning, display and layout, box model, typography, visual, transforms and animations, then miscellaneous. A property that is not in the list goes last, in alphabetical order.

```scss
.element {
  @include reset;

  // Positioning
  position: absolute;
  top: 0;
  left: 0;
  z-index: 10;

  // Display and layout
  display: flex;
  gap: 1rem;

  // Box model
  width: 100%;
  margin: 0 auto;
  padding: 1rem;
  border-radius: 0.5rem;

  // Typography
  font-size: 1rem;
  font-weight: 600;

  // Visual
  color: #333;
  background-color: #fff;
  box-shadow: 0 4px 8px rgb(0 0 0 / 10%);

  // Transitions and animations
  transition: transform 0.2s ease;

  &:hover {
    transform: scale(1.02);
  }

  @media (width >= 768px) {
    padding: 2rem;
  }
}
```

---

## Blade Conventions

- **2 spaces** indentation (same as HTML)
- Use **Blade components** (`<x-component />`) over raw HTML for reusable UI
- Keep logic out of Blade — use `@php` only as a last resort
- Use `{{ }}` for escaped output, `{!! !!}` only for trusted HTML

```html
<x-layout.card :title="$article->title">
    <x-content.paragraph>
        {{ $article->excerpt }}
    </x-content.paragraph>

    <x-form.button href="{{ route('articles.show', $article) }}">
        Read more
    </x-form.button>
</x-layout.card>
```

---

## Make Commands Reference

```bash
make quality      # Full gate (includes stylelint + prettier check)
make stylelint    # Run Stylelint on SCSS/CSS files
make prettier     # Check formatting with Prettier
make quality-fix  # Fix all auto-fixable issues (ESLint, Prettier, Stylelint, markdownlint)
```

---

**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[Prettier]: https://prettier.io/
[Stylelint]: https://stylelint.io/
