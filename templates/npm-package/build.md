# Build — npm-package

> **[Handbook][handbook]** › **[Templates][templates]** › **[npm-package][pkg]** › Build

---

## tsup — dual ESM + CJS

The package is built with `tsup` via `config/dev-tools/tsup.config.js` (published by `js-dev-tools`).

```bash
make build       # production build → dist/
make typecheck   # tsc --noEmit (no emit, type errors only)
```

Output:

```text
dist/
├── index.js     ← ESM  (consumed by bundlers, modern Node)
├── index.cjs    ← CJS  (consumed by require(), legacy toolchains)
├── index.d.ts   ← type declarations
└── index.d.ts.map
```

---

## package.json exports

```json
{
  "exports": {
    ".": {
      "types":   "./dist/index.d.ts",
      "import":  "./dist/index.js",
      "require": "./dist/index.cjs"
    }
  },
  "main":  "./dist/index.cjs",
  "types": "./dist/index.d.ts"
}
```

- `import` → ESM consumers (bundlers, `import` statements)
- `require` → CJS consumers (`require()`)
- `types` → TypeScript type resolution
- `main` → legacy Node.js fallback

For packages with multiple sub-exports, add entries under `exports`:

```json
{
  "exports": {
    ".":          { "types": "./dist/index.d.ts",    "import": "./dist/index.js" },
    "./utils":    { "types": "./dist/utils.d.ts",    "import": "./dist/utils.js" },
    "./helpers":  { "types": "./dist/helpers.d.ts",  "import": "./dist/helpers.js" }
  }
}
```

---

## Versioning

`package.json` stays at `0.0.0` permanently. **Never bump `version` manually.**

The CI pipeline resolves the version from the git tag at publish time:

```text
git tag 1.2.3 → CI reads tag → npm publish --tag latest (version 1.2.3)
```

This means `npm install @zairakai/{{PACKAGE_SLUG}}` always installs the latest tagged release regardless of what `package.json` says.

---

## TypeDoc

```bash
make docs    # → docs/  (gitignored — not committed)
```

TypeDoc config is published to `config/dev-tools/typedoc.json` by `js-dev-tools`. Entry point: `src/index.ts`. Uses JSDoc comments from source files.

---

## files published to npm

```json
{
  "files": [
    "dist",
    "src",
    "LICENSE",
    "README.md"
  ]
}
```

`config/`, `tests/`, `Makefile`, `tsconfig.json` are excluded from the npm tarball.

---

**[Back to npm-package][pkg]**

[handbook]: ../../README.md
[templates]: ../README.md
[pkg]: ./README.md
