# npm-package template

> **[Handbook][handbook]** › **[Templates][templates]** › npm-package

Boilerplate for new `@zairakai/*` NPM packages. TypeScript, ESM + CJS dual build, Vitest, full `@zairakai/js-dev-tools` integration, GitLab CI ready.

**Repository:** `Templates/npm-package/`
**Type:** npm package (`"type": "module"`)

---

## Documentation

| Page | Contents |
| :--- | :--- |
| **[Structure][structure]** | Directory layout, key files, tsconfig, entry point |
| **[Build][build]** | tsup dual build, package exports, TypeDoc, versioning |
| **[Quality & CI][quality]** | js-dev-tools integration, make targets, CI pipeline, git hooks |

---

## Placeholders

| Placeholder | Description | Example |
| :--- | :--- | :--- |
| `{{PACKAGE_SLUG}}` | Identifier — lowercase, kebab-case | `my-feature` |
| `{{PACKAGE_NAME}}` | Display name — used in Makefile | `My Feature` |
| `{{PACKAGE_DESCRIPTION}}` | One-line description | `Utility for doing X` |
| `{{SERVICE_DESK_TOKEN}}` | GitLab Service Desk email token | `contact-project+...@incoming.gitlab.com` |

---

## Quick start

```bash
# 1. Copy template
cp -r Templates/npm-package/ path/to/new-package/
cd path/to/new-package/

# 2. Replace all placeholders in all files (slug, name, description, service-desk token)

# 3. Install — postinstall publishes configs from js-dev-tools automatically
npm install

# 4. Validate
make quality
```

---

**[Back to Templates][templates]**

[handbook]: ../../README.md
[templates]: ../README.md
[structure]: ./structure.md
[build]: ./build.md
[quality]: ./quality.md
