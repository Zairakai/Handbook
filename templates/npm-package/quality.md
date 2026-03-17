# Quality & CI — npm-package

> **[Handbook][handbook]** › **[Templates][templates]** › **[npm-package][pkg]** › Quality & CI

---

## js-dev-tools integration

All tooling is delegated to `@zairakai/js-dev-tools`. The package is a `devDependency` — it brings ESLint, Prettier, Stylelint, Vitest, Knip, Markdownlint, ShellCheck and all their Makefile targets.

The `postinstall` hook runs on every `npm install`:

```json
"postinstall": "bash ./node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh --silent || true"
```

On first install, it publishes config stubs to `config/dev-tools/`. On subsequent installs, published files are hash-protected — user modifications are never overwritten.

---

## Make targets

```bash
make quality        # markdownlint + shellcheck + eslint + prettier + stylelint + knip
make quality-fast   # eslint + prettier + markdownlint (fast CI check)
make quality-fix    # eslint-fix + prettier-fix + stylelint-fix + markdownlint-fix
make test           # Vitest (no coverage)
make test-coverage  # Vitest with coverage → build/coverage/
make test-ci        # Vitest strict CI mode
make typecheck      # tsc --noEmit
make build          # tsup dual build → dist/
make bats           # BATS shell tests
make ci             # quality + typecheck + test + bats
make doctor         # environment diagnostics
```

See [js-dev-tools make targets][js-make] for the full reference.

---

## Config cascade

Configs are resolved in priority order:

```text
Priority 1  →  {project_root}/{filename}                  (root override)
Priority 2  →  {project_root}/config/dev-tools/{filename} (published stub)
Priority 3  →  node_modules/@zairakai/js-dev-tools/...   (bundled default)
```

Published stubs in `config/dev-tools/` extend their bundled counterparts:

```javascript
// config/dev-tools/eslint.config.js
import baseConfig from '@zairakai/js-dev-tools/config/eslint.config.js'
export default [...baseConfig]

// config/dev-tools/prettier.config.js
import baseConfig from '@zairakai/js-dev-tools/config/prettier.config.js'
export default { ...baseConfig }
```

To override a rule project-wide, edit the published stub in `config/dev-tools/`. To override for the entire project without going through the cascade, place a config at the root.

---

## Git hooks

Installed to `.githooks/` via `setup-project.sh --publish=hooks`:

| Hook | Trigger | Action |
| :--- | :--- | :--- |
| `commit-msg` | Every commit | Validates Conventional Commits format + ticket ID |
| `prepare-commit-msg` | Before editor opens | Prepends ticket ID from branch name |
| `pre-commit` | Before commit | `make quality-fast` |
| `pre-push` | Before push | `make quality` |

```bash
git commit --no-verify   # bypass pre-commit (justified cases only)
git push --no-verify     # bypass pre-push (justified cases only)
```

---

## GitLab CI

```yaml
include:
  - project: "zairakai/npm-packages/js-dev-tools"
    ref: v1.0.0
    file: ".gitlab/ci/pipeline-js-package.yml"

variables:
  CACHE_KEY: "{{PACKAGE_SLUG}}-v1"
  NPM_PACKAGE_NAME: "@zairakai/{{PACKAGE_SLUG}}"
```

### Pipeline stages

| Stage | What runs |
| :--- | :--- |
| `lint` | `make quality-fast` (ESLint + Prettier + Markdownlint) |
| `test` | `make test-ci` (Vitest strict + coverage) |
| `typecheck` | `make typecheck` |
| `build` | `make build` (tsup) |
| `publish` | `npm publish` — triggered on git tag only |

### Versioning in CI

On tag push (`v*`), the pipeline reads the tag, sets `npm version` to the tag value, then publishes. The `0.0.0` in `package.json` is never used in production.

### Updating the pipeline ref

When `js-dev-tools` releases a new version, update `ref:` in `.gitlab-ci.yml`:

```bash
# After npm update @zairakai/js-dev-tools, update .gitlab-ci.yml ref manually
# or let the devtools CI sync script handle it
```

---

**[Back to npm-package][pkg]**

[handbook]: ../../README.md
[templates]: ../README.md
[pkg]: ./README.md
[js-make]: ../../packages/js-dev-tools/make.md
