# Setup — js-dev-tools

> **[Handbook][handbook]** › **[Packages][packages]** › **[js-dev-tools][pkg]** › Setup

---

## setup-project.sh

Central setup script. Runs automatically via `postinstall` on every `npm install`.

```bash
# Normal setup (run automatically by npm install)
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh

# Publish specific configs to config/dev-tools/
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh --publish
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh --publish=quality
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh --publish=style

# Also generate/inject Makefile
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh --with-makefile

# Force overwrite (backs up existing files first)
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh --force

# Silent mode (used by postinstall — errors only)
bash node_modules/@zairakai/js-dev-tools/scripts/setup-project.sh --silent
```

---

## Publish targets

`--publish` deploys configs to `config/dev-tools/` so they can be customized per project. Published files are hash-protected — they will not be overwritten on subsequent runs unless you use `--force`.

| Key | Source | Destination |
| :--- | :--- | :--- |
| `eslint` | `stubs/quality/eslint.config.js.stub` | `config/dev-tools/eslint.config.js` |
| `knip` | `stubs/quality/knip.config.js.stub` | `config/dev-tools/knip.config.js` |
| `prettier` | `stubs/style/prettier.config.js.stub` | `config/dev-tools/prettier.config.js` |
| `stylelint` | `stubs/style/stylelint.config.js.stub` | `config/dev-tools/stylelint.config.js` |
| `prettierignore` | `config/.prettierignore` | `config/dev-tools/.prettierignore` |
| `stylelintignore` | `config/.stylelintignore` | `config/dev-tools/.stylelintignore` |
| `markdownlint` | `config/.markdownlint.json` | `config/dev-tools/.markdownlint.json` + `.markdownlint.json` (root) |
| `markdownlintignore` | `config/.markdownlintignore` | `config/dev-tools/.markdownlintignore` |
| `vitest` | `stubs/testing/vitest.config.js.stub` | `config/dev-tools/vitest.config.js` |
| `tsconfig` | `stubs/typescript/tsconfig.json.stub` | `tsconfig.json` |
| `hooks` | `stubs/githooks/` | `.githooks/` |
| `gitlab-ci` | `stubs/gitlab-ci/*.stub` | `.gitlab-ci.yml` (auto-detects project type) |
| `governance` | `stubs/governance/*.stub` | `SECURITY.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md` |

### Publish groups

| Group | Includes |
| :--- | :--- |
| `quality` | eslint, knip |
| `style` | prettier, stylelint, prettierignore, stylelintignore, markdownlint, markdownlintignore |
| `testing` | vitest |
| `typescript` | tsconfig |
| `hooks` | hooks only |
| `governance` | governance only |
| `all` | quality + style + testing + typescript + hooks (gitlab-ci and governance require explicit opt-in) |

---

## Config cascade

All scripts resolve config files using `resolve_config()` from `scripts/config.sh`:

```text
Priority 1  →  {project_root}/{filename}                 (user root override)
Priority 2  →  {project_root}/config/dev-tools/{filename} (published, customizable)
Priority 3  →  node_modules/@zairakai/js-dev-tools/...   (bundled default)
```

This means a root `eslint.config.js` overrides the published config, which overrides the bundled default.

---

## Makefile injection

`--with-makefile` generates or injects the `core.mk` include into the project `Makefile`.

For full-stack projects (PHP + JS), Makefile integration is handled by `laravel-dev-tools`'s `fullstack.mk`, which auto-detects `@zairakai/js-dev-tools` presence.

---

## postinstall hook

The `postinstall` script in `package.json` runs automatically:

```json
"postinstall": "bash ./scripts/setup-project.sh --silent || true"
```

The `|| true` ensures that `npm install` never fails due to a setup error (e.g. missing write permissions). The `--silent` flag suppresses output — errors still appear.

---

## Git hooks

Installed to `.githooks/` (versioned) and activated via `git config core.hooksPath .githooks`.

| Hook | Trigger | Action |
| :--- | :--- | :--- |
| `commit-msg` | Every commit | Validates Conventional Commits format + ticket ID |
| `prepare-commit-msg` | Before editor opens | Prepends ticket ID from branch name (if detectable) |
| `pre-commit` | Before commit | Runs `make quality-fast` (ESLint + Prettier + Markdownlint) |
| `pre-push` | Before push | Runs `make quality` (full gate) |

To skip a hook when needed:

```bash
git commit --no-verify    # skip pre-commit
git push --no-verify      # skip pre-push
```

> Bypass only when justified. Never skip in CI.

---

**[Back to js-dev-tools][pkg]**

[handbook]: ../../README.md
[packages]: ../README.md
[pkg]: ./README.md
