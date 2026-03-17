# JavaScript / Vue.js Standards

> **[Handbook][handbook]** › **[Coding Standards][standards]** › JavaScript / Vue.js

---

## Overview

JavaScript and Vue.js projects use `@zairakai/js-dev-tools` for unified quality enforcement. All tools run via `make quality`.

---

## Tools

| Tool | Purpose | Config file |
| :--- | :--- | :--- |
| **[ESLint]** | Static analysis + linting | `config/dev-tools/eslint.config.js` |
| **[Prettier]** | Code formatter | `config/dev-tools/prettier.config.js` |
| **[Stylelint]** | CSS/SCSS linting | `config/dev-tools/stylelint.config.js` |
| **[TypeScript]** | Type checking | `tsconfig.json` |
| **[Knip]** | Unused exports and dependencies | `config/dev-tools/knip.config.js` |
| **[Vitest]** | Unit testing | `config/dev-tools/vitest.config.js` |

---

## Vue.js Component Structure

### Directory Organization

```bash
resources/js/
├── Components/
│   ├── Content/      # Blockquote, Heading, Link, Paragraph…
│   ├── Form/         # Button, Input, Checkbox, Select…
│   ├── Layout/       # Header, Footer, Nav, Section…
│   └── Medias/       # Image, Video, Audio, Iframe…
├── Pages/            # Full page components (About, Dashboard…)
└── Stores/           # Pinia stores (Global.js, User.js…)
```

### Component Template

```vue
<template>
  <div class="card">
    <h2 class="card__title">{{ title }}</h2>
    <p class="card__body">{{ description }}</p>
    <button class="card__action" @click="toggle">
      {{ isOpen ? 'Close' : 'Open' }}
    </button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  title: { type: String, required: true },
  description: { type: String, default: '' },
})

const isOpen = ref(false)
const toggle = () => { isOpen.value = !isOpen.value }
</script>

<style scoped lang="scss">
.card {
  padding: 1rem;
  border-radius: 0.5rem;

  &__title { font-size: 1.25rem; }
  &__body  { color: #666; }
  &__action { cursor: pointer; }
}
</style>
```

### Rules

- Use **`<script setup>`** (Composition API) — not Options API for new components
- **One component per file**, filename matches component name (`UserCard.vue`)
- **Props**: always typed, required or with default
- **Events**: prefix with `on` in parent (`@onSubmit`) and emit as `submit` in child
- **Scoped styles**: always use `scoped` on `<style>`

---

## Pinia Store Pattern

```javascript
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useUserStore = defineStore('user', () => {
  const currentUser = ref(null)
  const isAuthenticated = computed(() => currentUser.value !== null)

  function setUser(user) {
    currentUser.value = user
  }

  function logout() {
    currentUser.value = null
  }

  return { currentUser, isAuthenticated, setUser, logout }
})
```

- Use the **setup store** syntax (composable-style)
- Store files: `PascalCase.js` — `User.js`, `Global.js`
- One store per domain — do not mix unrelated state

---

## Naming Conventions

| Element | Convention | Example |
| :--- | :--- | :--- |
| Components | `PascalCase.vue` | `UserCard.vue` |
| Composables | `use` prefix | `useAuth.js` |
| Stores | `PascalCase.js` | `User.js` |
| CSS classes | `kebab-case` BEM | `card__title` |
| JS variables | `camelCase` | `currentUser` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |

---

## TypeScript

For packages and typed projects, TypeScript is mandatory:

```typescript
interface User {
  id: number
  name: string
  email: string
}

function getUser(id: number): Promise<User> {
  return fetch(`/api/users/${id}`).then(r => r.json())
}
```

- No `any` — use `unknown` and narrow types
- Prefer `interface` over `type` for object shapes
- Always declare return types on public functions

---

## Vite — Best Practices

- **Aliases**: configure `@`, `@components`, `@stores` in `vite.config.js` for clean imports
- **Minification**: enabled in production (`esbuild`), disabled locally for easier debugging
- **Sourcemaps**: generated locally only
- **Images**: optimize with `vite-plugin-imagemin` — always generate WebP variants
- **Chunk size**: warn if any chunk exceeds 1600 KB

---

## Make Commands Reference

```bash
make quality      # Full gate: eslint + prettier + typecheck + stylelint + markdownlint
make quality-fix  # Fix all auto-fixable issues
make eslint       # Run ESLint
make prettier     # Check formatting
make typecheck    # TypeScript type checking
make stylelint    # CSS/SCSS linting
make knip         # Unused exports/dependencies check
make test         # Vitest unit tests
make bats         # BATS shell integration tests
make test-all     # Vitest + BATS
make markdownlint # Markdown lint
```

---

[ESLint]: https://eslint.org/
[Prettier]: https://prettier.io/
[Stylelint]: https://stylelint.io/
[TypeScript]: https://www.typescriptlang.org/
**[Back to Coding Standards][standards]**

[handbook]: ../README.md
[standards]: ./README.md
[Knip]: https://knip.dev/
[Vitest]: https://vitest.dev/
