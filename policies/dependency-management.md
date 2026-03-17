# Dependency Management

> **[Handbook][handbook]** › **[Policies][policies]** › Dependency Management

Keeping dependencies up-to-date reduces security vulnerabilities, ensures compatibility, and benefits from upstream bug fixes. This document covers the standard process for all Zairakai projects.

---

## PHP Dependencies (Composer)

### Check for outdated packages

```bash
make outdated
# or directly:
composer outdated --direct
```

### Update all dependencies

```bash
composer update
```

### Update a specific package

```bash
composer update zairakai/laravel-dev-tools
```

### Security audit

```bash
composer audit
# or via Make:
make security-audit
```

> The CI pipeline runs `composer audit` automatically — any known vulnerability blocks the merge.

---

## JavaScript Dependencies (NPM / Yarn)

### Check for outdated packages

```bash
npm outdated
# or:
yarn outdated
```

### Interactive upgrade (recommended)

```bash
npx npm-check-updates -i
# or with yarn:
yarn upgrade-interactive --latest
```

The interactive mode lets you select which packages to upgrade, preventing accidental major version jumps.

### Update all dependencies

```bash
npm update
# or:
yarn upgrade
```

### Security audit

```bash
npm audit
# or:
yarn audit
```

---

## Strategy

### Patch and minor updates

Apply regularly — at least once per sprint. These are low-risk and keep the project current.

### Major updates

- Always read the **CHANGELOG** / migration guide before upgrading
- Create a dedicated branch: `chore/#TICKET-upgrade-package-name`
- Run the full quality gate after upgrading: `make quality && make test-all`
- Update the baseline if PHPStan errors appear (only pre-existing ones)

### `dev-tools` packages

Both `zairakai/laravel-dev-tools` and `@zairakai/js-dev-tools` auto-update their distributed configs (`.gitlab-ci.yml` ref, stubs) on `composer update` / `npm update`. No manual sync needed.

---

## Make Commands Reference

```bash
# PHP
make outdated        # List outdated Composer dependencies
make security-audit  # Run composer audit

# JS (from @zairakai/js-dev-tools)
make outdated        # List outdated npm/yarn dependencies
```

---

**[Back to Policies][policies]**

[handbook]: ../README.md
[policies]: ./README.md
